# ChatGPT 与 Codex:注册、订阅、防封

> 原文地址：[https://claucc.net/articles/chatgpt-register](https://claucc.net/articles/chatgpt-register)　·　更多教程见 [claucc.net](https://claucc.net)

OpenAI 对注册 IP 的风控和 Claude 一样严:必须美国住宅 IP,绝不能机房起手。这篇把注册、选档、充值、Codex 上手、以及被卡验证 / 生图断流的处理一次讲完。

## 注册前清单

| 项目 | 说明 |
| --- | --- |
| 出口 IP | 美国住宅 · 风险分绿色 |
| 邮箱 | 没注册过 OpenAI 的 Gmail / Outlook |
| 手机号 | 能收短信即可 |
| 浏览器 | 干净独立的 Chrome Profile |

*图:注册前先把这四样对齐(先做 [环境自检](ip-quality-check.md))*

> [!CAUTION]
> 绝不能用机房 IP(VPS 直连)注册,被标记后即使换住宅 IP 也很难洗白。

## 注册步骤

#### 1. 挂住宅 IP

确认出口是美国住宅 IP、风险分绿色。

#### 2. 打开官网注册

访问 `chatgpt.com` → Sign up → 用邮箱注册 → 收验证邮件验证。

#### 3. 完善资料

填姓名 / 生日,按提示完成手机号短信验证。登录后即可用免费档。

> *示意界面（chatgpt.com）*：
>
> Create your account
>
> Email address
>
> your-name@gmail.com
>
> Continue
>
> 下一步:邮箱验证 → 手机短信验证 → 填姓名生日

*图:chatgpt.com 注册入口(示意)*

## 档位怎么选

ChatGPT 现在是五档。**没有单独的 Codex 订阅**,Codex 跟着你的 ChatGPT 档位走——但**各档能用到的 Codex 不一样**,这点最容易踩坑:

| 档位 | 价格 | Codex 能用到什么 |
| --- | --- | --- |
| **Free** | $0 | 只能做**零星的小任务**,体验性质。 |
| **Go** | $8 / 月 | **轻量编程任务**。注意:不含旧版模型。 |
| **Plus** | $20 / 月 | ★ **完整的 Codex**:网页、**命令行(CLI)**、IDE 扩展、iOS,还有自动代码审查、Slack 集成。 |
| **Pro** | $100 或 $200 / 月 | Codex 用量是 Plus 的 5 倍或 20 倍,另有更快的 Codex 专用模型。 |
| **Business** | $20 / 人 / 月(年付;月付 $25) | 团队共用工作区,含管理后台、SSO。 |

> [!WARNING]
> **想在命令行里用 Codex CLI,至少要 Plus**。Go 档虽然也算「有 Codex」,但只覆盖轻量任务——冲着 Codex CLI 来的别买 Go,会白花钱。

> [!TIP]
> **别一上来就 Pro**。新账号直接买最贵的档,在风控眼里是「太新 + 花钱太猛」的异常组合。先 Go 或 Plus 用几天,顺了再升。

### $8 的 Go 档到底值不值

Go 是免费和 Plus 之间的档,便宜一半多,但砍掉的东西很具体:

| Go 给你什么 | Go 砍掉了什么 |
| --- | --- |
| 比免费档多 **10 倍**的消息 / 上传 / 生图数 | **深度推理模型**——做研究、长文、数据分析的别买 |
| 能一直用最新的快速响应模型 | **旧版模型选择权**,Go 不给选 |
| 更长的记忆和上下文 | ★ **完整的 Codex**,只有轻量任务 |
| Codex 的轻量版 | **无广告**——2026 年 2 月起,美国的免费档和 Go 档已开始展示广告;Plus 及以上仍无广告 |

| 项目 | 说明 |
| --- | --- |
| 日常问答、写点东西,嫌免费档不够 | Go,正好 |
| 主要写代码 / 要 Codex CLI | 直接 Plus,别绕 |
| 做研究、长文、数据分析 | Plus(Go 没有深度推理模型) |
| 整天挂着跑编程任务 | Pro |
| 不确定自己算哪种 | 先 Go 一个月,不够再升 |

*图:按你实际要干的事选,别按价格选*

## 三种开通 / 充值方式

| 项目 | 说明 |
| --- | --- |
| A · 官网信用卡 | 最直接 · 美卡 / Apple Pay |
| B · 美区礼品卡 | 不需要美卡 · 美区 ID + 礼品卡 |
| C · OpenAI API 充值 | 给程序 / API Key 方式用 |

*图:三种方式各自适合谁*

- **A 官网信用卡**:登录 → 左下头像 → Upgrade plan → 选档,美国信用卡 / Apple Pay,账单地址填美国。没有美卡优先走 B(虚拟卡风险分偏低,不推荐)。
- **B 美区礼品卡**:步骤和 Claude 一样,详见 [苹果礼品卡订阅全流程](claude-subscription.md),之后网页 / Codex CLI 登录同一 OpenAI 账号即可。【2026-11 起不稳定】 近期只靠礼品卡余额、不绑付款方式的 Apple ID 常见付款被拒或被要求身份验证(KYC),先小额试,处理办法见 [这一节](claude-subscription.md)。
- **C OpenAI API**:打开 `platform.openai.com` → Billing → 预付费充值美元,按 token 计费。只有用 API Key 方式跑 Codex 才需要这条。

## Codex 怎么用

Codex 是 OpenAI 的 AI 编程 agent,相当于 OpenAI 版的 Claude Code——在命令行或 IDE 里读代码、改文件、跑命令。

```bash
# macOS / Linux 官方安装器
curl -fsSL https://chatgpt.com/codex/install.sh | sh

codex            # 在项目目录启动
```

- **登录二选一**:用 **ChatGPT 账号登录**(吃你的套餐额度,推荐),或填 API Key(按 token 另外付费)。
- 会话里输入 `/status` 查看剩余额度。
- **命令行不走系统代理**:要么开 TUN(虚拟网卡),要么给终端设 `HTTPS_PROXY`。
- Windows / npm / Homebrew 也可以装,官方文档里有对应命令;但原生安装器最省事,不需要 Node.js。

> [!WARNING]
> 网上流传的「Plus / Pro 登录 Codex CLI 送 $5 / $50 免费 API 额度」是早期的推广活动,**官方定价页已经查不到了**,别按这个做预算。

## 让 OpenAI 走住宅 IP

注册、登录、跑 Codex 全程挂住宅 IP,别中途掉线漏机房 IP。分流规则里把这些域名指向住宅节点:

```
openai.com
chatgpt.com
oaistatic.com
oaiusercontent.com
sora.com
```

其中 `oaiusercontent.com` 是图片 / 文件存储域名。如果你发现**能聊天但上传图片一直转圈**,多半是这个域名走了 UDP / QUIC 被丢包——在代理软件里关掉 QUIC(或让 UDP 回落 TCP)就好了。

## 反复人机验证(提示「请再试一次」)

> *示意界面（chatgpt.com）*：
>
> 请再试一次 / Please try again
>
> Arkose 人机验证一直转、过不去

*图:Arkose 验证反复——这是 IP 信誉被拒,不是验证页的问题(示意)*

**根因**:平台对你当前出口 IP 的信任分太低,直接在验证环节卡你。

**怎么办**:换一个干净的住宅 IP 段(同一段反复失败就说明该段被标记),严重时用移动网络(4G)环境过验证。**单纯刷新、改路由没用**——这是 IP 信誉评分的问题,不是配置问题。

## 图片生成 / 长输出断流

- **现象**:对话正常,但生成图片、长输出时卡住或断。
- **根因**:住宅代理网关对「长时间不发字节」的连接有空闲超时,而生图时模型在后台思考几十秒不出字节,连接就被掐了;也可能是域名被 DNS 污染、漏分流。
- **怎么办**:让你的代理服务商把 OpenAI 相关域名单独改走节点直出,绕开住宅网关的空闲超时;拿不准就 [加群问](https://claucc.net/contact)。

## 「降智」:问它是什么模型,回答 mini

**现象**:明明订了 Plus / Pro,用着用着觉得变笨了;问它"你是什么模型",它回答一个 mini 之类的小模型名。

> [!CAUTION]
> **这是账号侧的额度和路由问题,不是网络问题——换 IP 解决不了**。而且「问它是什么模型」本身就不可信,模型并不可靠地知道自己是谁。要确认用的是什么模型,**看界面上的模型选择器**。

三个真实原因:**①用量到顶被自动切到 mini**(最常见,官方设计)、**②自动路由把你的问题判轻了**、**③成品号被多人共用、额度被别人花光了**。逐个的判断方法、为什么"换个 IP 就好了"是错觉、以及怎么少遇到降智,见专文:[ChatGPT「降智」:为什么变笨、为什么回答 mini](chatgpt-downgrade-mini.md)。

## 降低风控的几个习惯

#### 1. 全程住宅 IP

注册、登录、使用都用美国住宅 IP,绝不机房起手。

#### 2. 固定不乱切

固定 IP、固定设备,别频繁切换、别多地同时登录同一账号。

#### 3. 循序渐进

第一次别直接上 Pro,先 Go / Plus 用几天。

## 真被封了怎么办

通过官方支持渠道申诉,如实说明;申诉前先把环境修干净避免再次触发。严重违规导致的封禁恢复概率有限,**预防永远优先**。

> [!TIP]
> 场景背景见 [用 ChatGPT 为什么要用住宅 IP](chatgpt.md);想在 VSCode 里用见 [VSCode AI 插件全方案](vscode-ai-plugins.md)。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/chatgpt-register) 为准。
