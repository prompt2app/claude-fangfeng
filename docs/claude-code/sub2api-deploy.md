# sub2api 搭建教程：自建 Claude Code / Codex API 网关

> 原文地址：[https://claucc.net/articles/sub2api-deploy](https://claucc.net/articles/sub2api-deploy)　·　更多教程见 [claucc.net](https://claucc.net)

sub2api 是一个开源的 AI API 网关：把 Claude、OpenAI、Gemini 等上游账号统一接进来，再发 Key 给自己或团队用，带用量统计、并发限制和管理后台。这篇按实际部署过的流程写：Docker 部署、域名和 HTTPS、上游出口 IP、分组与 Key、接入 Claude Code。

> [!WARNING]
> **先看风险**：sub2api 官方说明里写明，使用它可能违反 Anthropic 等上游服务的使用条款，账号被封、服务中断由使用者自己承担；项目作者也没有授权任何基于它的商业运营。建议只用于自己或团队内部，不要对外转卖额度。

## 它能做什么

- **多账号统一管理**：Claude、OpenAI、Gemini 等上游账号（OAuth 或 API Key）都接进同一个后台。
- **发 Key 给成员**：每人一个 Key，按 Token 统计用量和费用。
- **调度与限流**：粘性会话、按用户 / 按账号的并发限制、请求和 Token 限速。
- **分组路由**：同一个 Key 按模型名自动路由到不同上游（混合分组）。
- **管理后台**：账号、分组、Key、用量记录都在网页里操作。

技术栈是 Go 后端 + Vue 前端，依赖 PostgreSQL 15+ 和 Redis 7+，默认端口 8080。项目地址：[github.com/Wei-Shaw/sub2api](https://github.com/Wei-Shaw/sub2api)（LGPL-3.0）。

## 准备什么

| 项目 | 说明 |
| --- | --- |
| 海外服务器 | 2 核 4G 起步，Linux（amd64 / arm64） |
| Docker | Docker 20.10+、Docker Compose v2+ |
| 域名 | 一个子域名，解析到服务器（可走 Cloudflare 代理） |
| 上游出口 | 独享美国住宅 IP（Claude 账号必须） |
| 上游账号 | 自己的 Claude / OpenAI 等订阅或 API Key |

内存小于 4G 的机器建议加 2G swap，并把数据库和 Redis 的连接池调小。

## 第一步：Docker Compose 部署

官方的一键脚本会生成 `docker-compose.yml` 和 `.env`，再启动应用、PostgreSQL、Redis 三个容器：

```bash
mkdir -p sub2api-deploy && cd sub2api-deploy
curl -sSL https://raw.githubusercontent.com/Wei-Shaw/sub2api/main/deploy/docker-deploy.sh | bash
docker compose up -d
docker compose logs -f sub2api
```

想手动部署，就克隆仓库进 `deploy` 目录，复制 `.env.example` 为 `.env`，填好 `POSTGRES_PASSWORD`，再用 `openssl rand -hex 32` 生成 `JWT_SECRET` 和 `TOTP_ENCRYPTION_KEY`。

> [!TIP]
> **两点要注意**：① 在 `.env` 里把 `BIND_HOST` 设成 `127.0.0.1`，让 8080 只在本机监听，外面统一走 Nginx + HTTPS；② 管理员账号只能在首次打开时的安装向导里创建，提前放了 `config.yaml` 会跳过向导、导致登录不了。

## 第二步：域名、Nginx 和 HTTPS

用 Nginx 反向代理到 `127.0.0.1:8080`，Certbot 申请证书。AI 接口是长时间的流式输出，下面几项一定要配：关闭缓冲、超时放长、保持后端长连接。

```bash
# http 块：允许带下划线的请求头（Codex 的 session_id 需要）
underscores_in_headers on;

map $http_upgrade $conn_upgrade { default upgrade; '' ''; }
upstream sub2api_backend { server 127.0.0.1:8080; keepalive 32; }

server {
    server_name api.example.com;
    client_max_body_size 256m;
    location / {
        proxy_pass http://sub2api_backend;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $conn_upgrade;
        proxy_buffering off;
        proxy_request_buffering off;
        proxy_read_timeout 3600s;
        proxy_send_timeout 3600s;
    }
}
```

```bash
sudo certbot --nginx -d api.example.com
sudo nginx -t && sudo systemctl reload nginx
# 确认自动续期正常
sudo certbot renew --dry-run
```

改 Nginx 用 `reload`，不要 `restart`，正在跑的请求不会断。

## 第三步：给上游账号配住宅出口 IP

这一步决定 Claude 账号能不能长期用。服务器本身是机房 IP，Claude 账号直接从机房 IP 出去，很快就会被风控。做法是在后台给每个上游账号绑定一个**独享美国住宅 IP 的 SOCKS5 出口**：一个账号固定一个出口，不和别人共用。

绑好后先在后台测一次连通性，再看使用记录里请求是否正常返回。为什么必须是住宅 IP、怎么分辨真假住宅，见[为什么住宅 IP 至关重要](../claude/residential-ip.md)和[伪住宅 IP 的区别](../claude/fake-residential-ip.md)。

## 第四步：分组、Key 和模型映射

1. **建分组**：按用途分，比如「Claude 组」「GPT 组」；需要一个 Key 同时用多家模型，就建混合分组，按模型名自动路由。
2. **账号进组**：把上游账号加进对应分组。同一段对话里不要混用不同来源的 Claude，用分组隔开。
3. **发 Key**：给每个成员单独建 Key，绑定分组，设好并发和限额。

## 第五步：接入 Claude Code

Claude Code 的网关地址填**根地址**，不要加 `/v1`。写进 `~/.claude/settings.json`：

```bash
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.example.com",
    "ANTHROPIC_AUTH_TOKEN": "sk-你在后台建的 Key",
    "API_TIMEOUT_MS": "300000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1"
  }
}
```

模型名要和 Key 所在分组能提供的模型对应。实际调了哪个上游，以后台的使用记录为准，不要看模型自己怎么说。

## 日常运维

```bash
docker compose ps                       # 三个容器是否都在
docker compose logs -f --tail 200 sub2api # 看日志
docker compose restart sub2api          # 重启应用
curl http://127.0.0.1:8080/health       # 健康检查
```

- **升级前先备份**：数据在部署目录的 `data/`、`postgres_data/`、`redis_data/`，`.env` 里有密钥，权限保持 600。
- **版本要固定**：镜像用 `latest` 时，一次 `docker compose pull` 就可能升到新版本。生产环境建议锁定一个验证过的版本号，升级前先在测试环境跑一遍。
- **出口流量要盯**：住宅 IP 一般按流量计费，「能连通」不代表额度还够，定期去供应商那里查剩余流量。

> [!TIP]
> **不想自己折腾**？我们提供 sub2api 搭建服务，按这套流程交付一套稳定可用的版本，详情见 [sub2api 搭建服务](https://claucc.net/sub2api-service)。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/sub2api-deploy) 为准。
