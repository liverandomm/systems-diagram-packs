# Resilience patterns diagram pack (Video #1)
Editable D2 sources (`src/`, shared style in `_style.d2`), SVG (`svg/`) and PNG@2x (`png/`).
Render: `d2 --layout elk src/<file>.d2 out.svg` (D2 v0.9.0, ELK layout, free/open source).
License: CC BY 4.0.
| file | what it shows |
|---|---|
| s01_fanin | 20 services fanning into one database (cascading failure topology) |
| s03_chain | one request path: users → checkout → payments → database |
| s06_deadline | deadline propagation 30s → 23s → 19s |
| s07_retry_layers | retry at one layer only; upper layers propagate "overloaded, don't retry" |
| s11_breaker_states | circuit breaker state machine (closed/open/half-open) |
| s12_breaker_scope | per-shard breakers instead of one cluster-wide breaker |
| s14_policy | the full resilience policy: deadline, timeouts, single retry layer, breaker, fallback |
