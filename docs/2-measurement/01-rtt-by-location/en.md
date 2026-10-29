# Round-trip time, by probe

## The measurement

18 probes, 4 packets each, against `1.1.1.1`, sorted slowest first.

```text
https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms
```

<a href="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms" width="780" alt="round-trip-time-by-probe"></a>

## Why 1.1.1.1 is a special subject

`1.1.1.1` is **anycast**: the same address is announced from hundreds of points in the global network, and the routing system steers your traffic to whichever announcement your network reaches first. There is no single "the server". Whichever Cloudflare point answers you is whichever one your provider happens to be closest to.

So this chart is not measuring distance to a server. It is measuring distance to the nearest Cloudflare point, as seen through each probe's own network. The target is a constant; the answer is not.

## Reading the chart

The floor is about 1 ms, and a lot of places sit on it. Tokyo (Oracle), Singapore (LeaseWeb), Frankfurt, São Paulo, Kansas City, Montreal and Lagos all answer in roughly one millisecond, which in practice means within about a hundred kilometres of a Cloudflare point on a network that peers with it. Light in fibre travels roughly 200 km per millisecond, so a 1 ms round trip is about a 100 km one-way path.

Buffalo (HostPapa) at 12.1 ms is the outlier, nearly ten times Tokyo. This is the shape of a problem a single-location measurement cannot see: from Frankfurt the service looks flawless, from Buffalo it looks broken, and both are true at the same instant.

Geography does not explain it either. Falkenstein and Nuremberg (Hetzner, netcup) sit at 4 to 6 ms while Frankfurt, a couple of hundred kilometres away, is under 1 ms. If distance drove the numbers, those three German cities would sit together. They do not, and the gap between them is peering: which networks have direct routes to which, and which have to hand traffic across someone else's backbone.

That is the practical lesson. The distance from a user to a service is not a fact about geography. It is a fact about networks, and it gets decided by agreements the users never see. A latency table is a map of those agreements.

## What it means for the person being measured

If you are on a network like Buffalo's, no amount of local tuning helps. The path itself is long and the fix belongs to whoever controls it. If you are on a network like Tokyo's, the service is effectively at your doorstep, and any regression you see is local rather than global. And if you are on one of the middling networks, the interesting question becomes who your provider's upstream is for this destination and whether that is the shortest route they have. The chart cannot answer that, but it makes you ask.

## Caveats

Four packets, one moment. The ranking is stable; the individual numbers move by a few tenths of a millisecond between runs. Probe placement is not customer placement either: a probe in an Oracle datacenter is not a residential user on the same ISP. And the measurement is ICMP, which many networks deprioritise relative to TCP, so the absolute values are best read against each other rather than as the latency your application will see.

> Reproduce: `POST https://api.globalping.io/v1/measurements` with `{"type":"ping","target":"1.1.1.1","limit":18,"measurementOptions":{"packets":4}}`, then poll `GET /v1/measurements/{id}` and take `results[].result.stats.avg` per `results[].probe.location`.
