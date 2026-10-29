# ping

**Global reachability, as plain URLs.** Measure one target from hundreds of probes worldwide and the answer is a picture, not a number. Every chart below is a [x-cmd draw](https://github.com/x-cmd/draw) URL — the data lives in the link, server-rendered as SVG.

> Languages: English · 中文（每页双语；es / ja / de 待补）

## Sneak peek

### Round-trip time, by probe — [full page](docs/2-measurement/01-rtt-by-location/en.md)

<a href="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms"><img src="https://x-cmd.com/draw?type=hbar&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA%2CMumbai%20IN%2CLos%20Angeles%20US%2CHelsinki%20FI%2CTokyo%20JP%2CSingapore%20SG%2CSydney%20AU%2CLagos%20NG%2CFrankfurt%20DE%2CKansas%20City%20US%2CSao%20Paulo%20BR%2CMontreal%20CA&values=12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242%2C1.653%2C1.41%2C1.401%2C1.319%2C1.305%2C1.303%2C1.093%2C0.876%2C0.859%2C0.851%2C0.746&title=GlobalPing%3A%20RTT%20to%201.1.1.1%20from%2018%20probes&unit=ms" width="680" alt="round-trip-time-by-probe"></a>

### Jitter and distribution — [full page](docs/2-measurement/02-jitter-and-distribution/en.md)

<a href="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms"><img src="https://x-cmd.com/draw?type=radar&t=evil&labels=Buffalo%20US%2CSpokane%20US%2CFalkenstein%20DE%2CNuremberg%20DE%2CRoubaix%20FR%2CJohannesburg%20ZA&series=min%3A12.022%2C8.403%2C5.615%2C4.005%2C3.891%2C2.08&series=avg%3A12.06%2C8.431%2C5.824%2C4.061%2C4.027%2C2.242&series=max%3A12.129%2C8.459%2C6.068%2C4.13%2C4.205%2C2.656&title=GlobalPing%20RTT%20spread%20by%20probe&unit=ms" width="680" alt="jitter-and-distribution"></a>

## All pages

### overview

- [About this page](docs/1-overview/01-about/en.md)

### measurement

- [Round-trip time, by probe](docs/2-measurement/01-rtt-by-location/en.md)
- [Jitter and distribution](docs/2-measurement/02-jitter-and-distribution/en.md)

## 中文页

### overview

- [关于本页](docs/1-overview/01-about/cn.md)

### measurement

- [按探针看往返时延](docs/2-measurement/01-rtt-by-location/cn.md)
- [抖动与分布](docs/2-measurement/02-jitter-and-distribution/cn.md)

## How the charts work

Each figure is a plain `https://x-cmd.com/draw?type=…` URL — rendered to a standalone SVG by [x-cmd/draw](https://github.com/x-cmd/draw). No JS runs on this page, the SVG is static, printable, and AI-readable: the parameters *are* the data, so the same URL can be edited to query something else.

To make your own: run the CLI, then hand the output to draw — or let your AI agent do it with the [draw skill](https://raw.githubusercontent.com/x-cmd/draw/main/SKILL.md).

## Data sources

Globalping public API — [api.globalping.io/v1/probes](https://api.globalping.io/v1/probes) · measurement taken 2026-10-29 against `1.1.1.1` (18 probes, 6 continents, 14 countries, 4 packets each). The official Globalping CLI is packaged as `x globalping`.
