# AI SRE Agents on 3 Real Incidents: Where They'd Help, Where They'd Hurt

Diagram pack for **Systems, Drawn · Video #6**.

**License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).** Credit "Systems, Drawn · SRE // microservices". See [LICENSE.md](LICENSE.md).

📄 **On a phone?** Open the full pack as one PDF: [`systems-drawn-06-diagram-pack.pdf`](systems-drawn-06-diagram-pack.pdf).

## Primary sources

- AWS post-event summary, Oct 2025: https://aws.amazon.com/message/101925/
- GitHub RCA, 17 Aug 2026: https://www.githubstatus.com/incidents/zkxwbgr0cnmx · availability report Aug 2026: https://github.blog/news-insights/company-news/github-availability-report-august-2026/
- Google Cloud Service Health, GCS incident Oct 2026: https://status.cloud.google.com/incidents/mSFVt6PiP557bjRyYsut
- New Relic, 2026 Observability Forecast (vendor, with ETR), PDF pp. 2, 6, 7, 25: https://newrelic.com/sites/default/files/2026-09/new-relic-2026-observability-forecast-report.pdf
- Catchpoint, The SRE Report 2026 (vendor), p. 36 printed / p. 37 PDF: https://resources.catchpoint.com/hubfs/Website%20Assets%20-%20Briefs%2c%20EBooks%2c%20etc/The%20SRE%20Report%202026%20Catchpoint.pdf
- PagerDuty AI Resilience Survey 2025: https://www.pagerduty.com/newsroom/ai-resilience-survey-2025/ · method: https://www.pagerduty.com/resources/insights/learn/executives-adopting-ai-agents/
- PagerDuty 2026 State of AI-First Operations: https://www.pagerduty.com/blog/digital-operations/2026-state-of-ai-first-operations-report/
- Grafana Labs Observability Survey 2026: https://grafana.com/observability-survey/2026/
- Devin Oncall (Cognition): https://devin.ai/blog/introducing-devin-oncall
- PagerDuty SRE Agent GA (30 Oct 2025): https://support.pagerduty.com/main/changelog/sre-agent-is-generally-available
- Datadog Bits AI SRE press release (2 Dec 2025): https://www.datadoghq.com/about/latest-news/press-releases/datadog-launches-bits-ai-sre-agent-to-resolve-incidents-faster/

Every figure on a diagram carries its source on that diagram. Anything marked *our analysis*, *our framing*, *our arithmetic*, *our summary*, *illustrative* or *simplified* is the channel's, not the source's. Times: AWS and GCS in PDT (UTC−7) as reported, ART = PDT + 4 h; GitHub in UTC. Vendor surveys and vendor claims are quoted as such, not as measurements.

## Diagrams (order of the video, script blocks)

- **`d0a_opening_pager`** (B01): [PNG](png/d0a_opening_pager.png) · [SVG](svg/d0a_opening_pager.svg) · [D2](src/d0a_opening_pager.d2)
- **`d0b_opening_three_incidents`** (B01): [PNG](png/d0b_opening_three_incidents.png) · [SVG](svg/d0b_opening_three_incidents.svg) · [D2](src/d0b_opening_three_incidents.d2)
- **`d2a_aws_dns_race`** (B02 · D2): [PNG](png/d2a_aws_dns_race.png) · [SVG](svg/d2a_aws_dns_race.svg) · [D2](src/d2a_aws_dns_race.d2)
- **`d2b_aws_recovery`** (B02 · D2): [PNG](png/d2b_aws_recovery.png) · [SVG](svg/d2b_aws_recovery.svg) · [D2](src/d2b_aws_recovery.d2)
- **`d3b_github_block_403`** (B03 · D3): [PNG](png/d3b_github_block_403.png) · [SVG](svg/d3b_github_block_403.svg) · [D2](src/d3b_github_block_403.d2)
- **`d4a_gcs_capacity_blocked`** (B04 · D4): [PNG](png/d4a_gcs_capacity_blocked.png) · [SVG](svg/d4a_gcs_capacity_blocked.svg) · [D2](src/d4a_gcs_capacity_blocked.d2)
- **`d4b_gcs_two_windows`** (B04 · D4): [PNG](png/d4b_gcs_two_windows.png) · [SVG](svg/d4b_gcs_two_windows.svg) · [D2](src/d4b_gcs_two_windows.d2)
- **`d1_ai_sre_loop`** (B06 · D1): [PNG](png/d1_ai_sre_loop.png) · [SVG](svg/d1_ai_sre_loop.svg) · [D2](src/d1_ai_sre_loop.d2)
- **`d1b_vendor_claims`** (B06): [PNG](png/d1b_vendor_claims.png) · [SVG](svg/d1b_vendor_claims.svg) · [D2](src/d1b_vendor_claims.d2)
- **`d6a_survey_newrelic`** (B07): [PNG](png/d6a_survey_newrelic.png) · [SVG](svg/d6a_survey_newrelic.svg) · [D2](src/d6a_survey_newrelic.d2)
- **`d6c_survey_pagerduty25`** (B07): [PNG](png/d6c_survey_pagerduty25.png) · [SVG](svg/d6c_survey_pagerduty25.svg) · [D2](src/d6c_survey_pagerduty25.d2)
- **`d6d_survey_b08`** (B08): [PNG](png/d6d_survey_b08.png) · [SVG](svg/d6d_survey_b08.svg) · [D2](src/d6d_survey_b08.d2)
- **`d7_b13_no_independent_data`** (B13): [PNG](png/d7_b13_no_independent_data.png) · [SVG](svg/d7_b13_no_independent_data.svg) · [D2](src/d7_b13_no_independent_data.d2)

Shared imports: `src/_style.d2`, `src/_style_v05.d2`, `src/_style_v06.d2`.

## Render

`d2 --layout elk src/<file>.d2 svg/<file>.svg`. PNG@2x (3840×2160) rendered from the SVGs with headless Chrome; the PDF (`build_pack.py`) embeds those PNGs unchanged. The PDF pages are images; clickable links (`add_links.py`) cover the license URL and the source names on the License page, and the footer of each diagram that cites a single source. Every URL is also listed above. Labels are sized for a 1920×1080 frame: key ≥ 36 px, secondary ≥ 28 px, footers ≥ 22 px.

## Corrections

Spotted something off? Open an issue or a pull request and cite the source. Accepted corrections are credited in [CHANGELOG.md](CHANGELOG.md).
