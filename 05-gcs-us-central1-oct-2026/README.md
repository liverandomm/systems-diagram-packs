# Google Cloud Storage incident: write errors in us-central1, 8–9 Oct 2026

Diagram pack for **Systems, Drawn · Video #5**.

> **DRAFT v0.5.2, not published.** Pending YT Red Team review and Claudio's final approval. Remove this note at release.

**License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).** Every diagram in this episode is available under CC BY 4.0. Use it, adapt it, and put it in your own runbooks, postmortems, design reviews and talks. Credit "Systems, Drawn · SRE // microservices". See [LICENSE.md](LICENSE.md).

📄 **On a phone?** Open the full pack as one PDF: [`systems-drawn-05-diagram-pack.pdf`](systems-drawn-05-diagram-pack.pdf).

## Primary sources

- **Google Cloud Service Health, incident `mSFVt6PiP557bjRyYsut`:** https://status.cloud.google.com/incidents/mSFVt6PiP557bjRyYsut — five live updates (8 Oct 2026, 11:24–13:48 PT), the preliminary report (9 Oct 2026, 09:44 PT) and the incident report (14 Oct 2026, 00:15 PT).
- **Google Cloud Storage product history page** ("1 day, 6 hours"): https://status.cloud.google.com/products/UwaYoXQ5bHYHG6EdiPB8/history

Every time, duration, name and quote in these diagrams comes from those pages unless it's marked *our arithmetic*, *our simplification*, *illustrative* or *simplified*. Times are US/Pacific (PT, UTC−7 on these dates), as Google reports them; ART = PT + 4 h. Google publishes no internal topology: every box is a component Google's report names, but the placement and the arrows are ours. Where Google doesn't say something (how the traffic sorting optimization works, how capacity-aware routing decides, when each mitigation step happened, how the three names in the rollback relate), the diagrams say so instead of filling the gap.

## The operational lesson

On 8 Oct a configuration change meant to ease a capacity shortage, the *traffic sorting optimization*, inadvertently bypassed the standard capacity-aware routing logic. A small fraction of storage backend nodes with low serving capacity received an overwhelming proportion of incoming write traffic, and customers primarily saw HTTP 503 UNAVAILABLE on WriteObject plus client-side timeouts. The first window (12 h 30 min) was mitigated at 16:30 PT. The next morning the write errors came back in zone us-central1-b for 5 h 41 min: Google says the recurrence was caused by "an incomplete rollback of an upload planner feature".

Google's report lists three preventive actions: **Safe Configuration Rollouts**, **Intelligent Operational Controls** and **Resilient Scaling Logic**. Our recommendation (the channel's, not Google's): a rollback is done when every changed part is verified back, not when the command returns.

### What this does NOT solve

1. Routing guardrails don't create capacity: here even emergency capacity for the region was blocked.
2. A rollback checklist doesn't help if nobody owns the second component the change touched.
3. A provider's status page is a communication channel, not an alert: your own write error rates, per region and per zone, are what wake you up.

## Files

Each diagram has three versions:
- an editable D2 source in `src/`;
- an SVG in `svg/` that scales to any screen;
- a PNG at 2x in `png/` for retina displays and phones.

Shared imports: `src/_style.d2` (the pack's dark theme), `src/_style_v05.d2` (V5 classes: `blackbox`, `discrepancy`, edge classes `req`, `req_hot`, `fail`, `unknown`), `src/_strip_oct8_9.d2` (the to-scale strip shared by the three timelines: 60 px = 1 h; two windows with a grey gap, never one bar).

**`two_windows_b01`**: the cold open: "12h30m of write errors" (us-central1 · 8 Oct) and "Mitigated… then back: 5h41m · us-central1-b only · 9 Oct". Two bars at the same scale (60 px = 1 h) with a grey gap, never one 18-hour bar. Every SVG group has an `id` (bar_oct8, gap, bar_oct9…) for animation.
[PNG](png/two_windows_b01.png) · [SVG](svg/two_windows_b01.svg) · [D2](src/two_windows_b01.d2)

**`topology_capacity_aware`**: normal behavior, simplified: customers → GCS load balancing layer (standard capacity-aware routing logic) → storage backend nodes; more writes where there is room, less traffic (not zero) where serving capacity is low. Node count and arrow weights are illustrative.
[PNG](png/topology_capacity_aware.png) · [SVG](svg/topology_capacity_aware.svg) · [D2](src/topology_capacity_aware.d2)

**`topology_oct8_bypassed`**: 8 Oct inside the us-central1 region: the traffic sorting optimization (a black box: Google doesn't describe how it works) bypasses capacity-aware routing; a small fraction of nodes takes "an overwhelming proportion of incoming write traffic"; HTTP 503 UNAVAILABLE on WriteObject + client-side timeouts.
[PNG](png/topology_oct8_bypassed.png) · [SVG](svg/topology_oct8_bypassed.svg) · [D2](src/topology_oct8_bypassed.d2)

**`capacity_blocked`**: mitigation step 1, "attempted for the region · blocked": emergency capacity "blocked by systemic regional capacity limits". The arrow ends at the region border, because Google names the region, not nodes.
[PNG](png/capacity_blocked.png) · [SVG](svg/capacity_blocked.svg) · [D2](src/capacity_blocked.d2)

**`routing_decision`**: the same write, two paths. Left: capacity-aware routing as a decision diamond (how it decides: not published, our simplification). Right: 8 Oct, the black box.
[PNG](png/routing_decision.png) · [SVG](svg/routing_decision.svg) · [D2](src/routing_decision.d2)

**`timeline_oct8_morning`**: 04:00 impact start, 04:50 detected (internal telemetry), 05:19 as the live updates said (both reports: 04:00), 10:57 first improvement, 11:24 first status-page update; 6 h 34 min from detection to that update (our arithmetic).
[PNG](png/timeline_oct8_morning.png) · [SVG](svg/timeline_oct8_morning.svg) · [D2](src/timeline_oct8_morning.d2)

**`timeline_oct8_afternoon`**: 13:48 last live update (next entry 19 h 56 min later), 16:30 first window ends, mitigated (Google: "provided temporary relief"); the four mitigation steps in the order Google lists them, times not published, step 1 crossed out (blocked).
[PNG](png/timeline_oct8_afternoon.png) · [SVG](svg/timeline_oct8_afternoon.svg) · [D2](src/timeline_oct8_afternoon.d2)

**`timeline_oct9`**: 19 h 56 min with no status update; 04:05 the write errors are back in us-central1-b; 09:44 preliminary report (marked Available, covers 8 Oct only); 09:46 recurrence ends; 14 Oct final report. 18 h 11 min in two windows (per Google) vs "1 day, 6 hours" wall clock (product history).
[PNG](png/timeline_oct9.png) · [SVG](svg/timeline_oct9.svg) · [D2](src/timeline_oct9.d2)

**`incomplete_rollback`**: three names in Google's report, drawn as three boxes side by side, with no arrows and no common frame, in the order the video tells them; each starts with its own date: disable the traffic sorting optimization (8 Oct · us-central1 region) · the fully reverted load balancing configuration change (date NOT published) · the "upload planner" feature, cause of the recurrence (recurrence: 9 Oct · 04:05–09:46 PT · us-central1-b), with Google's short quote "an incomplete rollback of an upload planner feature". Below: Google doesn't say how the three relate.
[PNG](png/incomplete_rollback.png) · [SVG](svg/incomplete_rollback.svg) · [D2](src/incomplete_rollback.d2)

## Render

`d2 --layout elk src/<file>.d2 svg/<file>.svg` (D2 v0.9.0). PNG@2x and the PDF were rendered from the SVGs with headless Chrome. Labels are sized for a 1920×1080 frame: key labels ≥ 36 px, secondary text ≥ 28 px, footers ≥ 22 px.

## Corrections

Spotted something off? Open an issue or a pull request and cite the source. Accepted corrections are credited in [CHANGELOG.md](CHANGELOG.md).
