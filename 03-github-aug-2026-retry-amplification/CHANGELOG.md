# Changelog

Community corrections are welcome: open an issue or a pull request against this folder and cite the source (GitHub's RCA, availability report, or your own documented experience). Each accepted correction is listed here with credit.

Format: `YYYY-MM-DD · file(s) · what changed · why / source · credit`

## Unreleased

- Nothing yet. Yours could be the first entry.

## 2026-10-03 · first release

- Seven D2 diagrams (see README), SVG and PNG@2x renders, and `systems-drawn-03-diagram-pack.pdf` (the whole pack in one file for phones).
- Primary sources: githubstatus.com incident zkxwbgr0cnmx (RCA and updates); GitHub availability report, August 2026; GitHub CTO blog, 20 Aug 2026.
- Known simplifications: the hop order in `seq_retry_two_layers` is didactic (GitHub doesn't publish the exact call path); attempt counts (×3), the 10% retry budget and gauge levels are illustrative. "Why stopping nodes at the same time matters" and "why a 403 helps" are our reading, not GitHub's statements.
