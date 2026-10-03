# AWS us-east-1, 19–20 Oct 2025: the DNS race that emptied DynamoDB's endpoint (Video #2)

**License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).** Every architecture diagram in this episode is available under CC BY 4.0: use it, adapt it, put it in your own postmortems and talks, with attribution ("Systems, Drawn · SRE // microservices"). See [LICENSE.md](LICENSE.md).

Editable D2 sources (`src/`, shared style in `_style.d2`), SVG (`svg/`) and PNG@2x (`png/`).
Render: `d2 --layout elk src/<file>.d2 out.svg` (D2 v0.9.0, free and open source). `failure_dynamics.d2` uses a D2 grid and renders with the default layout too.

## Primary source

- **AWS, "Summary of the Amazon DynamoDB Service Disruption in the Northern Virginia (US-EAST-1) Region"**: https://aws.amazon.com/message/101925/ (every time, mechanism and AWS commitment in these diagrams comes from here unless marked otherwise).
- Secondary, for nuances only: AWS re:Invent 2025 talk DAT453, "DynamoDB: Resilience & lessons from the Oct 2025 service disruption" (AWS Events on YouTube). Used for: Route 53 behaved normally, plans stored as JSON with history in S3, the alarm that named the cause got lost in the noise, and automation back on in all regions by 28 Oct 2025.
- All times are **PDT (UTC−7)**, as AWS reports them. Buenos Aires time (ART) = PDT + 4 h.

## The operational lesson, in one paragraph

A check that was true when it ran, a cleanup with nothing stopping it from deleting the live plan, and a recovery path with no established procedure. DNS was fixed in under three hours (23:48 → 02:25 PDT); EC2 needed about eleven and a half more (until 13:50 PDT), because recovery itself became the failure mode: leases expiring fleet-wide, a queue of work that timed out before it finished, and health checks that removed healthy capacity. Static stability kept running instances healthy; it did not protect anything that had to change state.

## Files

| D2 file | what it shows |
|---|---|
| `c4_context.d2` | C4 level 1: the DynamoDB us-east-1 regional endpoint, Route 53, and the AWS services AWS names as depending on it; the second wave from NLB |
| `c4_containers_dns.d2` | C4 level 2: DNS Planner, three independent DNS Enactors (one per AZ), plans, Route 53 (plan generations illustrative) |
| `seq_enactor_race.d2` | UML sequence: the one-time "is my plan newer?" check, the delayed Enactor, the fast Enactor's cleanup, the record left with zero IPs |
| `c4_containers_ec2.d2` | C4 level 2: DWFM, droplets, leases, Network Manager, NLB and their dependency on DynamoDB |
| `loop_dwfm_collapse.d2` | the congestive-collapse loop after DynamoDB returned (02:25): attempt lease → times out → requeue → queue grows |
| `loop_nlb_flapping.d2` | NLB health checks failing on healthy targets → flapping → checker degraded → AZ DNS failover removing capacity |
| `timeline_three_periods.d2` | the three overlapping periods of impact and the milestones (PDT) |
| `failure_dynamics.d2` | for each loop: what happens first, the signal, the shared resource that runs out, what contained it, the accepted trade-off, how recovery avoided a second wave |
| `monday_policy.d2` | five channel recommendations [OPINION], each pinned to a moment of the night, each with what it does NOT solve |

## What each pattern does NOT solve

| pattern | helps with | does NOT solve |
|---|---|---|
| Conditional write on a plan generation (fencing token), checked by the receiver | a stale writer overwriting newer state | a newer plan that is itself wrong (fencing orders writes, it doesn't validate them) |
| Floor under destructive automation (never delete what's active, never publish 0 targets without a human) | the record going to zero | a plan that is bad but not empty, or a slow drain that stays above the floor |
| Synthetic DNS check paging on zero answers / sudden drop | detecting "no error, zero answers" before customers do | the fix; it detects, it doesn't contain. It only helps if it is the page that names the cause |
| Velocity cap on capacity removal + tested failover kill switch + freeze on replacements | health checks removing healthy capacity during a control-plane event | truly dead instances (they stay until you resume); it adds no capacity |
| Drill fleet-wide re-establishment, drop expired work, throttle intake, written recovery runbook | the recovery collapsing under its own load | the first outage; it decides whether you get a second, and how safely you come back |
| Static stability | keeping what's already running alive | anything that has to change state: launches, leases, network config, host replacement |

## Postmortem template

[`postmortem_oncall_checklist.md`](postmortem_oncall_checklist.md): an incident / pattern / policy template plus an on-call checklist built from this episode.

## Corrections welcome

Found something wrong or imprecise? Open an issue or a pull request against this folder, citing the source. Accepted corrections are listed in [CHANGELOG.md](CHANGELOG.md) with credit.

Recommendations marked [OPINION] are the channel's, derived from AWS's summary. They are not AWS's instructions.
