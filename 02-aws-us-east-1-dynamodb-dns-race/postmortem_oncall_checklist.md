# Postmortem + on-call checklist: incident → pattern → policy

A free template from *Systems, Drawn* (SRE // microservices), Video #2. License: CC BY 4.0.
Worked example in *italics*: AWS us-east-1, 19–20 Oct 2025 (source: https://aws.amazon.com/message/101925/; times PDT).
Copy it, delete the italics, fill it in for your own incident.

---

## 1. Incident: what happened, in time order

| field | your incident | *worked example* |
|---|---|---|
| Start (first customer impact) | | *23:48 PDT: DynamoDB regional endpoint resolves to zero IPs* |
| What happened first | | *a delayed DNS Enactor applied an old plan; a cleanup deleted it while it was active* |
| Signal that detected it | | *DNS answers with no addresses, no error; source identified 00:38* |
| Shared resource that ran out | | *the one regional endpoint record; later DWFM's time per lease vs. the timeout; later NLB healthy capacity* |
| What contained the spread | | *manual DNS repair; throttling + selective DWFM restarts (04:14); NLB auto failover off (09:36)* |
| Accepted trade-off | | *"request limit exceeded" for many launches; no automatic AZ failover 09:36–14:09* |
| How recovery avoided a second wave | | *throttles relaxed gradually from 11:23; failover re-enabled 14:09 after EC2 recovered* |
| Periods of impact (each with start/end) | | *DynamoDB 23:48–02:40 · EC2 launches 02:25–13:50 · NLB 05:30–14:09* |
| End (all customer impact over) | | *14:20 PDT* |
| Time to identify / to mitigate / to recover | | *50 min / 2 h 37 min (DNS) / about 14 h (EC2)* |

Timeline (one line per event, with the time zone written once at the top):

```
TZ: ____
hh:mm  event                                      source (dashboard, log, person)
```

## 2. Pattern: what shape was this?

Write contributors, not one root cause. Tick what applies and say where.

- [ ] **Stale precondition (check-then-act, TOCTOU).** Where did a decision get made against state that changed before it was carried out? *Enactor: "is my plan newer?" checked once, then applied minutes later.*
- [ ] **Destructive automation without a floor.** What could delete or empty something that was in use? *Cleanup deleted the active plan.*
- [ ] **Recovery as a failure mode.** Did coming back create more load than steady state? *Fleet-wide lease re-establishment timed out before it finished: congestive collapse.*
- [ ] **Protection system removing capacity.** Did a health check, autoscaler or failover make things worse? *NLB health checks flapped on healthy targets and triggered AZ failover.*
- [ ] **Static stability boundary.** What kept running, and what failed because it had to change state? *Running EC2 instances fine; launches, leases, network config, host replacement failed.*
- [ ] **Recovery tooling that depended on the broken thing.** *Tooling assumed the Enactor would work (re:Invent DAT453).*

Contributors (aim for 3 or more): 1. ____ 2. ____ 3. ____

## 3. Policy: what changes on Monday

One row per change. Every change names what it does NOT solve, so nobody mistakes it for the whole fix.

| moment of the incident | change | owner | due | does NOT solve |
|---|---|---|---|---|
| | | | | |
| *stale check* | *conditional write on plan generation, checked by the receiver in the same operation (fencing token); else single writer* | | | *a newer plan that is wrong* |
| *cleanup* | *never delete what's active; never publish 0 targets (or far below last size) without a human; hard check that pages* | | | *a bad but non-empty plan* |
| *identification* | *synthetic check resolving critical dependency hostnames; page on zero answers or sudden drop; make it the page that names the cause* | | | *the fix itself* |
| *capacity removed* | *cap removals per interval; tested kill switch for auto failover; freeze replacements and scale-in during a regional control-plane event; leave scale-out on* | | | *truly dead instances; adds no capacity* |
| *dependency returns* | *drill fleet-wide re-establishment vs. timeout; drop expired work; written runbook: throttle intake, drain queue, restart order, steps that need a control plane* | | | *the first outage* |

---

## On-call checklist (print it, keep it next to the runbook)

**When a dependency stops answering**
- [ ] Is it an error, or an empty answer? Check the answer count, not only the status (`dig +short <hostname>` returning nothing is a signal).
- [ ] Which of my systems need to *change state* right now (launch, lease, scale, replace)? Those are the ones at risk, even if everything running looks fine.
- [ ] Freeze anything that removes or replaces healthy instances: health-check replacements, scale-in, rebuild workflows. Leave scale-out on and expect it to fail or be throttled.
- [ ] Is a protection system (health check, failover, autoscaler) removing capacity? If it's flapping, is there a tested switch to turn it off?

**When the dependency comes back**
- [ ] Will everything re-establish at once? Throttle intake before the stampede, not after.
- [ ] Drop queued work that has already expired instead of retrying it.
- [ ] Restart in a known order; write down which steps need a control plane that might still be impaired.
- [ ] Relax throttles gradually and watch for a second wave before declaring recovery.
- [ ] Re-enable automatic failover only after the underlying service is stable.

**After**
- [ ] Fill in sections 1–3 above within a week, while the timeline is fresh.
- [ ] Every policy row has an owner, a date and a "does NOT solve" line.
- [ ] Share the diagram. Corrections from others make it better.
