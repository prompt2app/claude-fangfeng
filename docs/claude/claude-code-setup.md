# Claude Code 安装与联网配置

> 原文地址：[https://claucc.net/articles/claude-code-setup](https://claucc.net/articles/claude-code-setup)　·　更多教程见 [claucc.net](https://claucc.net)

Claude Code 已经不只是命令行了——桌面版、网页版、IDE 扩展都能用。这篇讲清三种用法各自怎么装、要放行哪些域名、**为什么它不认 socks5**,以及连不上时按什么顺序自查。

> [!CAUTION]
> **先确认一件事:Claude Code 需要付费账号**。免费版 claude.ai 不含 Claude Code,必须是 Pro / Max(或 Team / Enterprise / Console API 账号)。没订阅的先看 [Claude 注册到使用全流程](claude-usage.md)。

## 先选:命令行、桌面版,还是网页版

三种用法背后是同一个 Claude Code,选哪个只影响你怎么跟它打交道、以及网络怎么配。

| 用法 | 适合谁 | 网络怎么走 |
| --- | --- | --- |
| **桌面版**(App) | 怕折腾命令行的人。图形界面,装完就能用。 | **一般走系统代理**,clash 开着多数能通;但内置的 Claude Code 会话不一定跟随,**稳妥起见也开 TUN** |
| **命令行**(CLI) | 习惯终端、要接 MCP / Skills 的人 | **不走系统代理**,要开 TUN 或设 `HTTPS_PROXY` |
| **网页版** | 临时用一下,不想在本机装东西 | 走浏览器,即系统代理 |
| **IDE 扩展** | VSCode / JetBrains 里边写边问 | 跟随 IDE,一般同系统代理 |

> [!TIP]
> 被网络问题卡住的十有八九是**命令行**用户。如果你只是想用上 Claude Code、不折腾插件,**直接装桌面版最省事**——不用碰终端;连不上就把 clash 的 TUN(虚拟网卡)打开,一次解决。

## 装:命令行版

官方现在推荐**原生安装器**,不需要 Node.js,装完会自己后台更新。

```bash
# macOS / Linux / WSL
curl -fsSL https://claude.ai/install.sh | bash

# Windows PowerShell
irm https://claude.ai/install.ps1 | iex

# 装完在项目目录里启动
claude
```

也可以用包管理器装,但这些方式**不会自动更新**,得自己定期升级:

```bash
# macOS Homebrew
brew install --cask claude-code

# Windows WinGet
winget install Anthropic.ClaudeCode

# npm(现在要 Node 22+,不推荐)
npm install -g @anthropic-ai/claude-code
```

> [!WARNING]
> 以前流传的 `npm install -g @anthropic-ai/claude-code` 现在**要求 Node 22 以上**,低版本会报 `EBADENGINE`。新装建议直接用上面的原生安装器,绕开 Node 这件事。

## 装:桌面版

#### 1. 下载

打开 `claude.ai/download`,选你的系统(macOS / Windows / Linux)。

#### 2. 登录

用你的 Claude 账号登录,吃 Pro / Max 额度。**登录这一步要挂住宅 IP**。

#### 3. 打开项目

在 App 里选一个本地文件夹,就能开始让它读代码、改文件。

## 关键:它不支持 socks5 代理

> [!CAUTION]
> Anthropic 官方文档原话:**Claude Code does not support SOCKS proxies**。它只认 `HTTPS_PROXY` / `HTTP_PROXY` 这类 HTTP 代理。

这条经常坑到人:手里有一个 socks5 地址(比如指纹浏览器在用的那个),想着终端里也填上就能用——**填了不会报错,就是连不上**。命令行版要联网,只有两条路:

| 做法 | 怎么配 | 什么时候用 |
| --- | --- | --- |
| **开 TUN(推荐)** | 在 clash / Stash 里打开 TUN(虚拟网卡)开关,终端不用配任何东西 | 绝大多数人。一次打开,所有命令行工具都通 |
| **设 HTTPS\_PROXY** | 见下面的命令,填你客户端的 **HTTP 代理端口**(不是 socks 端口) | 不方便开 TUN、或只想让某个终端走代理 |

```bash
# macOS / Linux:填客户端的 HTTP 端口,常见是 7890
export HTTPS_PROXY=http://127.0.0.1:7890
export HTTP_PROXY=http://127.0.0.1:7890
claude

# Windows PowerShell
$env:HTTPS_PROXY="http://127.0.0.1:7890"
claude
```

注意 `export` 只对当前这个终端窗口有效,关掉就没了。想长期生效写进 `~/.zshrc` 或 `~/.bashrc`。

## 要放行哪些域名

自己配分流规则,或者公司网络有白名单的,照这份清单让它们走住宅 IP / 放行:

| 域名 | 用来做什么 |
| --- | --- |
| `api.anthropic.com` | 模型请求本身,最关键的一条 |
| `claude.ai` | 账号登录认证;原生安装器与自动更新也走它 |
| `claude.com` | 登录时浏览器打开的页面 |
| `platform.claude.com` | 令牌交换与刷新,两种登录方式都要 |
| `mcp-proxy.anthropic.com` | 从 claude.ai 带过来的 MCP 连接器 |
| `downloads.claude.ai` | 插件下载、安装器与版本检查 |
| `registry.npmjs.org` | 装插件、跑 `npx` 型 MCP 服务 |
| `statsig.com` / `statsigapi.net` | 功能开关 |

> [!TIP]
> 按后缀写规则的话,`anthropic.com` + `claude.ai` + `claude.com` 三条就覆盖了前六项。**登录和模型请求这两类必须走住宅 IP**——它们带着你的账号身份。

## 装完先自查这三条

#### 1. 装没装上

终端跑 `claude --version`,应该打印一个版本号。报 `command not found` 就是没装上或没进 PATH。

#### 2. 配置对不对

跑 `claude doctor`,它会只读地检查安装状态、配置文件语法、更新情况,并给出修复建议——**不会启动会话**,可以放心跑。

#### 3. 代理生效没有

进会话后输入 `/status`,看 **Proxy** 那一行是不是你设的地址。值填错(比如漏了 `http://`)它会标成 invalid。

## 连不上时按这个顺序查

| 现象 | 先查什么 |
| --- | --- |
| 一直转圈 / 超时 | TUN 开了没?或者 `HTTPS_PROXY` 填的是不是 **HTTP 端口**(填成 socks 端口一定不通)。 |
| 登录跳转打不开 | 浏览器能不能开 `claude.ai`。登录走的是浏览器,浏览器不通命令行也白搭。 |
| 说没有权限 / 要订阅 | 你的账号是不是 Pro / Max。免费版不含 Claude Code。 |
| 装插件 / MCP 失败 | `registry.npmjs.org` 通不通。 |
| 能连但老是断 | 节点稳不稳。跨境长连接被中间设备掐是常见原因,[找我](https://claucc.net/contact)换个线路试试。 |

## 顺便:让它别只会聊天

装好之后给它配上 MCP 和 Skills,才算真正用起来——查最新文档、跑浏览器、按规范产出文档都能交给它。见 [推荐 MCP 与 Skills:安装与使用全指南](mcp-skills.md)。

想让它切到国产模型或第三方中转省钱,见 [用 cc-switch 切换其他厂家模型](cc-switch.md)。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/claude-code-setup) 为准。
