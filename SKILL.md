---
name: ping
description: Global reachability measurements from a worldwide probe network (Globalping), plus the charts built from them. Use when the user asks about "latency from multiple locations", "is it reachable from other countries", "RTT distribution", "jitter", "packet loss", "global ping", or wants a multi-region reachability picture of a target.
metadata: type=reference, source=globalping-api, schema=yaml-1-doc, refresh=manual, license=apache-2.0, scope=latency-reachability
---

# x-cmd/ping — measuring reachability from many places

Two consumption paths. Pick whichever fits.

## 1. Run a measurement (the official CLI is packaged by x-cmd)

```bash
x globalping ping 1.1.1.1                 # from the nearest probe
x globalping ping 1.1.1.1 --country DE --country JP
x globalping traceroute 1.1.1.1
x globalping dns example.com
x globalping http example.com
x globalping -h                            # full help
```

The same thing over the raw API, if you would rather not install anything:

```sh
curl -X POST 'https://api.globalping.io/v1/measurements' \
  -H 'content-type: application/json' \
  -d '{"type":"ping","target":"1.1.1.1","limit":18,"measurementOptions":{"packets":4}}'
# → {"id":"...","probesCount":18}
curl 'https://api.globalping.io/v1/measurements/<id>'
```

Each result carries `probe.location` (continent, country, city, asn, network,
lat/lon) and `result.stats` (min, max, avg, loss, rcv, drop).

## 2. Read the recorded measurement

```sh
curl -fsSL https://raw.githubusercontent.com/x-cmd/ping/main/data/measurement-2026-10-29.yaml
```

```yaml
probes:
  - city: Buffalo
    country: US
    network: HostPapa
    avg_ms: 12.06
    loss_pct: 0
```

## Turning it into a chart

```
https://x-cmd.com/draw?type=hbar&labels=Tokyo,Buffalo&values=1.319,12.06&title=RTT%20by%20probe&unit=ms
```

## Watch out

- **An average hides loss.** A probe at 100% packet loss contributes nothing to
  any mean and never shows up as "down". Always carry `stats.loss` alongside
  `stats.avg`.
- **The probes are a convenience sample.** They are volunteer-run and cluster
  where hosting is cheap. A country with no probe is "not measured", not
  "unreachable".
- **A single ping measures your path, not the target.** That is the entire
  reason a probe network exists.
- **ICMP is deprioritised on many networks.** Read the absolute numbers as
  relative to each other, not as application latency.
