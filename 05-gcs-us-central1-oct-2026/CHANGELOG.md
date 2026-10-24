# Changelog

Community corrections are welcome: open an issue or a pull request against this folder and cite the source (Google's incident page or product history page, or your own documented experience). Each accepted correction is listed here with credit.

Format: `YYYY-MM-DD · file(s) · what changed · why / source · credit`

## Unreleased

- 2026-10-17 · src/capacity_blocked.d2 · v0.5.3: src comment: Google quote corrected to the literal text ("Engineers initially attempted to dynamically allocate additional emergency capacity to the affected region, but this was blocked by systemic regional capacity limits.") · Red Team M1 (V6 diagrams review) · SVG, PNG and PDF regenerated; visible text unchanged (text diff of the 9 SVGs = 0; capacity_blocked.png pixel-identical) · not released, not pushed.
- 2026-10-17 · all · DRAFT v0.5.2 of the pack, built from the V5 sketch v0.5.2 (pending Red Team and Claudio) · not released, not pushed.
  Nine D2 diagrams (see README), SVG and PNG@2x renders, and `systems-drawn-05-diagram-pack.pdf` (cover + one page per diagram, for phones).
  Red Team review of research and script applied before release: 16:30 drawn as "mitigated" (Google: "temporary relief"); two windows with a grey gap, never one 18-hour bar; the incomplete rollback as three separate boxes with no arrows; Google's rollback sentence verbatim; the 19 h 56 min gap card worded "none mentioned the recurrence"; "first status-page update" for 11:24.
  v0.5.2: incomplete_rollback amber box "⚠ Google doesn't say how the three relate"; upload planner box dated "recurrence: 9 Oct · 04:05–09:46 PT" / "us-central1-b". Red Team: approved (2026-10-17 ~18:57 ART, render 18:41); pending Claudio (final approval + platform). Pack rebuilt 18:42 ART from that render.
  v0.5.1: incomplete_rollback back to three boxes side by side (narration order: disable · full revert · upload planner), date as the first line of each box, short quote only in the upload planner box; "Incident End" line on the 16:30 card without the stray "·".
  v0.5 (Red Team review of v0.4/v0.4.1): D4 boxes each carry their own date, no common 9 Oct frame; 16:30 card amber ("temporary relief"), not green; 05:19 card "LIVE UPDATES SAID"; one legend for amber dashed ("the record disagrees or has a gap"); step 1 crossed out; "ingestion timing out" on the topology; "us-central1-b only" as a key label on 9 Oct.
  v0.4.1: added `two_windows_b01` (B01 cold-open frame, SVG groups with ids); all times labelled PT (+ ART).
  Known simplifications: the topology is generic (Google publishes none); node counts, arrow weights and the routing diamond are illustrative / our simplification; card spacing on the timelines is not to scale (the strip is).
