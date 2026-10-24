# Drought early warning: demo

A small, runnable demo of an early-warning platform for reservoirs and the settlements that depend on them.
It was built for EIT Water Hackathon Sofia 2026.

> **All data in this repository are simulated.** Reservoir and village names, locations and numbers are invented.
> The demo is not a hydrological model and must not be used for operational decisions.

## What it shows

1. **Reservoir status.** Fill against the seasonal norm, rate of decline, weeks to a critical threshold (with a range).
2. **Traffic light.** Green, yellow, orange or red from transparent, tunable rules.
3. **Trend forecast.** A linear trend with an interval from the fastest and slowest recent decline.
4. **Ranked settlements.** Risk from how much each settlement depends on reservoirs in alert, after backup sources.
5. **Plain-language explanation.** Built from the computed numbers. A language model could reword it, but it never
   changes a number or a class.

## Run it

```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
streamlit run app.py
```

The app opens at http://localhost:8501. Use the sidebar to change the critical threshold and the number of weeks
used for the trend, and watch the alert levels and the ranking change.

To regenerate the simulated data: `python generate_data.py`. To run the tests: `pytest`.

## Repository layout

| Path | What it is |
| --- | --- |
| `app.py` | Streamlit interface: map, reservoirs, settlements at risk, about |
| `core/logic.py` | The rules: reservoir status, traffic light, forecast, settlement risk, explanation |
| `data/` | Simulated CSV files (reservoirs, observations, settlements, supply links) |
| `generate_data.py` | Creates the files in `data/` |
| `tests/test_logic.py` | Unit tests for the rules |
| `docs/data_schema.md` | How the demo files map to the full 11-table data model |
| `.github/workflows/ci.yml` | Runs the tests on every push and pull request |

## The rules

- **Deviation** D = fill − seasonal norm, in percentage points (pp).
- **Weeks to threshold** W = (fill − critical fill) / weekly decline. No crossing is reported when the reservoir is not falling.
- **Traffic light** (illustrative defaults):

| Level | Condition |
| --- | --- |
| Green | none of the below |
| Yellow | D < −10 pp, or W ≤ 12 weeks |
| Orange | D ≤ −25 pp, or W < 8 weeks |
| Red | W < 4 weeks, or fill ≤ critical + 5 pp |

- **Settlement exposure** = sum over reservoirs of supply share × (1 − backup share) × alert factor
  (green 0, yellow 0.4, orange 0.7, red 1), capped at 1.
- **Settlement score** = 100 × exposure × (0.7 + 0.2 × population term + 0.1 × critical facility).
  Classes: high ≥ 50, medium ≥ 25, otherwise low. A settlement that depends only on green reservoirs scores 0.

All thresholds and weights are illustrative. In a real pilot they are agreed with the reservoir operator, the water
utility and the basin directorate.

## Limits

- The forecast is a trend, not a model of inflow, rainfall or evaporation.
- Level and volume from satellite images need the reservoir's level–area–volume curve; this demo uses fill in percent only.
- Results should always be shown with their interval and their source.

## Next steps for a pilot

1. Replace `data/` with real observations (Sentinel-1 and Sentinel-2 surface area, operator data, sensors).
2. Add the level–area–volume curve per reservoir and compute volume from it.
3. Add drought indices (SPI, SPEI, soil moisture, FAPAR) from Copernicus and EDO.
4. Agree thresholds and the supply links with the operator.

## Publish it on GitHub

```bash
git init
git add .
git commit -m "Drought early warning demo"
git branch -M main
git remote add origin https://github.com/<your-user>/drought-early-warning-demo.git
git push -u origin main
```

To host the app for free, deploy it from the repository on Streamlit Community Cloud
(choose `app.py` as the main file). Add a licence file of your choice (for example MIT) before making the repository public.
