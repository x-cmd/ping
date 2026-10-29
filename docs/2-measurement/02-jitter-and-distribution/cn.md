# 抖动与分布

**每个探针的 RTT 区间 —— 最小、平均、最大**，取六个最慢的位置。区间越宽说明链路越丢包或越拥塞，越窄说明越干净。

```text
https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms
```

<a href="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms"><img src="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms" width="780" alt=""></a>

**全部 17 个有响应探针的 RTT 分布。**

```text
https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes
```

<a href="https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes"><img src="https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes" width="780" alt=""></a>

**延迟最低的七个探针。**

```text
https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms
```

<a href="https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms" width="780" alt=""></a>

分布图说明了什么：

- **17 个探针里有 7 个落在 1–2 ms 区间**，4 个不到 1 ms —— 超过一半的探针在两毫秒以内。
- **只有 2 个探针高于 8 ms**，而且都是北美的（布法罗、 Spokane）—— 长尾不是地理散布，而是两个具体的网络。
- **那个 100% 丢包的探针**（Nuremberg，netcup）没有出现在上面任何一个均值里。静默丢包正是单次 ping 完全看不见的故障模式：主机并没有"宕机"，它只是从来不回包。

最后这一点，就是为什么任何可达性测量都值得单独给丢包率一条系列 —— 也是 Globalping 在 `stats.avg` 之外还返回 `stats.loss` 的原因。

> 复现：与上一页同一个测量；把 `results[].result.stats.avg` 分箱统计，丢包率取 `results[].result.stats.loss`。
