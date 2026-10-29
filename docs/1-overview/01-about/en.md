# Why one ping is not enough

## The question this page answers

Run `ping example.com` and you get a number. It feels like a property of the target, as if "example.com is 30 ms away" were a fact about example.com. It is not. It is a fact about the path between you and the target, and it is a fact about exactly one path out of the millions that exist.

A round-trip time is a sum. Every router that receives your packet and forwards it adds its own processing time, every fibre segment contributes its propagation time, and the reply comes back the same way and adds the same contributions again. What you get is one number standing in for a chain of dozens of devices and thousands of kilometres. A different chain would give a different number.

This matters more than it sounds, because the failure is common. When a service is slow, the instinct is to check it from somewhere else. But if your own path happens to be the bad one, every check you run from the same desk keeps telling you the same reassuring lie. The measurement is biased toward your location, and the bias stays invisible until you move.

## What a probe network changes

[Globalping](https://globalping.io) is a volunteer network of measurement probes hosted in datacenters and residential networks around the world. You submit a target, it runs the measurement from dozens of vantage points at once, and it hands back structured results: one record per probe, with round-trip times, packet loss and coordinates.

The gain is not more data. It is a change in what the data is a sample of. A ping from your desk samples one path out of many. A ping from eighteen probes in fourteen countries samples the distribution of paths, and a distribution shows you things a single sample structurally cannot:

- Which locations are far and which are close, which is often a fact about networks rather than about countries
- How much the numbers vary inside one location, since four probes at 4.1, 4.2, 4.3 and 15.9 ms is a different situation from four probes at 4.1, 4.2, 4.3 and 4.4 ms
- Whether anyone is being ignored altogether, since a probe with 100% packet loss contributes nothing to any average

## The measurement behind these pages

Everything in this section comes from one measurement, taken on **2026-10-29**: `ping 1.1.1.1`, **18 probes across 6 continents and 14 countries**, **4 packets each**. The target is deliberately an anycast address, which makes it a good subject, as the next page explains.

One probe (netcup, Nuremberg) had 100% packet loss and is excluded from every average shown. It is excluded because averaging over a node that never responded would not mean anything.

> CLI: `x ping` — see [x-cmd.com/mod/ping](https://x-cmd.com/mod/ping)
> The official Globalping CLI is packaged as `x globalping`
> Data: [api.globalping.io/v1/probes](https://api.globalping.io/v1/probes) · [api.globalping.io/v1/measurements](https://api.globalping.io/v1/measurements)

## What this is not

Two caveats apply to everything that follows, so it helps to hold them in mind.

The probes are a convenience sample, not a random one. They are volunteer-run, so they cluster where hosting is cheap, datacenters especially. That skews the picture toward well-connected networks, and a country with no volunteer probe does not appear as unreachable; it appears as unmeasured.

Four packets is a small sample of one moment. It is enough to separate 1 ms from 12 ms. It is not enough to characterise a link's behaviour over a day. Treat every number here as a snapshot from 2026-10-29, and re-measure before drawing a conclusion about anything that matters.
