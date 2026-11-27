# VSCode 里用上 Claude 和 GPT:插件全方案

> 原文地址：[https://claucc.net/articles/vscode-ai-plugins](https://claucc.net/articles/vscode-ai-plugins)　·　更多教程见 [claucc.net](https://claucc.net)

把 Claude 和 GPT 直接接进 VSCode,边写代码边问、让 AI 直接改文件,比来回切网页高效得多。方案不少,容易挑花眼。本文把当前五大主流方案讲清:各能接哪些模型、扩展叫什么、用官方订阅还是自带 API Key(BYOK),最后给一张"按需求选"的速记。

## 先有个全局:两种付费模式

- **官方订阅制**:用 Claude / ChatGPT / Copilot 的订阅账号登录,额度由订阅提供,开箱即用——**Claude Code 扩展、Codex 扩展、GitHub Copilot** 属于这类。
- **自带 API Key(BYOK)**:自己去各家拿 API Key 填进插件,按量付费,能在一个插件里随便切 Claude / GPT / 国产模型——**Cline、Continue** 属于这类。

> [!TIP]
> 不论哪种,**登录账号、调用 API 都要走稳定的美国住宅 IP**。数据中心 IP / 频繁跳变的节点容易触发风控、反复人机验证甚至封号。先把节点挂好,再装插件。详见 [为什么住宅 IP 至关重要](residential-ip.md)。

## 五大方案速选表

| 方案 | 能接模型 | 付费方式 | 扩展 ID |
| --- | --- | --- | --- |
| Claude Code(官方) | Claude | Claude 订阅 / API Key | `anthropic.claude-code` |
| Codex(OpenAI 官方) | GPT 系列 | ChatGPT 登录 / API Key | `openai.chatgpt` |
| GitHub Copilot | Claude + GPT | Copilot 订阅 | `GitHub.copilot` |
| Cline | Claude / GPT / 任意 | 自带 API Key | `saoudrizwan.claude-dev` |
| Continue | Claude / GPT / 任意 | 自带 API Key | `Continue.continue` |

*表:VSCode 主流 AI 编程插件对比(2026)。下面逐个讲安装与配置*。

## 方案一 · Claude Code 扩展(官方,用 Claude)

Anthropic 官方扩展,把 Claude Code 嵌进 VSCode 侧边栏,适合已经在用 Claude Pro / Max 的人。

#### 1. 安装

VSCode 扩展市场搜 **Claude Code**(发布者 Anthropic,ID `anthropic.claude-code`)安装。本机建议先装好 Claude Code CLI。

#### 2. 登录

侧边栏点开 Claude → **用 Claude 账号登录(Pro / Max 订阅)**,或填 Anthropic API Key(BYOK)。登录这一步务必挂好美国住宅 IP。

#### 3. 使用

在侧边栏对话,让它读工程、改文件、跑命令。想让 Claude Code 改用第三方 / 国产模型,可配合 [cc-switch](cc-switch.md) 一键切换供应商。

## 方案二 · Codex 扩展(OpenAI 官方,用 GPT)

#### 1. 安装

扩展市场搜 **Codex**(发布者 OpenAI,ID `openai.chatgpt`)安装。它和 Codex CLI 共享同一套配置。

#### 2. 登录

用 **ChatGPT 账号登录**(Plus / Pro / Business 含额度),或填 OpenAI API Key。ChatGPT 登录、Codex 调用同样要稳定美国 IP,否则容易反复验证。

#### 3. 使用

在面板里描述需求,Codex 自动读写工程文件。更多见 [Codex 使用方法与充值](chatgpt-register.md)。

## 方案三 · GitHub Copilot(一个订阅,Claude / GPT 都能选)

补全 + 聊天一体,模型可在 Claude 和 GPT 之间切,适合不想分别养两个订阅的人。

#### 1. 安装

装 **GitHub Copilot**(`GitHub.copilot`)和 **Copilot Chat**(`GitHub.copilot-chat`),用 GitHub 账号登录并开通 Copilot 订阅。

#### 2. 切换模型

聊天框右下角的**模型下拉**里选 Claude(Sonnet / Opus)或 GPT;补全模型走命令面板 `GitHub Copilot: Change Completions Model`。

> [!TIP]
> **注意**:自 2026 年起,Copilot 的 **Free / 学生版取消了手动选模型**,只能用 Auto 自动选;要手选 Claude / GPT 模型需付费版(Pro 及以上)。

## 方案四 · Cline(开源,自带 Key,Claude/GPT 随便接)

开源自治 Agent,能读写整个工程、跑命令。模型无关——填谁的 Key 就用谁,Claude、GPT、国产兼容端点、本地模型都行。

#### 1. 安装

扩展市场搜 **Cline**(ID `saoudrizwan.claude-dev`,原名 Claude Dev)安装。

#### 2. 配 Provider + Key

点扩展设置,**API Provider** 选 Anthropic / OpenAI / OpenRouter / 兼容端点,填对应 **API Key** 和 **模型**。想同时备 Claude 和 GPT,配两个 Provider 随时切。

#### 3. 使用

侧边栏描述任务,Cline 会规划步骤、改文件、执行命令(每步可人工确认)。Roo Code(`RooVeterinaryInc.roo-cline`)、Kilo Code(`kilocode.kilo-code`)是同类分支,用法基本一致。

## 方案五 · Continue(开源,自带 Key)

#### 1. 安装

扩展市场搜 **Continue**(ID `Continue.continue`)安装。

#### 2. 配置模型

在 Continue 的配置文件里按 provider 填 **API Key + 模型**(可同时配 Claude、GPT、本地模型),在聊天框顶部切换。补全、聊天、编辑三种用法分开配。

## 到底选哪个

| 项目 | 说明 |
| --- | --- |
| 已有 Claude 订阅、只用 Claude | Claude Code 扩展 |
| 已有 ChatGPT 订阅、只用 GPT | Codex 扩展 |
| 补全+聊天一体、订阅制省心 | GitHub Copilot(付费版可选模型) |
| 想一个插件随便切两家、自带 Key | Cline / Roo Code / Continue |
| 想用国产模型省钱 | Cline(填兼容端点)或 [cc-switch](cc-switch.md) |

*图:按需求选方案速记*

> [!TIP]
> 无论用哪个,登录与调用前先确认出口是干净的美国住宅 IP、无 DNS / WebRTC 泄露。检测方法见 [IP 质量检测](ip-quality-check.md) 与 [DNS / WebRTC 泄露检测](dns-leak-check.md)。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/vscode-ai-plugins) 为准。
