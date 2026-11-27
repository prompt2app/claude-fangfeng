# 首次登录必看：清理残留、改邮箱密码、循序渐进使用

> 原文地址：[https://claucc.net/articles/claude-first-login](https://claucc.net/articles/claude-first-login)　·　更多教程见 [claucc.net](https://claucc.net)

很多号不是注册时被封，而是登录后头几天用得太猛被封。第一次登录按这三步走：先清环境，再改邮箱密码，然后低频起步、逐步加量。

**环境清理**（清掉旧痕迹再登录） → **低频使用**（网页端先用几天） → **逐步加量**（再上 Claude Code）

## ① 先检查设备环境

**这台设备以前出过 Claude 封号，就不要直接登录新账号**。封号记录会跟着设备走，新号登上去很可能被连带。先清理 Claude / Claude Code / Anthropic 相关的缓存、登录状态和本地残留，再登录。

```bash
帮我清理当前电脑所有 Claude / Claude Code / Anthropic 相关缓存、登录状态、Token、配置及本地残留，不要删除项目和个人文件。
```

清理之外，网络环境也要先过一遍检测，步骤和合格标准见[使用 Claude 前的电脑检测](claude-pc-checkup.md)。

## ② 第一时间修改邮箱密码

环境确认没问题、能正常登录之后，**马上把注册邮箱的密码改掉**。账号是别人帮你注册或转交给你的，更要改：邮箱就是 Claude 账号的钥匙，谁掌握邮箱，谁就能登录。

## ③ 循序渐进地用，别一上来就猛调

短时间内大量、高频地调用，会被 Claude 判定为「模型蒸馏」这类异常行为，直接触发封号。新号要像普通用户一样，慢慢把用量提上去：

#### 1. 先用免费版网页端，大约三天

正常聊天、问问题，让账号先有一段「真人在用」的记录。

#### 2. 充值 Pro，网页端再用两天左右

订阅后先别急着开 Claude Code，网页端继续正常用。订阅怎么付款见[苹果礼品卡订阅全流程](../claude/claude-subscription.md)。

#### 3. 再上 Claude Code，少量开始

先做小任务、低频调用，确认稳定后再逐步加大用量。安装见[Claude Code 安装与联网配置](../claude/claude-code-setup.md)。

> [!WARNING]
> **新号最容易出事的几种用法**：注册当天就开 Claude Code 跑大任务；多个终端同时并发调用；一天里长时间不间断地高强度请求。这些都等头几天过去、用量慢慢上来之后再说。

## 一句话流程

**环境清理 → 低频使用 → 逐步增加调用量**。前一周稳住，后面就是正常使用。日常还要守住哪些红线，看[Claude 防封避坑清单](../claude/claude-anti-ban.md)。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/claude-first-login) 为准。
