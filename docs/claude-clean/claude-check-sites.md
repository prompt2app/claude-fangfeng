# 网络环境检测地址汇总（附代理配置要点）

> 原文地址：[https://claucc.net/articles/claude-check-sites](https://claucc.net/articles/claude-check-sites)　·　更多教程见 [claucc.net](https://claucc.net)

用 Claude 之前打开这几个检测站过一遍。先把代理客户端按下面的要点设好，再逐个检测。

## 先设好代理客户端

| 项目 | 说明 |
| --- | --- |
| 虚拟网卡（TUN 模式） | 开启 |
| 系统代理 | 开启 |
| DNS 覆写 | 不用开 |

> [!WARNING]
> **TUN 和系统代理要同时开**。只开 TUN、不开系统代理，DNS 会泄露。

## 检测地址

[Claude 专项检测（最重要）

ip.net.coffee/claude

AI 信任评分、三个出口 IP 是否一致、DNS 和 WebRTC 泄露、时区](https://ip.net.coffee/claude/)
[网络综合体检

ip.net.coffee

IP、DNS、WebRTC、延迟等综合检测入口](https://ip.net.coffee/)
[IP 类型与风控值

ping0.cc

是不是家庭宽带、ASN 运营商、风控值、共享人数](https://ping0.cc)
[纯净度与泄露

ippure.com

IP 属性、纯净度系数、WebRTC / DNS 泄露](https://ippure.com)
[国内 IP 查询

my.ip.cn

国内站点看到的 IP，和上面的海外检测结果对照看](https://my.ip.cn)

## 合格标准

1. **AI 信任评分 90 分以上**，IP 属性是住宅（家庭宽带），不能是机房。
2. **ip.net.coffee/claude 第一行三个出口 IP 一样**。
3. **DNS 泄露、WebRTC 泄露结果里不能出现中国**。

每一项怎么看、不合格怎么处理，见[使用 Claude 前的电脑检测](claude-pc-checkup.md)。更多检测工具在 [IP 检测工具合集](https://claucc.net/iptools)。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/claude-check-sites) 为准。
