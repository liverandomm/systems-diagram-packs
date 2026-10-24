# V6 diagram pack · CHANGELOG

## v1.0 — Sat 24/10/2026 (approved by Claudio, 24/10 19:26 ART)
- Release of v0.1.1 content. README DRAFT note removed. PDF unchanged (it carried no DRAFT marking).

## v0.1.1 DRAFT — Sat 24/10/2026 (Red Team: approve with 4 minors; not published, not pushed)
- m1: `src/*.d2` in the pack no longer carry internal production comments (full-line `#` stripped in `build_pack.py`; `video06/diagrams/src` untouched). Check: D2 output of stripped vs original sources byte-identical 16/16; pack SVG/PNG unchanged. README hidden RT comment removed; no other HTML comments.
- m2: source URLs added to README from the local copies' metadata: PagerDuty SRE Agent GA = `<link rel="canonical">` of `research_data/pagerduty_sre_agent_ga.html`; Datadog Bits AI SRE PR = `og:url` of `research_data/datadog_bits_ai_sre_pr.html`.
- m3: LICENSE.md and page 18: "share and adapt these diagrams".
- m4: clickable link annotations (pypdf, `add_links.py`): page 18 license URL + 9 source names; footers of the 9 single-source scenes (d0a, d2a, d2b, d3b, d4a, d4b, d6a, d6b, d6c). Multi-source footers (d3a, d5, d1, d1b, d6d) have no link. README notes the PDF is image-based.
- Grafana Labs kept: d6d cites grafana.com/observability-survey/2026.
- Pages 2–17 image streams byte-identical to v0.1. Built under `nice -n 19`.

## v0.1 DRAFT — Sat 24/10/2026 (not published, not pushed; pending YT Red Team + Claudio)
- New pack modelled on the V5 pack: README.md, LICENSE.md (CC BY 4.0), src/ svg/ png/, `systems-drawn-06-diagram-pack.pdf` (18 pages: cover + 16 scenes + License page), `build_pack.py`.
- Content = the 16 approved v0.2.2 scenes from `video06/diagrams/`, copied byte-identical (src, svg, png_2x → png; md5 checked in the build). No visible text changed, no re-render.
- New text for Red Team: cover, page headers (scene id + script block), README prose, LICENSE attribution and sources/trademark paragraph (adapted from V5).
- Built under `nice -n 19`, one render at a time.
