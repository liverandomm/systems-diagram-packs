# AWS us-east-1, 19–20 Oct 2025: the DNS race that emptied DynamoDB's endpoint

Diagram pack for **Systems, Drawn · Video #2**.

**License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).** Every architecture diagram in this episode is available under CC BY 4.0. Use it, adapt it, and put it in your own postmortems and talks. Credit "Systems, Drawn · SRE // microservices". See [LICENSE.md](LICENSE.md).

📄 **On a phone?** Open the whole pack as one PDF: [`systems-drawn-02-diagram-pack.pdf`](systems-drawn-02-diagram-pack.pdf).

## Primary source

**AWS, "Summary of the Amazon DynamoDB Service Disruption in the Northern Virginia (US-EAST-1) Region"**
https://aws.amazon.com/message/101925/

Every time, mechanism and AWS commitment in these diagrams comes from that summary unless marked otherwise.

Secondary source, used for nuances only: AWS re:Invent 2025 talk DAT453, "DynamoDB: Resilience & lessons from the Oct 2025 service disruption" (AWS Events on YouTube). It's the source for these points:
- Route 53 behaved normally.
- Plans are stored as JSON, with their history in S3.
- The alarm that named the cause got lost among hundreds of others.
- Automation was back on in all regions by 28 Oct 2025.

All times are **PDT (UTC−7)**, as AWS reports them, written 12-hour style as in the video (e.g. 12:38 AM). Buenos Aires time (ART) is PDT + 4 h.

## The operational lesson

A check that was true when it ran. A cleanup with nothing stopping it from deleting the live plan. A recovery path with no established procedure.

DNS was fixed in under three hours (11:48 PM → 2:25 AM PDT). EC2 needed about eleven and a half more (until 1:50 PM PDT), because recovery itself became the failure mode:
- leases expired fleet-wide;
- a queue of work timed out before it finished;
- health checks removed healthy capacity.

Static stability kept running instances healthy. It did not protect anything that had to change state.

## Files

Each diagram has three versions:
- an editable D2 source in `src/`;
- an SVG in `svg/` that scales to any screen;
- a PNG at 2x in `png/` for retina displays and phones.

**`c4_context`**: C4 level 1. Shows the DynamoDB us-east-1 regional endpoint, Route 53, and the AWS services that AWS names as depending on it, plus the second wave from NLB.
[PNG](png/c4_context.png) · [SVG](svg/c4_context.svg) · [D2](src/c4_context.d2)

**`c4_containers_dns`**: C4 level 2. Shows the DNS Planner, three independent DNS Enactors (one per AZ), the plans and Route 53. Plan generations are illustrative.
[PNG](png/c4_containers_dns.png) · [SVG](svg/c4_containers_dns.svg) · [D2](src/c4_containers_dns.d2)

**`seq_enactor_race`**: UML sequence. Shows the one-time "is my plan newer?" check, the delayed Enactor, the fast Enactor's cleanup, and the record left with zero IPs.
[PNG](png/seq_enactor_race.png) · [SVG](svg/seq_enactor_race.svg) · [D2](src/seq_enactor_race.d2)

**`c4_containers_ec2`**: C4 level 2. Shows DWFM, droplets, leases, Network Manager and NLB, and how they depend on DynamoDB.
[PNG](png/c4_containers_ec2.png) · [SVG](svg/c4_containers_ec2.svg) · [D2](src/c4_containers_ec2.d2)

**`loop_dwfm_collapse`**: the congestive-collapse loop after DynamoDB returned at 2:25 AM. Attempt lease → times out → requeue → queue grows.
[PNG](png/loop_dwfm_collapse.png) · [SVG](svg/loop_dwfm_collapse.svg) · [D2](src/loop_dwfm_collapse.d2)

**`loop_nlb_flapping`**: NLB health checks fail on healthy targets → flapping → the checker degrades → AZ DNS failover removes capacity.
[PNG](png/loop_nlb_flapping.png) · [SVG](svg/loop_nlb_flapping.svg) · [D2](src/loop_nlb_flapping.d2)

**`timeline_three_periods`**: the three overlapping periods of impact and the milestones.
[PNG](png/timeline_three_periods.png) · [SVG](svg/timeline_three_periods.svg) · [D2](src/timeline_three_periods.d2)

**`failure_dynamics`**: for each loop, shows:
- what happens first;
- the signal;
- the shared resource that runs out;
- what contained it;
- the accepted trade-off;
- how recovery avoided a second wave.

[PNG](png/failure_dynamics.png) · [SVG](svg/failure_dynamics.svg) · [D2](src/failure_dynamics.d2)

**`monday_policy`**: five channel recommendations (our opinion). Each is pinned to a moment of the night and lists what it does NOT solve.
[PNG](png/monday_policy.png) · [SVG](svg/monday_policy.svg) · [D2](src/monday_policy.d2)

Render with D2 v0.9.0 (free and open source): `d2 --layout elk src/<file>.d2 out.svg`. `failure_dynamics.d2` uses a D2 grid and also renders with the default layout.

## What each pattern does NOT solve

**Conditional write on a plan generation (a fencing token), checked by the receiver**
- Helps with: a stale writer overwriting newer state.
- Does NOT solve: a newer plan that is itself wrong. Fencing orders writes; it doesn't validate them.

**A floor under destructive automation (never delete what's active, never publish 0 targets without a human)**
- Helps with: the record going to zero.
- Does NOT solve: a plan that is bad but not empty, or a slow drain that stays above the floor.

**A synthetic DNS check that pages on zero answers or a sudden drop**
- Helps with: detecting "no error, zero answers" before customers do.
- Does NOT solve: the fix itself. It detects; it doesn't contain. It only helps if it is the page that names the cause.

**A velocity cap on capacity removal, a tested failover kill switch, and a freeze on replacements**
- Helps with: health checks removing healthy capacity during a control-plane event.
- Does NOT solve: truly dead instances, which stay down until you resume. It adds no capacity.

**Drilling fleet-wide re-establishment, dropping expired work, throttling intake, and a written recovery runbook**
- Helps with: a recovery that collapses under its own load.
- Does NOT solve: the first outage. It decides whether you get a second one, and how safely you come back.

**Static stability**
- Helps with: keeping what's already running alive.
- Does NOT solve: anything that has to change state, such as launches, leases, network config or host replacement.

## Postmortem template

[`postmortem_oncall_checklist.md`](postmortem_oncall_checklist.md) is an incident / pattern / policy template, plus an on-call checklist built from this episode.

## Corrections welcome

Found something wrong or imprecise? Open an issue or a pull request against this folder, and cite the source. Accepted corrections are listed in [CHANGELOG.md](CHANGELOG.md) with credit.

The "Monday" recommendations are the channel's own opinion, based on AWS's summary. They are not instructions from AWS.
