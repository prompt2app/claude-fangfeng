# 用 cc-switch 让 Claude Code 切换其他厂家模型

> 原文地址：[https://claucc.net/articles/cc-switch](https://claucc.net/articles/cc-switch)　·　更多教程见 [claucc.net](https://claucc.net)

Claude Code 默认连 Anthropic 官方,但它认的是 `ANTHROPIC_BASE_URL` + `ANTHROPIC_AUTH_TOKEN` 这套环境,任何"Anthropic 兼容"的端点都能接——于是可以让 Claude Code 改用第三方中转,或国产模型(智谱 GLM、Kimi、DeepSeek)。手动改配置文件麻烦又容易错,**cc-switch** 就是把这件事变成"点一下切换"的小工具。本文讲清它怎么装、怎么用。

## cc-switch 是什么

- **一个跨平台桌面应用(GUI)**:统一管理多个 AI 编程工具的供应商配置,免去手动编辑各家格式不一的配置文件。除 Claude Code 外还支持 Codex、Gemini CLI 等多种工具。
- **内置 50+ 供应商预设**:官方(Anthropic 等)、各类第三方中转、国产模型(GLM / Kimi / DeepSeek),选一下就自动填好端点,也能手动建自定义配置。
- **一键切换 + 热生效**:对 Claude Code **切换供应商无需重启**(其他工具一般要重启对应 CLI)。

> [!TIP]
> 用国产 / 第三方模型主要图**省钱或免梯子**;但写复杂代码的能力仍以 Claude 官方 Opus / Sonnet 最强。建议:**官方账号 + 国产中转两套配置都加好,按任务一键切**——日常杂活用便宜的,硬骨头切回官方。官方账号的稳定登录仍建议走美国住宅 IP。

## 它实际改了什么

切换时,cc-switch 把选中的配置**写进各工具真实的配置文件**。对 Claude Code,就是写 `~/.claude/settings.json` 里 `env` 段的 `ANTHROPIC_BASE_URL` / `ANTHROPIC_AUTH_TOKEN` 等。它自己的数据放在 `~/.cc-switch/`(含 SQLite 库与最近若干次自动备份)。所以即使不装它,你手动改 `settings.json` 也能达到同样效果——cc-switch 只是把这件事做成了图形化、可一键来回切。

## 第一步 · 安装

它是原生桌面应用(Tauri),**不是 npm 包**,按系统下载安装即可:

| 项目 | 说明 |
| --- | --- |
| macOS | brew install --cask cc-switch  或 .dmg |
| Windows | .msi 安装包 / 便携版 .zip |
| Linux | .deb / .rpm / .AppImage |

*图:各平台安装方式。源与发布页见 GitHub `farion1231/cc-switch`。*

## 第二步 · 添加一个供应商配置

#### 1. 新建

主界面右上角点 **+** 打开添加面板。可以从**预设下拉**选一个供应商(端点自动填好),也可手动建自定义。

#### 2. 填字段(以 Claude Code 为例)

需要:**名称**(自己识别用)、**Base URL**(端点)、**API Key**(该供应商的密钥)、**模型**。填好 Key 和端点后,可点**「获取模型」**自动拉取该供应商的可用模型列表。

#### 3. 保存

点添加提交。重复以上,把**官方**和**各家中转**都建成独立配置,后面随时切。

| 项目 | 说明 |
| --- | --- |
| 名称 | GLM-备用 |
| Base URL | https://open.bigmodel.cn/api/anthropic |
| API Key | 你的 GLM 密钥 |
| 模型 | glm-4.6 |

*图:添加一个"智谱 GLM"供应商的字段示意*

## 第三步 · 切换

主界面**选中目标供应商 → 点启用**,或从系统托盘快捷切换。Claude Code 是热切换,**当前终端立即生效、无需重启**;Codex / Gemini CLI 等需要重开终端或重启 CLI。验证是否切成功:在 Claude Code 里发一句话,看是否由新模型回答即可。

## 常用「Anthropic 兼容」国产端点

这几家都提供 Anthropic 兼容入口,填法统一:**Base URL 只填到 `…/anthropic` 一级**(`/v1/messages` 由客户端自动补),Key 用各家自己的 API Key。

| 厂家 | Base URL | 模型(示例) |
| --- | --- | --- |
| 智谱 GLM | `https://open.bigmodel.cn/api/anthropic` | glm-4.6 |
| Kimi / 月之暗面 | `https://api.moonshot.cn/anthropic` | kimi 系列 |
| DeepSeek | `https://api.deepseek.com/anthropic` | 自动映射 opus/sonnet |

*表:常见国产 Anthropic 兼容端点(具体模型名以各家当日官方文档为准)*。

> [!TIP]
> DeepSeek 会把 `claude-opus*` 自动映射到它的高配模型、`claude-sonnet*/haiku*` 映射到快配,所以模型名按 Claude 的写也能用。GLM / Kimi 直接写它们自己的模型名。

## 几个坑

- **切换会覆盖整段配置**:装在 `settings.json` 里的插件等通用数据,切换后可能丢。先用 cc-switch 的"共享 / 通用配置"把公共部分提取出来,再切换。
- **别把配置删光**:系统始终需要一个激活配置,删空会让 Claude Code 无可用供应商而报错。
- **切回官方登录**:先加官方预设 → 重启 Claude Code → 重新走登录 / OAuth。
- **第三方中转的 Key 要谨慎**:选可信渠道,Key 泄露等于额度被盗;国产模型质量、合规、隐私自行评估,敏感工程慎用。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/cc-switch) 为准。
