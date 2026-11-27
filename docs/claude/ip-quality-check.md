# 注册前环境自检:IP 质量与泄露检测

> 原文地址：[https://claucc.net/articles/ip-quality-check](https://claucc.net/articles/ip-quality-check)　·　更多教程见 [claucc.net](https://claucc.net)

拿到住宅 IP、注册账号之前,花 3 分钟把环境过一遍。全绿再注册,能挡掉绝大多数刚注册就被拒的情况。

## 3 分钟自检清单

#### 1. 出口是美国

打开 `api.ipify.org`,确认看到的是美国 IP,不是国内或其他地区。

#### 2. IP 类型是住宅、风险分低

打开 `ippure.com`:IP 类型为住宅(Residential),风险分绿色(0–25),没有被标记为代理 / 数据中心。判读方法见下一节。

#### 3. 无 DNS 泄露

确认 DNS 出口也在境外、不漏国内,见 [DNS / WebRTC 泄露检测](dns-leak-check.md)。

#### 4. 无 WebRTC / IPv6 泄露

检测页里的 WebRTC、IPv6 不应暴露你的真实 IP。

#### 5. 时区与 IP 一致

系统时区设成 IP 所在地(美区),语言不用改。见 [改时区的具体步骤](claude-clean-fingerprint-reregister.md)。

| 项目 | 说明 |
| --- | --- |
| 出口 IP | 美国 (US) |
| IP 类型 | Residential 住宅 ✓ |
| 风险分 Fraud Score | 0–25　绿色 ✓ |
| DNS 泄露 | 无 ✓ |
| WebRTC 泄露 | 无 ✓ |
| 时区 | 美区一致 ✓ |

*图:全绿示例——任意一项不对,就先修好再注册*

> [!CAUTION]
> 任意一项不对,先修好再注册——**注册时被记一次,后面很难洗白**。

## 怎么判读:ippure.com 一站检测

开启代理后，浏览器打开 <https://ippure.com/>，重点看三项：

| 项目 | 合格标准 |
| --- | --- |
| IP 类型 / Type | **Residential（住宅）**，而不是 Hosting / Datacenter。显示 Residential 还不够,再查一下 ASN 是不是真的家庭宽带运营商——见 [伪住宅 IP:Comcast 与 NTT / GTT 的区别](fake-residential-ip.md) |
| 风险分 / Fraud Score | **绿色、0–25 之间越低越好** |
| 地理位置 | 美国，与你期望的州 / 城市大致一致 |
| DNS / WebRTC | 同为美国，无泄露（详见 [DNS 泄露检测](dns-leak-check.md)） |

> [!TIP]
> 类型显示 Residential + 风险分绿色 + 无 DNS/WebRTC 泄露 = 这个 IP 适合长期挂 AI 账号。

| 项目 | 说明 |
| --- | --- |
| IP 类型 (Type) | Residential 住宅 ✓ |
| 风险分 (Fraud Score) | 12 / 100　绿色 ✓ |
| 位置 | United States · New York |
| DNS / WebRTC | 无泄露 ✓ |

*图:ippure.com 合格结果长这样——全绿即可放心长期使用*

## 辅助工具

- `https://api.ipify.org` —— 最简单，只回显当前出口 IP，确认代理是否在工作。
- **whoer.net** —— 综合匿名度评分，90% 以上为佳。
- **ipleak.net** —— 重点看 WebRTC 是否暴露真实 IP。
- **dnsleaktest.com** —— 看 DNS 服务器是否为 Cloudflare / Google，而非中国 ISP。

## 命令行快速确认出口

```
curl https://api.ipify.org
# 返回的应是美国住宅 IP，而不是你本地的国内 IP
```

## 怎么判断「是不是代理坏了」

1. 能看到节点出口 IP = 代理在工作。
2. 能开 google.com、只有某个站打不开 = 该站针对性问题（账号区 / 风控），不是代理。
3. 检测到 IPv6 = 有泄露，关闭 IPv6。
4. 同设备换个节点 / 端口能用 = 原出口问题；换哪个都不行 = 设备或本地网络问题。

## 建议的检测节奏

- **每月**做一次 IP 质量 + DNS 检测（ippure.com，风险分保持绿色）。
- **每周**快速看一次 DNS / WebRTC 是否泄露。
- 账号一旦频繁要求验证，先查 IP 风险分有没有变高。

> [!WARNING]
> 风险分变高、类型变成 Datacenter，说明该 IP 出问题了，换一条 IP，不要硬用。

## 某项不对,怎么处理

| 问题 | 怎么处理 |
| --- | --- |
| IP 不是美国 / 不是住宅 | 确认客户端选的是住宅节点;仍不对就换一条 IP。 |
| 风险分偏高(黄 / 红) | 该 IP 段偏「脏」,换一个干净 IP 段。 |
| 有 DNS / WebRTC 泄露 | 按 [泄露检测](dns-leak-check.md) 关掉 IPv6、封 STUN、用境外 DNS。 |
| 时区 / 语言不符 | 系统时区改成美区,见 [改时区的具体步骤](claude-clean-fingerprint-reregister.md)。 |

> [!TIP]
> 全绿之后,再去 [注册并用起 Claude](claude-usage.md) 或 [注册 ChatGPT](chatgpt-register.md)。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/ip-quality-check) 为准。
