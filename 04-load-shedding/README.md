# Rate limiting isn't enough: load shedding, explained

Diagram pack for **Systems, Drawn · Video #4**.

**License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).** Every architecture diagram in this episode is available under CC BY 4.0. Use it, adapt it, and put it in your own runbooks, design reviews and talks. Credit "Systems, Drawn · SRE // microservices". See [LICENSE.md](LICENSE.md).

📄 **On a phone?** Open the whole pack as one PDF: [`systems-drawn-04-diagram-pack.pdf`](systems-drawn-04-diagram-pack.pdf).

## Primary sources

- **AWS Builders' Library, David Yanacek, "Using load shedding to avoid overload" (2019):** https://d1.awsstatic.com/builderslibrary/pdfs/using-load-shedding-to-avoid-overload.pdf (web: https://builder.aws.com/content/3Eun1EEyX6p2e3VYNyRLSJzLuMV/using-load-shedding-to-avoid-overload)
- **Google SRE book, ch. 21 "Handling Overload":** https://sre.google/sre-book/handling-overload/
- **Google SRE book, ch. 22 "Addressing Cascading Failures":** https://sre.google/sre-book/addressing-cascading-failures/
- **Netflix `concurrency-limits` README:** https://github.com/Netflix/concurrency-limits
- **Netflix Tech Blog, "Keeping Netflix Reliable Using Prioritized Load Shedding" (2020):** https://netflixtechblog.com/keeping-netflix-reliable-using-prioritized-load-shedding-6cc827b02f94
- **Netflix Tech Blog, "Enhancing Netflix Reliability with Service-Level Prioritized Load Shedding" (2024):** https://netflixtechblog.com/enhancing-netflix-reliability-with-service-level-prioritized-load-shedding-e735e6ce8f7d
- **Envoy docs (latest, Oct 2026):** adaptive concurrency https://www.envoyproxy.io/docs/envoy/latest/configuration/http/http_filters/adaptive_concurrency_filter · admission control https://www.envoyproxy.io/docs/envoy/latest/configuration/http/http_filters/admission_control_filter · overload manager https://www.envoyproxy.io/docs/envoy/latest/configuration/operations/overload_manager/overload_manager
- **RFC 8289, CoDel (Controlled Delay AQM):** https://www.rfc-editor.org/rfc/rfc8289
- **Dean & Barroso, "The Tail at Scale" (CACM, 2013):** https://cacm.acm.org/research/the-tail-at-scale/
- **Microsoft Azure Architecture Center, "Bulkhead pattern":** https://learn.microsoft.com/en-us/azure/architecture/patterns/bulkhead
- **GitHub incident RCA, 17 Aug 2026 (the cold open):** https://www.githubstatus.com/incidents/zkxwbgr0cnmx · **GitHub availability report, August 2026:** https://github.blog/news-insights/company-news/github-availability-report-august-2026/

Every figure and quote in these diagrams comes from those sources unless it's marked **ILLUSTRATIVE**, *simplified* or *our reading*. Values the sources give as examples keep their own hedge ("say 75%", "might cap at 25%", "about 5%").

## The operational lesson

Rate limiting and load shedding answer different questions. A per-client rate limit asks *is this client over its quota?* (fairness). Load shedding asks *am I, the server, over my capacity right now?* A per-client limit protects you from one client; load shedding protects you from everyone at once. You need both.

Without shedding, an overloaded server keeps taking work, latency crosses the client's timeout, and goodput (useful, on-time answers) collapses while throughput stays high. Retries make it a steady state. Load shedding refuses some requests so the rest succeed. Four levers:

1. **What to drop:** priority (Google's criticality levels, Netflix's Zuul buckets). Never shed the load balancer's health check.
2. **How to queue:** FIFO serves the requests most likely to be abandoned. Newest first (LIFO), with a deadline on every queue that travels with the request. CoDel's idea, time spent in queue, adapted from network packets.
3. **How much to accept:** a fixed RPS limit goes stale; limit concurrency and let the limit adapt to latency (Little's Law, Netflix Vegas / Gradient2, Envoy adaptive concurrency). Reject fast with a clear "overloaded" signal that tells clients to back off.
4. **Where:** rejecting isn't free. Shed in layers; the edge is the cheapest place to say no but knows the least. Client-side throttling (Google) / admission control (Envoy). Log what you drop.

### What load shedding does NOT solve

1. It adds no capacity (AWS: in a brownout the priority is to add capacity and address the bottleneck).
2. It can blind your autoscaler if it sheds at about the same CPU target that triggers scaling. Netflix's 2024 fix: shed only above the scaling target.
3. It can flatter your dashboards: fast rejections drag median latency down (AWS's 60% example).
4. Your priority labels can be wrong (Netflix found a low-priority request whose shedding broke playback, by testing shedding on purpose).
5. It can't fix clients that ignore the signal: that needs retry budgets and backoff with jitter (see Video #3).

Neighbors, and where they stop: bulkheads isolate (they are not shedding); hedged requests *add* load (hedge when you have headroom, shed when you don't).

## Files

Each diagram has three versions:
- an editable D2 source in `src/`;
- an SVG in `svg/` that scales to any screen;
- a PNG at 2x in `png/` for retina displays and phones.

**`ratelimit_vs_shedding`**: the two questions side by side (per-client rate limit vs load shedding), and why you need both.
[PNG](png/ratelimit_vs_shedding.png) · [SVG](svg/ratelimit_vs_shedding.svg) · [D2](src/ratelimit_vs_shedding.d2)

**`overload_over_time`**: one server under overload, minute by minute: offered load, latency vs the client timeout, throughput vs goodput, retries, then shedding on. **ILLUSTRATIVE** curve shapes, no real data.
[PNG](png/overload_over_time.png) · [SVG](svg/overload_over_time.svg) · [D2](src/overload_over_time.d2)

**`failure_dynamics`**: the channel's failure-over-time grid for a generic overload: first event, signal, shared resource, containment, trade-off, recovery (goodput holds, then load ramps back gradually).
[PNG](png/failure_dynamics.png) · [SVG](svg/failure_dynamics.svg) · [D2](src/failure_dynamics.d2)

**`inside_one_service`**: C4-style components inside one service (simplified): priority classifier → request queue → concurrency limiter.
[PNG](png/inside_one_service.png) · [SVG](svg/inside_one_service.svg) · [D2](src/inside_one_service.d2)

**`health_check_spiral`**: never shed the health check (AWS), and the spiral when overloaded servers fail health checks (Google SRE book). Two questions: process alive? / can serve this request now? Server counts ILLUSTRATIVE.
[PNG](png/health_check_spiral.png) · [SVG](svg/health_check_spiral.svg) · [D2](src/health_check_spiral.d2)

**`queue_fifo_vs_lifo`**: Google's FIFO example (queue = 10× threads, 100 ms each → 1.1 s, mostly waiting), and the fix: newest first, with a deadline on every queue (30 s → 23 s after 7 s in A, Google's example).
[PNG](png/queue_fifo_vs_lifo.png) · [SVG](svg/queue_fifo_vs_lifo.svg) · [D2](src/queue_fifo_vs_lifo.d2)

**`adaptive_limit_loop`**: from a static RPS limit (goes stale) to an adaptive concurrency limit: Little's Law, latency vs baseline, the feedback loop, Envoy's gradient formula.
[PNG](png/adaptive_limit_loop.png) · [SVG](svg/adaptive_limit_loop.svg) · [D2](src/adaptive_limit_loop.d2)

**`topology_where_to_shed`**: generic topology (simplified), client → edge → API gateway → service → database, with a shedding point on every hop; cheapest at the edge, costs visibility.
[PNG](png/topology_where_to_shed.png) · [SVG](svg/topology_where_to_shed.svg) · [D2](src/topology_where_to_shed.d2)

**`does_not_solve`**: the five limits above, each with its mitigation.
[PNG](png/does_not_solve.png) · [SVG](svg/does_not_solve.svg) · [D2](src/does_not_solve.d2)

**`bulkheads`**: compartments (Google: might cap any one client at 25% of threads; Netflix: live 90% / batch 10%). Isolation, not shedding.
[PNG](png/bulkheads.png) · [SVG](svg/bulkheads.svg) · [D2](src/bulkheads.d2)

**`hedged_request_seq`**: UML sequence, 2 replicas: send a copy after the p95 wait, use whichever answers first, cancel the other (about +5% load).
[PNG](png/hedged_request_seq.png) · [SVG](svg/hedged_request_seq.svg) · [D2](src/hedged_request_seq.d2)

## Render

`d2 --layout elk src/<file>.d2 svg/<file>.svg` (D2 v0.9.0). `src/_style.d2` holds the shared dark theme. PNG@2x and the PDF were rendered from the SVGs with headless Chrome.

## Corrections

Spotted something off? Open an issue or a pull request and cite the source. Accepted fixes are credited in [CHANGELOG.md](CHANGELOG.md).
