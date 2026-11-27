# DNS / WebRTC 泄露检测方法

> 原文地址：[https://claucc.net/articles/dns-leak-check](https://claucc.net/articles/dns-leak-check)　·　更多教程见 [claucc.net](https://claucc.net)

即使出口 IP 是美国住宅，如果 DNS 或 WebRTC 泄露了真实信息，照样会暴露你在中国、用的是代理。这是最容易被忽视的破绽。

## 什么是泄露，为什么危险

- **DNS 泄露**：你访问网站时，域名解析请求跑去了国内 DNS（如 223.5.5.5），对方据此推断你的真实位置。
- **WebRTC 泄露**：浏览器的 WebRTC 功能会直接暴露你的真实本机 IP（包括 IPv6），绕过代理。
- **IPv6 泄露**：很多裸节点 / 裸二维码只代理 IPv4，IPv6 流量直接走本地，泄露真实 IP。

## 怎么检测

| 站点 | 看什么 | 合格标准 |
| --- | --- | --- |
| [ippure.com](https://ippure.com/) | IP / DNS / WebRTC 综合 | 全部美国，无泄露 |
| dnsleaktest.com | DNS 服务器 | Cloudflare / Google，无中国 ISP |
| ipleak.net | WebRTC | 与出口一致或无 WebRTC |
| test-ipv6.com | IPv6 | 不应出现你的真实 IPv6 |

| 项目 | 说明 |
| --- | --- |
| 出口 IP | 美国 ✓ |
| DNS 服务器 | Cloudflare (US) ✓ |
| WebRTC | 无泄露 ✓ |
| IPv6 | 未出现真实 IPv6 ✓ |

*图:无泄露时,检测结果应全是这样(全绿)*

## 配置层面如何根治

本方案的配置已经内置以下处理，正常导入订阅即生效：

- **fake-ip 模式**：代理流量的域名由服务端解析，不会把解析请求漏到本地。
- **境外 DoH**：用 1.1.1.1 / 8.8.8.8 over HTTPS 解析，只对国内域名（geosite:cn）走国内 DNS。
- **阻断 WebRTC / STUN**：拦截 STUN 端口与域名，让 WebRTC 拿不到真实 IP。
- **关闭 IPv6**：节点设置 `ipv6: false`，避免 IPv6 绕行。

> [!WARNING]
> 如果检测出泄露，最常见的两个原因：① 客户端没关 IPv6；② 用了不带规则的「裸节点 / 裸二维码」。请在客户端关闭 IPv6，并改用我发的**订阅链接导入**（带完整规则）。

## 各端注意

- **iOS Stash**：无 fake-ip，需开启 sniffer（TLS SNI 嗅探）才能正确处理被污染域名；用我提供的配置已开启。
- **安卓**：部分机型要在 APN 里把网络设为 IPv4 only 才能彻底关 IPv6。
- **Windows**：在系统适配器属性里取消勾选 IPv6 更彻底。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/dns-leak-check) 为准。
