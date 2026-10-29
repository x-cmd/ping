# 按探针看往返时延

## 这次测量

18 个探针，每探针 4 个包，目标是 `1.1.1.1`，按延迟从高到低排。

```text
https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms
```

<a href="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms" width="780" alt=""></a>

## 为什么 1.1.1.1 是个特殊的被测对象

`1.1.1.1` 是 **anycast**：同一个地址在全球网络的成百上千个点同时宣告，互联网的路由系统把你的流量导向你的网络最先到达的那个宣告。**不存在"那台服务器"**。回应你的 Cloudflare 节点，是你的运营商恰好离得最近的那一个。

这带来一个容易忽略的后果：**这张图测的不是到某台服务器的距离，而是"透过每个探针自己的网络"看到的、最近的 Cloudflare 节点的距离。** 目标是常量，答案不是。

## 怎么读这张图

**地板在 1 ms 附近，而且 lots of 地方贴着地板。** 东京（Oracle）、新加坡（LeaseWeb）、法兰克福、圣保罗、堪萨斯城、蒙特利尔、��加斯都在一毫秒左右。实际含义是"离某个 Cloudflare 节点约一百公里，且你的网络和它直连" —— 因为光在光纤里约每毫秒走 200 公里，1 ms 的往返大约对应 100 km 的单程。

**布法罗（HostPapa）12.1 ms 是那个异常值**，接近东京的十倍。这正是单点测量看不见的失效形状：同一秒里，从法兰克福看服务完美无缺，从布法罗看服务彻底坏了。

**而且地理解释不了它。** Falkenstein 和 Nuremberg（Hetzner、netcup）在 4–6 ms，而几百公里外的法兰克福不到 1 ms。如果距离是主因，这三座德国城市应该挤在一起。它们没有 —— 它们之间的差距来自**互联**：哪些网络之间有直连路由，哪些必须把流量转交别人的骨干网。

最后这一点才是实际的教训。**用户到一个服务的距离不是地理事实，而是网络事实**，由用户永远看不到的协议决定。一张时延表，其实是一张协议分布图。

## 对被测者的意义

- 如果你在布法罗那样的网络上，本地怎么调都没用。路径本身就长，修复权在掌握它的人手里。
- 如果你在东京那样的网络上，服务等于在你家门口 —— 那么你遇到的任何回退都是本地回退，不是全球回退。
- 如果你在中间那一档，有意思的问题就变成"我的运营商到这个目的地走的是谁的骨干，这条是不是他最短的一条" —— 图回答不了这个问题，但它让你想到这个问题。

## 注意事项

- **四个包，一个瞬间。** 排序是稳的，单个数字在多次测量间会漂动几百分之一毫秒。
- **探针的位置不等于用户的位置。** 放在 Oracle 数据中心的探针，不等于同一家 ISP 里的家庭用户。
- **测的是 ICMP。** 很多网络对 ICMP 的优先级低于 TCP。所以这里的绝对值最好**互相之间比较**，而不是当成你应用会看到的时延。

> 复现：`POST https://api.globalping.io/v1/measurements`，body 为
> `{"type":"ping","target":"1.1.1.1","limit":18,"measurementOptions":{"packets":4}}`，
> 然后轮询 `GET /v1/measurements/{id}`，按 `results[].probe.location` 取 `results[].result.stats.avg`。
