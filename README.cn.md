# ping —— 全球可达性

> 🌐 **English version: [README.md](./README.md)**.
>
> **📑 目录**
>
> - [为什么一次 ping 不够](#about)
> - [按探针看往返时延](#rtt-by-location)
> - [抖动、丢包与分布](#jitter-and-distribution)
>
> **开发者文档** —— 仓库结构、命名规则、CI：[`CONTRIBUTING.md`](./CONTRIBUTING.md)
> **数据使用** —— 命令、schema、脚本：[`SKILL.md`](./SKILL.md)

从你桌面发出的一次 ping 只测量一件事：*你的*网络离目标有多远。它没法告诉你目标从拉各斯、大阪还是圣保罗能不能连上。

这个站点同时从多个观测点测量，并把结果渲染成「数据在 URL 里」的图。

## 页面

### 为什么一次 ping 不够 — [全文](docs/1-overview/01-about/cn.md)

### 按探针看往返时延 — [全文](docs/2-measurement/01-rtt-by-location/cn.md)

<a href="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms" width="680" alt=""></a>

### 抖动、丢包与分布的形状 — [全文](docs/2-measurement/02-jitter-and-distribution/cn.md)

<a href="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms"><img src="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms" width="680" alt=""></a>

## 图是怎么工作的

每一张图都是一个明文的 `https://x-cmd.com/draw?type=…` URL —— 由 [x-cmd/draw](https://github.com/x-cmd/draw) 渲染成独立 SVG。页面上不跑任何 JavaScript；SVG 是静态的、可打印的、机器可读的，因为**参数本身就是数据**。

## 许可证

本仓库自身的��容以 Apache-2.0 授权（见 [LICENSE](./LICENSE)）。`data/` 下的派生数据来自各注册机构与公开 API，来源与日期见各文件头部。
