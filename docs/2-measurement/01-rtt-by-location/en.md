# Round-trip time, by probe

**Average RTT to 1.1.1.1 from 18 probes worldwide**, sorted slowest first.

```text
https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms
```

<a href="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms" width="780" alt="round-trip-time-by-probe"></a>

The shape of this chart is the whole argument for global measurement:

- **Tokyo (Oracle) and Singapore (LeaseWeb) lead at ~1.3 ms**, and Frankfurt, São Paulo, Kansas City and Montreal all sit under 1 ms. `1.1.1.1` is anycast, so what is actually being measured is *distance to the nearest Cloudflare point*, not distance to a fixed server.
- **Buffalo (HostPapa) is the outlier at 12.1 ms** — nearly ten times Tokyo. For a user in that network, Cloudflare's anycast is landing far away, and no amount of local tuning fixes it.
- **Falkenstein and Nuremberg (Hetzner, netcup) sit at 4–6 ms** while Frankfurt — 300 km away — is under 1 ms. That gap is peering, not geography.

**A single ping from one office cannot tell you any of this.** From Frankfurt the service looks perfect; from Buffalo it looks broken. Both are true at the same moment, and only a multi-probe measurement separates them.

> Reproduce: `POST https://api.globalping.io/v1/measurements` with `{"type":"ping","target":"1.1.1.1","limit":18,"measurementOptions":{"packets":4}}`,
> then poll `GET /v1/measurements/{id}` and take `results[].result.stats.avg` per `results[].probe.location`.
