# Changelog

Community corrections are welcome: open an issue or a pull request against this folder and cite the source (AWS's post-event summary, an AWS talk, or your own documented experience). Each accepted correction is listed here with credit.

Format: `YYYY-MM-DD · file(s) · what changed · why / source · credit`

## Unreleased

- Nothing yet. Yours could be the first entry.

## 2026-10-03 · first release

- Nine D2 diagrams (see README), SVG and PNG@2x renders, and `postmortem_oncall_checklist.md`.
- Primary source: AWS post-event summary, https://aws.amazon.com/message/101925/. Nuances from AWS re:Invent 2025 DAT453: Route 53 behaved normally; plans stored as JSON with history in S3; the alarm that pointed at the cause was lost among hundreds; fix validated 22 Oct and automation back on in all regions by 28 Oct 2025.
- Known simplifications: plan generations, queue sizes and load levels are illustrative (AWS publishes no such figures); Enactor internals are drawn at the level of detail AWS describes, no further.
