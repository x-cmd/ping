# 关于本页

从你自己的笔记本 ping 一次，只说明一件事：*你的*网络离目标有多远。它完全不能回答目标从拉各斯、大阪还是圣保罗能不能连上。

同时从很多个观测点测，答的才是人们真正想问的问题 —— **「是我这边的问题，还是所有人的问题？」**

[Globalping](https://globalping.io) 就是干这个的：全球数据中心和民用网络上的志愿者探针，替你在那个位置发起测量，然后把结构化结果还给你 —— 每个探针一组，含往返时延、丢包率和坐标。

这里是 **2026-10-29** 对 `1.1.1.1` 的一次实测：**6 大洲 14 国 18 个探针**，每个 4 个包。其中一个探针（Nuremberg 的 netcup）100% 丢包，已从均值中剔除。

> CLI：`x ping` —— 见 [x-cmd.com/mod/ping](https://x-cmd.com/mod/ping) · `x globalping`（官方 CLI，已由 x-cmd 打包）
> 数据：[api.globalping.io](https://api.globalping.io/v1/probes)
