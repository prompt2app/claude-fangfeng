# 伪住宅 IP:Comcast 与 NTT / GTT 的区别

> 原文地址：[https://claucc.net/articles/fake-residential-ip](https://claucc.net/articles/fake-residential-ip)　·　更多教程见 [claucc.net](https://claucc.net)

一句话:并不是「注册方是家电信公司」就等于住宅。现在市面上被当成高端货卖的「静态住宅 / ISP 住宅」,相当一部分是 NTT、GTT 这类**骨干网运营商**的机房段——它们在风控数据库里被归为 hosting,和 Comcast 那种真家庭宽带完全是两类东西,价格却常常更贵。

## 先看结论

【真住宅(末端家庭宽带)】

- Comcast / Xfinity AS7922、AT&T AS7018、Spectrum AS20115、Cox AS22773、CenturyLink AS209、Frontier AS5650。
- 这些公司真的往一户户人家里拉线。IP 的另一头是**某个家庭的路由器**。
- IP 情报库的用途字段(usage type)返回 residential / consumer ISP。
- 地理位置分散在成百上千个城市,和人口分布一致。

【伪住宅(骨干 / 中转运营商)】

- NTT America AS2914、GTT AS3257、Cogent AS174、Zayo AS6461、Hurricane Electric AS6939、Arelion/Telia AS1299、Lumen AS3356。
- 它们**不做家庭宽带**,卖的是 IP 中转、机房托管和企业专线。IP 的另一头是机柜。
- 用途字段返回 hosting / corporate / transit——和 AWS 同一类。
- 整个 /24 全部落在一个机房城市(洛杉矶、阿什本、法兰克福),常年 100% 在线。

> [!TIP]
> 判断标准只有一个:**这家公司有没有往民宅里拉最后一公里的线**。有,才叫住宅;只做骨干和机房,注册名再像运营商也不是。

## NTT 的迷惑性在哪

NTT 确实是电信巨头,在日本也确实有家庭宽带,所以卖家一句「NTT 的住宅 IP」听上去毫无破绽。问题在于——**那是两张不同的网、两个不同的 ASN**:

| 你以为的 NTT | 你买到的 NTT |
| --- | --- |
| 日本的 NTT 东/西 + OCN(AS4713 等),真的接入日本家庭 | 美国的 NTT America / NTT Global(AS2914),前身是 Verio,做的是全球 IP 中转和数据中心 |
| 用途字段 residential | 用途字段 hosting |
| 日本住宅 IP,不是美国 IP | 美国 IP,但不是住宅 |

GTT 更直接一点:它**从来没有做过消费者宽带**。GTT(原 Global Telecom & Technology,后来吞并 Interoute)是一家纯粹的国际中转运营商,把它的段说成住宅,基本属于明知故说。

## 为什么风控一眼就能分出来

平台不靠猜。MaxMind、IPinfo、IP2Location、IPQS、Spur 这类 IP 情报库,每条记录都带一个**用途字段**,Claude / OpenAI / Cloudflare / Meta 直接读它。除此之外还有几条一看就露馅的特征:

| 项目 | 说明 |
| --- | --- |
| ASN 注册类型 | 家宽 ISP vs. NSP(网络服务商) / Hosting——RIR 登记里就写着 |
| 反查域名 rDNS | 家宽是每户自动生成(带 hsd1 / cpe / dyn / res 这类池标记);骨干段要么没有,要么是路由器命名 |
| WHOIS 再分配 | 家宽登记为用户地址池;骨干段登记为「已转租给某某公司」 |
| 地理分布 | 家宽散在几百个城市;骨干段整段一个机房坐标 |
| 在线特征 | 家宽有上下线、有换绑;骨干段 7×24 全亮,像服务器一样稳 |

*图:五条特征互相印证——伪住宅骗得过销售话术,骗不过风控库*

所以会出现这种经典场景:卖家给你看一张「Type: ISP」的截图,你自己换个检测站一查,Fraud Score 亮红、类型写着 Hosting。**不同检测站结论打架,本身就是伪住宅的典型症状**——真家宽 IP 在各家库里的结论是高度一致的。

## 为什么市面上卖的大多是这种

说白了是成本。

- **真住宅拿不到量**。要么和 ISP 谈合作拿真实用户段,要么靠装在真人设备上的 SDK 分成换出口——两条路都慢、都贵、都有量的上限。
- **骨干段随便租**。向 NTT、GTT 这类运营商租一个 /24,单价比真住宅低一到两个数量级,今天下单明天就能开卖,想要多少有多少。
- **「住宅」两个字没人管**。ISP 住宅、静态住宅、原生住宅、双 ISP……这些全是营销词,没有任何行业定义和认证。注册名里带 Communications、Telecom,拿出来当住宅卖就完事了。

> [!CAUTION]
> 成本低一到两个数量级,售价却往往比真住宅还高——差价就是「住宅」这两个字的信息差。**贵不等于真,便宜更不等于真,只有 ASN 说了算**。

## 30 秒自己验货

#### 1. 拿到出口 IP

挂上代理,打开 `api.ipify.org`,记下这个 IP。

#### 2. 查它属于哪个 ASN

浏览器打开 `ipinfo.io/你的IP`,看 `org` 那一行;或者用 `bgp.he.net/ip/你的IP`。这一步是关键——**ASN 是伪造不了的,它由路由表决定**。

#### 3. 对号入座

出现 Comcast / AT&T / Spectrum / Cox / CenturyLink / Frontier = 真住宅。出现 NTT / GTT / Cogent / Zayo / Hurricane Electric / Telia / Lumen / 任何云厂商 = 机房段,不管卖家怎么说。

#### 4. 交叉验一遍

再用 `ippure.com` 看类型和风险分,和第 3 步的结论对得上才算过。判读方法见 [注册前环境自检](ip-quality-check.md)。

命令行更快:

```bash
# 1. 看自己的出口
curl https://api.ipify.org
73.xx.xx.xx

# 2. 看这个 IP 属于谁
curl https://ipinfo.io/<你的出口IP>/json
{
  "city": "Eugene",
  "region": "Oregon",
  "org": "AS7922 Comcast Cable Communications, LLC"
}
```

上面是一条真住宅线路的出口(IP 已打码),把你自己的出口 IP 填进去照着敲一遍:AS7922 = Comcast 家庭宽带,城市落在俄勒冈州 Eugene 这样的普通居民城市,而不是某个机房所在地。**不用信我说,自己敲一遍就知道**。

## 话术对照表

| 卖家说法 | 实际可能是什么 | 怎么拆 |
| --- | --- | --- |
| ISP 住宅 / 原生住宅 | 可能真,也可能是骨干段套壳 | 只认 ASN,别认形容词 |
| 双 ISP(Dual ISP) | 多半是机房自有的、登记成 ISP 的段 | 查 ASN,再看整段是不是全在同一个机房城市 |
| 静态住宅、永不更换 | 「静态」是对的,「住宅」未必 | 静态和住宅是两件事,要分开验 |
| 企业级住宅 / 商宽住宅 | corporate 段,用途字段不是 residential | 问清 ASN,不给就是答案 |
| 「我们不方便透露 ASN」 | — | 真住宅没有不敢报 ASN 的道理 |

## 公平地说:骨干段并非一无是处

要讲清楚,免得走到另一个极端。NTT / GTT 这类段**确实比 AWS、DigitalOcean 干净一档**:带宽足、延迟低、不在最脏的黑名单里。拿来采集数据、跑接口、做一般的境外访问,完全够用,而且更快更稳。

问题只出在一个地方——**会读用途字段的那批风控**。Claude、ChatGPT、TikTok、Meta、各大电商后台,恰恰全都在这一批里。它们不看你带宽多少,只看这个 IP 的另一头是不是像一个真人的家。所以:

- 跑爬虫、批量请求、看境外网页 → 骨干段没问题,还更划算。
- 注册和长期挂 AI 账号、社媒账号、店铺后台 → **必须是末端家庭宽带**,这一项没有折中方案。

> [!CAUTION]
> 最坑的情况是**拿伪住宅去注册**:当时能注册成功,你以为没事,平台那边其实已经记了一笔。后面再换成真住宅也洗不干净——和用机房 IP 注册是同一个坑,见 [为什么住宅 IP 至关重要](residential-ip.md)。

## 小结

| 项目 | 说明 |
| --- | --- |
| 唯一判据 | ASN 背后那家公司做不做家庭宽带 |
| 30 秒验法 | ipinfo.io/你的IP → 看 org |
| 看到 Comcast / AT&T / Spectrum | 真住宅 ✓ |
| 看到 NTT / GTT / Cogent / Zayo | 骨干机房段,不适合挂账号 |
| 卖家不肯说 ASN | 直接换一家 |

*图:一张表记住——形容词不值钱,ASN 才是硬证据*

---

有问题欢迎加 QQ 群 **1061861709** 交流，最新版本以 [claucc.net](https://claucc.net/articles/fake-residential-ip) 为准。
