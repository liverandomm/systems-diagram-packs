# GitHub, 17 Aug 2026: how retries amplified the overload

Diagram pack for **Systems, Drawn · Video #3**.

**License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).** Every architecture diagram in this episode is available under CC BY 4.0. Use it, adapt it, and put it in your own postmortems and talks. Credit "Systems, Drawn · SRE // microservices". See [LICENSE.md](LICENSE.md).

📄 **On a phone?** Open the whole pack as one PDF: [`systems-drawn-03-diagram-pack.pdf`](systems-drawn-03-diagram-pack.pdf).

## Primary sources

- **GitHub status page, incident and root cause analysis (RCA):** https://www.githubstatus.com/incidents/zkxwbgr0cnmx
- **GitHub availability report, August 2026:** https://github.blog/news-insights/company-news/github-availability-report-august-2026/
- **GitHub CTO, "The August 17 outage and the work ahead" (20 Aug 2026):** https://github.blog/news-insights/company-news/the-august-17-outage-and-the-work-ahead/

Every time, figure and GitHub follow-up in these diagrams comes from those three sources unless it's marked *illustrative* or *our reading*.

All times are **UTC**, as GitHub reports them, written 12-hour style as in the video (e.g. 1:28 PM UTC). Buenos Aires time (ART) is UTC − 3 h.

## The operational lesson

A new traffic peak pushed an Istio sidecar in Central US to its concurrency limit, and it didn't scale up, because the autoscaling policy watched the host service, not the sidecar.

Pressure moved to four HAProxy nodes on the shared gateway authentication path. From there, retries in two layers made it worse:
- optimistic retries inside GitHub's gateway;
- a latent retry bug in a VS Code client, which took Copilot Token Service from 7–9K to 70–100K requests per second.

Part of how GitHub recovered: stopping the saturated nodes at the same time, refusing some requests on purpose (a 403 on token requests) and cutting retries before ramping traffic back site by site. A gradual ramp lowers the risk of a second wave, but it can't stop clients already stuck in a retry loop; those had to be blocked first.

## Files

Each diagram has three versions:
- an editable D2 source in `src/`;
- an SVG in `svg/` that scales to any screen;
- a PNG at 2x in `png/` for retina displays and phones.

**`seq_retry_two_layers`**: UML sequence, simplified. Shows the client, the gateway, the saturated internal LB and auth / Copilot Token Service, with the two retry loops. The hop order is didactic, and the ×3 attempt counts are illustrative.
[PNG](png/seq_retry_two_layers.png) · [SVG](svg/seq_retry_two_layers.svg) · [D2](src/seq_retry_two_layers.d2)

**`topology_shared_auth`**: microservice topology, **simplified: GitHub doesn't publish this topology** (hop order, fan-in and node count are our drawing from the RCA). Shows the products that route through Central US fanning in to four HAProxy nodes and the shared gateway auth path, the pod with its sidecar, and the autoscaler that watched the wrong gauge.
[PNG](png/topology_shared_auth.png) · [SVG](svg/topology_shared_auth.svg) · [D2](src/topology_shared_auth.d2)

**`retry_multiplier`**: why layers multiply (3 × 3 = 9 calls per click, illustrative) and the fix: retry at one layer, with a budget.
[PNG](png/retry_multiplier.png) · [SVG](svg/retry_multiplier.svg) · [D2](src/retry_multiplier.d2)

**`timeline_aug17`**: the day in UTC, from first impact at 1:28 PM to resolution at 9:15 PM, plus the three durations the sources give.
[PNG](png/timeline_aug17.png) · [SVG](svg/timeline_aug17.svg) · [D2](src/timeline_aug17.d2)

**`failure_dynamics`**: the channel's failure-over-time grid. First event, signal, shared resource, amplifier, containment, trade-off, recovery (block the loop, then ramp back slowly).
[PNG](png/failure_dynamics.png) · [SVG](svg/failure_dynamics.svg) · [D2](src/failure_dynamics.d2)

**`aug06_backlog`**: the 6 Aug 2026 Actions incident. A routine deploy, sidecars throttled, runners stuck retrying revoked jobs: a self-amplifying backlog.
[PNG](png/aug06_backlog.png) · [SVG](svg/aug06_backlog.svg) · [D2](src/aug06_backlog.d2)

**`monday_policy`**: six recommendations (our opinion, built from GitHub's published follow-ups), each with what it does NOT solve.
[PNG](png/monday_policy.png) · [SVG](svg/monday_policy.svg) · [D2](src/monday_policy.d2)

## Render

`d2 --layout elk src/<file>.d2 svg/<file>.svg` (D2 v0.9.0). `src/_style.d2` holds the shared dark theme.

## Corrections

Spotted something off? Open an issue or a pull request and cite the source. Accepted fixes are credited in [CHANGELOG.md](CHANGELOG.md).
