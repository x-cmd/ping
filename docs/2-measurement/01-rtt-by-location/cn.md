# 按探针看往返时延

**从全球 18 个探针到 1.1.1.1 的平均 RTT**，按延迟从高到低排。

```text
https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms
```

<a href="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms" width="780" alt=""></a>

这张图的形状，就是全球测量存在的全部理由：

- **东京（Oracle）和新加坡（LeaseWeb）领先，约 1.3 ms**，而法兰克福、圣保罗、堪萨斯城、蒙特利尔都在 1 ms 以内。`1.1.1.1` 是 anycast，所以实际测到的是*到最近 Cloudflare 节点的物理距离*，不是到某台固定服务器的距离。
- **布法罗（HostPapa）是异常值，12.1 ms** —— 接近东京的十倍。对这个网络里的用户来说，Cloudflare 的 anycast 落得很远，本地怎么调都没用。
- **Falkenstein 和 Nuremberg（Hetzner、netcup）在 4–6 ms**，而 300 km 外的法兰克福不到 1 ms。这个差距来自互联，而不是地理。

**在一间办公室 ping 一次，这些信息一个都得不到。** 从法兰克福看服务完美，从布法罗看服务坏了。同一时刻两者都成立，只有多点测量才能把它们分开。

> 复现：`POST https://api.globalping.io/v1/measurements`，body 为 `{"type":"ping","target":"1.1.1.1","limit":18,"measurementOptions":{"packets":4}}`，
> 然后轮询 `GET /v1/measurements/{id}`，按 `results[].probe.location` 取 `results[].result.stats.avg`。
