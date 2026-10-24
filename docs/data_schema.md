# Data schema

The demo uses four simulated CSV files in `data/`. They are a small subset of the full 11-table model of the
platform (PostgreSQL/PostGIS). Units are in the field names: `_m3` cubic metres, `_pct` percent, `_ha` hectares, `_m` metres.
An empty value means "unknown", not zero.

| Demo file | Full-model table | Fields in the demo |
| --- | --- | --- |
| `reservoirs.csv` | `reservoir` | reservoir_id, name, total_capacity_m3, lat, lon |
| `reservoir_observations.csv` | `reservoir_observation` | reservoir_id, obs_date, fill_pct, season_norm_pct, rain_mm, quality_flag |
| `settlements.csv` | `settlement` | settlement_id, name, lat, lon, population, has_critical_facility |
| `supply_links.csv` | `supply_link` | reservoir_id, settlement_id, supply_share_pct, backup_share_pct, daily_demand_m3 |

Computed in `core/logic.py` (these correspond to `forecast` and `risk_assessment` in the full model):
alert level, weeks to threshold, forecast interval, settlement exposure, risk score, risk class and rank.

Not in the demo, but in the full model: `data_source` (every number links to a source), `municipality`,
`reservoir_curve` (level, area and volume), `drought_indicator` (SPI, SPEI, SMA, FAPAR and others) and `citizen_report`.

Every simulated observation has `quality_flag = simulated`, so it cannot be mixed with real data.
