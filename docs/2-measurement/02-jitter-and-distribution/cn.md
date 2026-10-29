# 抖动、丢包与分布的形状

## 为什么均值不够

一个平均往返时延只告诉你典型的延迟，完全没告诉你这条链路稳不稳。两条链路都可能平均 5 毫秒，而体验天差地别：一条每个包都准时 5 毫秒，另一条在 1 毫秒和 9 毫秒之间来回跳。

把两者分开的第一数字是抖动，也就是最好和最差之间的差距。对交互式工作来说，一个 shell、一次语音通话、一局游戏、一条 SSH 终端，抖动往往比延迟更难受，因为应用是对变化作出反应，不是对均值。

## 每个探针的区间

最小、平均、最大，取六个最慢的位置：

```text
https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms
```

<a href="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms"><img src="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms" width="780" alt=""></a>

区间越宽，说明这条路径在丢包或在排队：你那个迟到的包不是因为距离变长了而迟到，是因为它在缓冲区里坐了一会儿。区间越窄，说明路径干净。

## 分布

```text
https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes
```

<a href="https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes"><img src="https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes" width="780" alt=""></a>

形状里带着均值抹掉的信息。17 个有响应的探针里有 11 个落在 2 毫秒以内，4 个不到 1 毫秒，超过一半的探针在两毫秒之内。分布是右偏的而不是均匀铺开，一团致密的低延迟加上一条细尾巴。而高于 8 毫秒的只有两个探针，都是北美的（布法罗 12.1 毫秒、Spokane 8.4 毫秒）。这条尾巴是两个具体的网络，不是「整个互联网都慢」。

## 没有出现在任何一张图里的那个探针

有一个探针 100% 丢包。它被排除在上面的均值之外，而它恰恰是这次测量里最有意思的数据点。

静默丢包正是均值在结构上看不见的失效模式。一台真正宕机的主机会拒绝连接，在任何统计里都表现为零。而一台静默丢包的主机在均值看来完全正常：它只是贡献了一个稍高的数字，或者像这个探针一样，因为从不回答而什么都没贡献。如果你只盯 `avg`，你永远分不出这两者的区别。

这就是为什么 Globalping 在 `stats.avg` 之外还返回 `stats.loss`，也是为什么任何认真的可达性测量都该把丢包率单列一列，而不是揉进均值里。

## 延迟最低的探针

```text
https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms
```

<a href="https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms" width="780" alt=""></a>

## 运维拿到这些会怎么做

均值高、区间窄，说明路径长但干净，换一条路（换运营商、换 PoP）能解决。均值低、区间宽，说明路径短但拥塞，要加容量而不是换路由。某个探针 100% 丢包，那根本不是性能问题，是可达性问题，应该进另一个队列。

## 注意事项

一次测量，2026-10-29。拥塞随时段变化，而这只测了当天的一个点。分箱是我们选的边界（`<1`、`1–2`、`2–4`、`4–8`、`>=8` 毫秒），不是数据本身要求的，探针更多时应当按观测到的分位数来定。而那个 100% 丢包的探针只是单次观测，可能是真的故障，也可能是探针侧的问题，或者 ICMP 被过滤了。

> 复现：与上一页同一个测量，把 `results[].result.stats.avg` 分箱统计，丢包率取 `results[].result.stats.loss`。
