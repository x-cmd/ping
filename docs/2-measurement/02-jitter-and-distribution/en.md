# Jitter, loss, and the shape of the distribution

## Why an average is not enough

An average round-trip time tells you the typical delay and nothing about whether the link is steady. Two links can both average 5 ms while being completely different experiences: one that delivers every packet at 5 ms, and one that alternates between 1 ms and 9 ms.

The number that separates them is jitter, the spread between the best and the worst packet. For interactive work, a shell, a voice call, a game, a terminal over SSH, jitter is often worse than latency, because applications react to changes rather than to means.

## The spread per probe

Minimum, average and maximum, across the six slowest locations:

```text
https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms
```

<a href="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms"><img src="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms" width="780" alt="jitter-loss-and-the-shape-of"></a>

A wide band means a path that is losing packets or queueing them: the packet you send late is not late because the distance grew, it is late because it sat in a buffer. A narrow band means a clean path.

## The distribution

```text
https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes
```

<a href="https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes"><img src="https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes" width="780" alt="jitter-loss-and-the-shape-of"></a>

The shape carries information the average destroys. Eleven of the 17 responding probes land at or below 2 ms, four under 1 ms, so more than half the fleet is inside two milliseconds. The distribution is right-skewed rather than spread evenly, with a dense low-latency cluster and a thin tail. And only two probes sit above 8 ms, both North American (Buffalo at 12.1 ms, Spokane at 8.4 ms). The tail is two specific networks, not a slow internet.

## The probe that is in none of these charts

One probe had 100% packet loss. It is excluded from the averages above, and it is the most interesting data point in the measurement.

Silent loss is the failure mode an average is structurally blind to. A host that is down refuses connections and shows up everywhere as zero. A host that silently drops packets looks fine to an average: it contributes a slightly high number, or in this case contributes nothing at all because it never answered. If you only track `avg`, you never learn the difference.

That is why Globalping returns `stats.loss` next to `stats.avg`, and why any serious reachability measurement carries loss as its own series rather than folding it into an average.

## The lowest-latency probes

```text
https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms
```

<a href="https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms" width="780" alt="jitter-loss-and-the-shape-of"></a>

## What an operator does with this

A high average with a narrow band means the path is long but clean, and the fix is a different path: another provider, another PoP. A low average with a wide band means the path is short but congested, and the fix is capacity rather than routing. A probe at 100% loss is not a performance problem at all but a reachability problem, and it belongs in a different queue.

## Caveats

One measurement, 2026-10-29. Congestion is time-of-day dependent and this was a single point in the day. The banding is our choice of edges (`<1`, `1–2`, `2–4`, `4–8`, `>=8` ms) rather than something the data dictates; with more probes the bands should come from the observed quantiles. And the 100%-loss probe is a single observation, which could be a real outage, a problem on the probe side, or ICMP filtering.

> Reproduce: the same measurement as the previous page; group `results[].result.stats.avg` into bands, and read `results[].result.stats.loss` for the packet-loss rate.
