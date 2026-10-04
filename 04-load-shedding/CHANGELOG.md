# Changelog

Community corrections are welcome: open an issue or a pull request against this folder and cite the source (AWS Builders' Library, the Google SRE book, Netflix, Envoy docs, or your own documented experience). Each accepted correction is listed here with credit.

Format: `YYYY-MM-DD · file(s) · what changed · why / source · credit`

## Unreleased

- Nothing yet. Yours could be the first entry.

## 2026-10-04 · first release

- Eleven D2 diagrams (see README), SVG and PNG@2x renders, and `systems-drawn-04-diagram-pack.pdf` (cover + one page per diagram, for phones).
- Script already passed an independent Red Team review (v1 → v2.1) before the diagrams were drawn; its fixes are built in: "per-client" rate limit scope; GitHub's 403 presented as *our reading* (GitHub doesn't call it load shedding); "the ones left overload faster" (not "the healthy ones"); CoDel described as an adaptation from network packets; the "1 per window" step only on Vegas; "tell clients to back off, not retry".
- Known simplifications: curve shapes, server counts, the 1 s client timeout, the 70% CPU marks and the log line are ILLUSTRATIVE; `topology_where_to_shed` and `inside_one_service` are generic and simplified, not any company's real topology. "A server that's shedding is still serving", "hedge when you have headroom, shed when you don't" and "shedding chooses who fails" are our opinion.
