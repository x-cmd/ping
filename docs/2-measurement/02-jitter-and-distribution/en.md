# Jitter and distribution

**RTT spread per probe — min, average and max** across the six slowest locations. A wide spread means a lossy or congested path; a tight one means a clean one.

```text
https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms
```

<a href="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms"><img src="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms" width="780" alt="jitter-and-distribution"></a>

**Distribution of RTT across all 17 responding probes.**

```text
https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes
```

<a href="https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes"><img src="https://x-cmd.com/draw?type=bar&labels=%3C1ms%2C1-2ms%2C2-4ms%2C4-8ms%2C%3E%3D8ms&values=4%2C7%2C1%2C3%2C2&title=GlobalPing%20RTT%20distribution%20across%2017%20probes&unit=probes" width="780" alt="jitter-and-distribution"></a>

**The seven lowest-latency probes.**

```text
https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms
```

<a href="https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=line&labels=Tokyo%2CSingapore%2CFrankfurt%2CLagos%2CSydney%2CMontreal%2CKansas%20City&series=RTT%3A1.319%2C1.305%2C0.876%2C1.093%2C1.303%2C0.746%2C0.859&title=GlobalPing%20lowest-latency%20probes&unit=ms" width="780" alt="jitter-and-distribution"></a>

What the distribution chart says:

- **7 of 17 probes land in the 1–2 ms band**, 4 are under 1 ms — more than half the fleet is inside two milliseconds.
- **Only 2 probes are above 8 ms**, and both are North American (Buffalo, Spokane) — the tail is not geographic spread, it is two specific networks.
- **The one 100%-loss probe** (Nuremberg, netcup) is absent from every average above. Silent loss is the failure mode a single ping hides entirely: the host is not "down", it simply never answers.

That last point is why loss rate deserves its own series in any reachability measurement — and why Globalping returns `stats.loss` alongside `stats.avg`.

> Reproduce: the same measurement as the previous page; group `results[].result.stats.avg` into bands, and read `results[].result.stats.loss` for the packet-loss rate.
