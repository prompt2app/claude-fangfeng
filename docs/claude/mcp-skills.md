# Claude Code 推荐 MCP 与 Skills:安装与使用全指南

> 原文地址：[https://claucc.net/articles/mcp-skills](https://claucc.net/articles/mcp-skills)　·　更多教程见 [claucc.net](https://claucc.net)

账号能用只是起点。给 Claude Code 装上对的 MCP 和 Skills,它就从「会聊天」变成「能查最新文档、跑浏览器、操作你的项目、按规范产出文档」的真正生产力工具。

## 一、先分清:MCP vs Skills

|  | MCP | Skills |
| --- | --- | --- |
| 是什么 | 给 AI 接「外部工具/数据源」的协议(Model Context Protocol) | 把「某类任务的标准做法」打包成可复用能力 |
| 解决 | 让 AI 能**做事**:查文档、开浏览器、搜网页、读数据库 | 让 AI **把事做对**:按规范生成 Word/PPT、按流程调试 |
| 形态 | 一个外部服务(命令或 URL) | 一个含 `SKILL.md` 的文件夹 |
| 触发 | AI 需要时调用工具 | 任务匹配时自动加载,也可手动 `/技能名` |

一句话:**MCP 给 AI 长出「手和眼」,Skills 给 AI 一份「操作手册」**。

**Claude Code**（你的 AI 助手） → **MCP**（手和眼查文档/跑浏览器） → **Skills**（操作手册按规范做事）

*MCP 让 AI 能做事,Skills 让 AI 把事做对*

## 二、装好 Claude Code

```bash
# macOS / Linux
curl -fsSL https://claude.ai/install.sh | bash
# Windows PowerShell
irm https://claude.ai/install.ps1 | iex

claude            # 在项目目录里启动
```

- 官方原生安装器,不需要 Node.js,装完会自动后台更新。(仍想用 npm 装的,现在要 Node 22+。)
- **命令行不走系统代理**:要么开 TUN(虚拟网卡),要么给终端设 `HTTPS_PROXY`。**Claude Code 不支持 socks5 代理**,别拿 socks 地址去配。需要放行的完整域名清单与三种客户端的选法,见 [Claude Code 安装与联网配置](claude-code-setup.md)。
- 用你的 Claude 账号登录(吃 Pro / Max 额度),默认使用最新的 Claude 模型。

## 三、怎么装 MCP

两种方式,任选其一:

### 方式 A:命令行添加(推荐)

```bash
# 本地命令型 MCP(stdio)
claude mcp add <名字> -- <启动命令>
# 远程 URL 型 MCP(HTTP/SSE)
claude mcp add --transport http <名字> <https://服务地址>

# 查看 / 删除
claude mcp list
claude mcp remove <名字>
```

### 方式 B:写进项目配置文件

在项目根目录建 `.mcp.json`(团队共享),或写进用户级配置:

```
{
  "mcpServers": {
    "context7": { "command": "npx", "args": ["-y", "@upstash/context7-mcp"] },
    "playwright": { "command": "npx", "args": ["-y", "@playwright/mcp"] }
  }
}
```

启动 Claude Code 后用 `/mcp` 查看已连接的 MCP 与其状态。

> [!WARNING]
> **别一次装太多**:每个 MCP 都会往上下文里塞工具说明,装多了拖慢、还容易选错工具。按当前项目需要启用 2–4 个最实用的即可。带密钥的 MCP(如 GitHub、数据库),密钥写进环境变量,别硬编码进配置。

## 四、实战在用的 MCP(亲测好用)

| MCP | 作用 | 什么时候用 |
| --- | --- | --- |
| **context7** | 实时拉取库 / 框架 / API 的**最新官方文档** | 写 React / Next.js / SDK,避免 AI 用过时 API |
| **playwright** | 驱动真实浏览器:导航、点击、填表、截图、抓页面 | 端到端测试、网页自动化、生成后截图自检 |
| **firecrawl** | 网页搜索 + 抓取 + 批量结构化提取 | 调研、采集竞品、把网页内容喂给 AI |
| 文件系统 / 数据库类 | 读写本地文件、查询 DB | 让 AI 直接操作项目与数据 |

这套组合的价值:**context7 管「查得准」,playwright 管「做得了」,firecrawl 管「找得到」**——查文档、跑浏览器、搜资料全交给 AI,你只把关方向。

## 五、GitHub 上最火的 MCP

除上面几个,社区高星、常被推荐的还有:

- **filesystem** —— 受控地读写指定目录的文件。
- **github** —— 读写仓库、Issue、PR(需 GitHub Token)。
- **sequential-thinking** —— 给复杂任务一个结构化「分步思考」框架。
- **memory** —— 跨会话记忆(知识图谱),让 AI 记住长期事实。
- **fetch** —— 抓取单个网页并转成适合 AI 阅读的格式。
- **brave-search / 搜索类** —— 联网搜索。
- **postgres / sqlite** —— 直接查询数据库。
- **slack / 通讯类** —— 收发消息、读频道。

找 MCP:认准官方仓库 `modelcontextprotocol/servers` 和各服务厂商的官方 MCP,别装来源不明的。

## 六、Skills 怎么用

Skills 是放在 Claude Code `skills/` 目录下的文件夹,每个含一个 `SKILL.md`(描述 + 步骤)。它的描述常驻上下文,任务匹配时 Claude 自动读取全文并执行;也能手动 `/技能名` 触发。

### 安装方式

- **放目录**:把 skill 文件夹放进项目或用户级 `skills/` 目录即可被发现。
- **插件市场**:用 Claude Code 的插件机制安装成套技能。

### 常用 / 高星 Skills

- **文档处理(官方 anthropics/skills)** —— 创建 / 编辑 Word(docx)、Excel(xlsx)、PPT(pptx)、PDF,保留格式、改修订、加批注。做对外文档、报表特别好用。
- **前端设计** —— 按规范产出统一风格、避免「AI 味」的界面。
- **工作流类(如 superpowers 套件)** —— 把头脑风暴、系统化调试、测试驱动开发、代码审查等流程固化下来,让 AI 按章法做事。
- **skill-creator** —— 帮你创建 / 打包自己的 Skill。

> [!TIP]
> 找 Skills:官方仓库 `anthropics/skills`(文档处理那几个最实用)和社区的 `superpowers` 套件最值得先装。

## 七、还有三样值得知道:插件 / 子代理 / Hooks

MCP 和 Skills 之外,Claude Code 还有三块能力,合在一起才是它现在的完整样子。不用一上来全用,但该知道有这些东西:

| 是什么 | 解决什么 | 什么时候用 |
| --- | --- | --- |
| **插件(Plugin)** | 把**斜杠命令 + 子代理 + Hooks + MCP** 打包成一个可安装的整体,从市场一键装,也能随时开关 | 想成套地装能力,不用一个个配 |
| **子代理(Subagent)** | 开一个**独立上下文**的分身去干一件子任务,只把结论带回来,不污染主对话 | 大型代码库里做搜索 / 调研,或想并行推进多件事 |
| **Hooks** | 在特定时机**自动执行**你指定的命令(改完文件自动格式化、结束前自动跑测试、禁止越界写文件) | 想把团队规范变成硬约束,而不是靠提醒 |

### 插件怎么装

```bash
# 在 Claude Code 会话里输入
/plugin
# 浏览市场、安装、启用/停用都在这个界面里
```

装插件要能访问 `registry.npmjs.org` 和 `downloads.claude.ai`——装不上先查这两个域名通不通,见 [Claude Code 安装与联网配置](claude-code-setup.md)。

### Hooks 常见用法

- **改完文件自动格式化** —— 每次编辑后自动跑 prettier / gofmt,省得来回提醒它。
- **结束前自动跑测试** —— 会话收尾时执行测试套件,有问题当场发现。
- **禁止写到仓库外** —— 拦截越界的文件写入,防止误伤系统文件。
- **敏感信息拦截** —— 执行命令前过滤密钥、令牌,避免误打印到日志里。

> [!WARNING]
> **装第三方插件和 Skills 前先看一眼内容**。它们能定义 Hooks(自动执行命令)和 MCP 连接,等于在你机器上跑别人写的脚本。**只装来源可信的**,别看着 star 多就直接装。

## 八、高效使用建议

- 先让它「读懂再动手」:给清楚的目标 + 约束,比一句话指令效果好得多。
- 用 MCP 把「查文档、跑浏览器、搜资料」交给 AI,你只把关方向与验收。
- 大任务拆小步、逐步验证,比一次性大改更稳。
- MCP/Skills 按项目需要启用,别堆满——精而准比多而杂强。

> [!TIP]
> Claude Code 和常用 MCP / Skills 配不好?[加群交流](https://claucc.net/contact);团队要共用,看 [sub2api 搭建服务](https://claucc.net/sub2api-service)。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/mcp-skills) 为准。
