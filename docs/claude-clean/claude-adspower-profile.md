# 用 AdsPower 给 Claude 建一个专属浏览器环境

> 原文地址：[https://claucc.net/articles/claude-adspower-profile](https://claucc.net/articles/claude-adspower-profile)　·　更多教程见 [claucc.net](https://claucc.net)

光有干净的 IP 还不够，Claude 还会看浏览器环境。用指纹浏览器 AdsPower 给 Claude 账号单独建一个固定、干净的环境，网页端只在这里登录。

## 为什么还要指纹浏览器

很多人被封号、被反复验证，不是 IP 有问题，而是**浏览器指纹暴露了真实环境**：Canvas、WebGL、时区、语言这些特征，在日常浏览器里和你的其他账号、其他网站记录都连在一起。

指纹浏览器相当于在电脑上开了一台「虚拟电脑」：有自己的指纹、Cookie 和代理设置，和真实浏览器完全隔离，而且**每次打开都一样**。对 Claude 来说，你就是一个一直从同一台电脑、同一个地方登录的普通用户。

AdsPower 免费版个人用就够了，一两个号完全够用。

## 第一步：安装 AdsPower

到官网 [adspower.net](https://www.adspower.net) 下载，支持 Mac、Windows、Linux。注册登录后，在设置里能找到 API Key，以后想用 Claude Code 自动化管理环境时会用到，先不用管。

## 第二步：建分组，再建环境

1. 左侧「分组管理」→ 右上角「创建分组」，名字叫「Claude 账号」。
2. 左上角「新建浏览器」，名称填「Claude 账号」，分组选刚才建的分组。
3. 浏览器内核用默认的 SunBrowser（Chrome 内核），操作系统选和你电脑一致的（Mac 就选 macOS），看起来更自然。

## 第三步：代理信息选「直连」

本地代理软件已经开了 **TUN 模式**的话，代理类型选 **No Proxy（本地直连）**就行：环境里的流量会被 TUN 接管，出口就是你的美国家宽 IP。

> [!TIP]
> **必须开 TUN，只开「系统代理」不行**。指纹浏览器不一定走系统代理，只开系统代理时它可能直接用国内网络出去。没开 TUN、或者要给每个环境单独配一个出口，就用独享 socks5，填法见[AdsPower 配合独享 socks5 完整配置](../claude/adspower-socks5-setup.md)。

## 第四步：配置指纹

在「指纹配置」里重点看这几项，目标是：**指纹独立、固定，但看起来像一个真实用户**。

| 项目 | 说明 |
| --- | --- |
| UA 系统 | 和真实系统一致（Mac 选 macOS） |
| 时区 / 地理位置 / 语言 | 基于 IP |
| Canvas 指纹 | 噪声 |
| WebGL 图像 | 噪声 |
| AudioContext | 噪声 |
| WebRTC | 禁用（防止泄露真实 IP） |
| 端口扫描保护 | 开启 |

语言设成「基于 IP」很重要：日常浏览器的语言是 zh-CN，出口却在美国，检测站会标「语言不一致」。

## 第五步：打开环境先检测，再登录

环境建好后，**先在这个环境里打开 [ip.net.coffee/claude](https://ip.net.coffee/claude/)**，对照下面三条：

1. **AI 信任评分 90 分以上**，不能是机房 IP。评分低就换线路，别硬上。
2. **DNS 泄露里不能有中国**。有的话打开全局 + TUN 再测。
3. **第一行三个 IP 必须一样**。Claude 可能做指纹检测，任何一个出口漏出国内 IP 都是风险，所以用 Claude 时一直开着全局 + TUN。

完整的检测站和合格标准见[使用 Claude 前的电脑检测](claude-pc-checkup.md)。都通过了，再在这个环境里打开 claude.ai 登录。

## 日常使用的规矩

- **一个号一个环境**，环境之间不混用；多个号的完整隔离方案见[指纹浏览器 + 独享住宅 IP：多号隔离](../claude/fingerprint-browser-isolation.md)。
- **Claude 网页端只在这个环境里登录**，日常浏览器用 BlockSite 屏蔽 claude.ai。
- 每次打开环境前，先确认代理客户端和 TUN 开着。客户端没开时，别打开这个环境。

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/claude-adspower-profile) 为准。
