# Jitter, loss, and the shape of the distribution

## Why an average is not enough

An average round-trip time tells you the typical delay and nothing about whether the link is *steady*. Two links can both average 5 ms while being completely different experiences: one that delivers every packet at 5 ms, and one that alternates between 1 ms and 9 ms.

The first number that separates them is **jitter** — the spread between the best and worst packet. For interactive work (a shell, a voice call, a game, a terminal over SSH) jitter is often worse than latency, because applications react to *changes*, not to means.

## The spread per probe

Minimum, average and maximum, across the six slowest locations:

```text
https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms
```

<a href="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms"><img src="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms" width="780" alt="jitter-loss-and-the-shape-of"></a>

A wide band means a path that is losing packets or queueing them — the packet you send late is not late because the distance grew, it is late because it sat in a buffer. A narrow band means a clean path.

## The distribution

```text
https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes
```

<a href="https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes"><img src="https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes" width="780" alt="jitter-loss-and-the-shape-of"></a>

The shape carries information that the average destroys:

- **11 of 17 responding probes land at or below 2 ms** — 4 under 1 ms, 7 in the 1–2 ms band. More than half the fleet is inside two milliseconds.
- **The distribution is right-skewed, not spread evenly.** There is a dense low-latency cluster and a thin tail.
- **The tail is two probes, and both are North American** (Buffalo at 12.1 ms, Spokane at 8.4 ms). The tail is not "the internet is slow everywhere"; it is two specific networks.

## The probe that is not in any of these charts

**One probe had 100% packet loss.** It is excluded from the averages above, and it is the most interesting data point in the measurement.

Silent loss is the failure mode an average is structurally blind to. A host that is down refuses connections and shows up everywhere as zero. A host that silently drops packets looks *fine* to an average — it just contributes a slightly high number, or in this case contributes nothing at all because it never answered. If you only track `avg`, you never learn the difference.

This is why Globalping returns `stats.loss` next to `stats.avg`, and why any serious reachability measurement should carry loss as its own series rather than as an average.

## The lowest-latency probes

```text
https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms
```

<a href="https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms" width="780" alt="jitter-loss-and-the-shape-of"></a>

## What an operator does with this

- **High average, narrow band** → the path is long but clean. Fix it by changing the path (different provider, different PoP).
- **Low average, wide band** → the path is short but congested. Fix it with capacity, not with routing.
- **A probe at 100% loss** → not a performance problem at all; a reachability problem, and it belongs in a different queue.

## Caveats

- One measurement, 2026-10-29. Congestion is time-of-day dependent and this was a single point in the day.
- Banding is our choice of edges (`<1`, `1–2`, `2–4`, `4–8`, `>=8` ms), not something the data dictates. With more probes the bands should be chosen from the observed quantiles.
- The 100%-loss probe is a single observation. It could be a genuine outage, a probe-side problem, or ICMP filtering.

> Reproduce: the same measurement as the previous page; group `results[].result.stats.avg` into bands, and read `results[].result.stats.loss` for the packet-loss rate.
