# Changelog

Community corrections are welcome: open an issue or a pull request against this folder and cite the source (GitHub's RCA, availability report, or your own documented experience). Each accepted correction is listed here with credit.

Format: `YYYY-MM-DD · file(s) · what changed · why / source · credit`

## Unreleased

- Nothing yet. Yours could be the first entry.

## 2026-10-03 · Red Team corrections (before the video goes live)

- 2026-10-03 · `topology_shared_auth` (D2, SVG, PNG, PDF page) · added the caption "simplified · GitHub doesn't publish this topology (hop order, fan-in and node count are our drawing from the RCA)"; README says the same · GitHub's RCA and availability report describe what was shared, not the exact topology · credit: Systems, Drawn Red Team review
- 2026-10-03 · `failure_dynamics` · step 6 is now "recovery: block the loop, then ramp back slowly" (was "recovery without a second wave") · a gradual ramp lowers the risk of a second wave but doesn't rule it out; GitHub blocked the token requests with a 403 and cut retries before ramping (githubstatus.com RCA; GitHub CTO blog, 20 Aug 2026) · credit: Red Team review
- 2026-10-03 · `monday_policy` · item 6 "does NOT solve" now reads "slower by design: users wait longer. Lowers, doesn't remove, second-wave risk: clients stuck in a retry loop must be blocked first" · same reason as above · credit: Red Team review
- 2026-10-03 · `aug06_backlog` · "then retries on work that couldn't succeed" is now "then retries adding load faster than the system could clear it" · on Aug 17 retrying often worked (GitHub availability report); the old line only fits Aug 6 · credit: Red Team review
- 2026-10-03 · README · the recovery summary no longer says "so there was no second wave"

## 2026-10-03 · first release

- Seven D2 diagrams (see README), SVG and PNG@2x renders, and `systems-drawn-03-diagram-pack.pdf` (the whole pack in one file for phones).
- Primary sources: githubstatus.com incident zkxwbgr0cnmx (RCA and updates); GitHub availability report, August 2026; GitHub CTO blog, 20 Aug 2026.
- Known simplifications: the hop order in `seq_retry_two_layers` is didactic (GitHub doesn't publish the exact call path); attempt counts (×3), the 10% retry budget and gauge levels are illustrative. "Why stopping nodes at the same time matters" and "why a 403 helps" are our reading, not GitHub's statements.
