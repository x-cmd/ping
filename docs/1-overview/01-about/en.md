# Why one ping is not enough

## The question this page answers

You run `ping example.com` and you get a number. That number feels like a property of the *target* — "example.com is 30 ms away". It is not. **It is a property of the path between you and the target**, and it is a property of exactly one path out of the millions that exist.

A round-trip time measures a sum. Every router that receives your packet and forwards it adds its own processing time; every fibre segment contributes its propagation time; the reply comes back the same way and adds the same contributions again. What you get is one number standing in for an entire chain of dozens of devices and thousands of kilometres — and a different chain would give a different number.

This matters more than it sounds, because of how common the failure is. When a service is slow, the instinct is to check it from somewhere else. But if your own path happens to be the bad one, every check you run from the same desk will keep telling you the same reassuring lie. **The measurement is biased toward your location, and the bias is invisible unless you move.**

## What a probe network changes

[Globalping](https://globalping.io) is a volunteer network of measurement probes hosted in datacenters and residential networks around the world. You submit a target; it runs the measurement from dozens of vantage points at once and returns structured results — one record per probe, with the round-trip times, the packet loss, and the coordinates.

The shift is not "more data". It is **changing what the data is a sample of**. A ping from your desk is a sample of one path out of many. A ping from eighteen probes in fourteen countries is a sample of the *distribution* of paths — and once you have a distribution, you can see things a single sample structurally cannot:

- **Which locations are far, and which are close.** Often the answer is "it depends on the network, not the country".
- **How much the numbers vary inside one location.** A country with four probes at 4.1, 4.2, 4.3 and 15.9 ms is a different situation from a country with four probes at 4.1, 4.2, 4.3 and 4.4 ms.
- **Whether anyone is being ignored entirely.** A probe with 100% packet loss contributes nothing to any average — it simply never answers, and averages are blind to it.

## The measurement behind these pages

Everything in this section comes from a single measurement, taken on **2026-10-29**: `ping 1.1.1.1`, **18 probes across 6 continents and 14 countries**, **4 packets each**. The target is deliberately an anycast address, which makes it a good subject — see the next page.

One probe (netcup, Nuremberg) had 100% packet loss and is excluded from every average shown. It is not excluded to tidy the numbers; it is excluded because averaging over a node that never responded is not meaningful.

> CLI: `x ping` — see [x-cmd.com/mod/ping](https://x-cmd.com/mod/ping)
> The official Globalping CLI is packaged as `x globalping`
> Data: [api.globalping.io/v1/probes](https://api.globalping.io/v1/probes) · [api.globalping.io/v1/measurements](https://api.globalping.io/v1/measurements)

## What this is not

Two caveats that apply to everything that follows, so you can hold them in mind while reading:

**The probes are a convenience sample, not a random one.** They are volunteer-run, so they cluster where hosting is cheap — datacenters in particular. This skews the picture toward well-connected networks. A country with no volunteer probe does not appear as "unreachable"; it appears as "not measured".

**Four packets is a small sample of one moment.** It is enough to separate 1 ms from 12 ms. It is not enough to characterise a link's behaviour over a day. Treat every number here as a snapshot of 2026-10-29, and re-measure before drawing a conclusion about anything that matters.
