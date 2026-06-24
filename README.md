# Ukraine Air-Raid Alerts — Time Series Analysis

A beginner-friendly, fully reproducible time-series analysis of air-raid alerts across Ukraine, from the start of the full-scale invasion (25 Feb 2022) through 23 Jun 2026. It asks **when** alerts happen, **where** they concentrate, whether the pattern is **seasonal or trend-driven**, and **projects alert volume to the end of 2026**.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/PavloLoiek/ukraine-air-raid-alerts/blob/main/air_raid_analysis.ipynb)

## TL;DR — key findings

- **101,705 alerts** across **25 regions**, 25 Feb 2022 – 23 Jun 2026.
- **2026 is the most intense period of the entire war.** The busiest single month is **May 2026 (3,402 alerts)** — above the 2022 invasion peak (2,084/mo).
- **The war has no weekly rhythm.** Thursday (15,103) is only ~14% above the quietest day, Sunday (13,231); attacks don't respect weekends.
- **Alert *starts* peak at midday** (12:00, 5,482) and bottom out in the early morning (06:00, 2,776) — but this counts when alerts *begin*, not hours spent under alert (see caveat).
- **Biggest targets are frontline east/south oblasts:** Dnipropetrovsk (11.6%), Kharkiv (10.9%), Zaporizhzhia (8.2%), Donetsk (8.0%), Sumy (7.6%). The capital, **Kyiv City, is far down the list (2,299 alerts)**.
- **It's a trend, not a season.** A Prophet decomposition shows the trend swinging ~46→133 alerts/day, dwarfing weekly (±~6) and yearly (±~12) effects.
- **Projection:** if the recent escalation continues, **~136 alerts/day by end of 2026** (range 109–164). This is an extrapolation, not a prediction (see caveats).

## What's in this repo

| File | What it is |
|---|---|
| `Ukraine-Air-Raid-Alerts-Report.pdf` | Findings as a clean 8-page report — **start here** for a quick read. |
| `Ukraine-Air-Raid-Alerts-Report.html` | The same report as self-contained HTML (opens offline in any browser). |
| `air_raid_analysis.ipynb` | The full analysis notebook — every step commented; runnable in Colab. |
| `air_alerts_snapshot_2026-06-23.csv` | The frozen dataset the analysis runs on. |
| `LICENSE` | MIT. |

## Questions this answers

1. When do alerts happen most — across the day, the week, and over the war?
2. Which regions are the biggest targets, and how has that shifted?
3. Is the pattern seasonal or trend-driven — and where is it heading?

## The data

- **Source:** [Vadimkin/ukrainian-air-raid-sirens-dataset](https://github.com/Vadimkin/ukrainian-air-raid-sirens-dataset) (MIT-licensed), file `volunteer_data_en.csv`. Each row is one alert: region, start, end, and a `naive` flag.
- **Provenance:** this is **volunteer / OSINT data**, originating from the *eTryvoga* Telegram channel — *not* official government data. The closest official source is the *Povitryana Tryvoga* (Air Alert) app and its `api.ukrainealarm.com`, which isn't published as a historical dataset; this volunteer set is the de-facto research source for this kind of analysis.
- **Reproducibility:** the exact snapshot used (frozen 23 Jun 2026) is committed here as `air_alerts_snapshot_2026-06-23.csv`. The notebook loads that file, so anyone re-running gets identical numbers even though the live source updates daily.
- **Known limitations:**
  - **4.9%** of alerts have an estimated end time (`naive=True`, set to start + 30 min) — affects duration math, not counts.
  - Two permanently-on sirens (Luhansk from Apr 2022, Crimea from Dec 2022) are excluded from the source.
  - Counts are **alert events**, not hours under alert.

## Method

- **Python + pandas + matplotlib**, with **Prophet** for the forecast. Runs in Google Colab (badge above) or any Jupyter environment.
- Timestamps are converted from UTC to **Kyiv local time** (`Europe/Kyiv`), which applies the EET/EEST daylight-saving switch automatically per record. (Ukraine still observed DST through 2026 — the 2024 law to abolish it was vetoed.)
- Alerts are **resampled** to daily/weekly/monthly counts and examined by hour-of-day, day-of-week, month, and region.

The notebook is written to be read top-to-bottom — each step is commented and explained in plain language.

## Findings in detail

### When
- **Over the war:** invasion spike (Feb 2022) → calmer 2023 (~1,000–1,600/mo) → gradual escalation through 2024 → mid-2025 dip → steep climb to all-time highs in 2026. Peak month: **May 2026, 3,402 alerts**.
- **Time of day:** alert *starts* peak at **12:00 (5,482)**, lowest at **06:00 (2,776)**. This counts when alerts *begin*; overnight drone (Shahed) alerts tend to be long single events, so total *time under alert* likely skews more nocturnal than the starts curve suggests.
- **Day of week:** essentially flat — **Thursday highest (15,103), Sunday lowest (13,231)** — only ~14% above the quietest day.

### Where
- **Biggest targets** (share of all alerts): Dnipropetrovsk **11.6%**, Kharkiv **10.9%**, Zaporizhzhia **8.2%**, Donetsk **8.0%**, Sumy **7.6%** — all frontline east/south oblasts.
- **Kyiv City is low (2,299)** — partly a Kyiv-City-vs-Kyiv-oblast split, partly different capital alert dynamics. The ranking tracks proximity to the front, not population.
- **Shift over time:** regions move together in broad national waves but diverge in 2025–26 — **Kharkiv and Sumy (northeast border) escalate sharply** while Donetsk declines. Caveat: Donetsk's drop may partly reflect occupied areas no longer issuing alerts, not reduced danger.

### Seasonal or trend-driven?
A Prophet decomposition is decisive: the war is **trend- and geography-driven, not calendar-seasonal**.
- **Trend** swings ~**46 → 133 alerts/day** — the dominant signal.
- **Weekly** effect is tiny: **−5.9 to +2.7/day** (Sunday lowest, Wed/Thu highest).
- **Yearly** effect is small (**−13 to +11/day**) *and* unreliable in shape: it shows a late-autumn dip, which contradicts the historically heavy autumn–winter energy-grid campaigns. With only ~4 years under a steep trend, Prophet's yearly term absorbs one-off events as false "seasonality" — so **no real calendar season should be inferred**.

### Projection to end of 2026
If the recent escalation continues, the model projects **~136 alerts/day by 31 Dec 2026** (uncertainty range **109–164**) — sustained record highs.

**This is an extrapolation, not a prediction.** Air-raid alerts are driven by military and strategic decisions, not a natural process. A ceasefire, further escalation, or a change in air-defense posture would break the projection entirely. The widening uncertainty band reflects that growing unknown.

## Reproduce it

The analysis is fully reproducible — the exact dataset is committed in this repo.

- **In Colab (easiest):** click the badge above, then `Runtime → Run all`. The notebook loads the committed snapshot from this repo over the web; no API keys or setup needed.
- **Locally / offline:** clone the repo and run the notebook in Jupyter. To read the committed CSV straight from disk (no internet), change the `url = ...` line in the data-loading cell (Cell 2) to `url = "air_alerts_snapshot_2026-06-23.csv"`.

## Attribution & license

- Data: [Vadimkin/ukrainian-air-raid-sirens-dataset](https://github.com/Vadimkin/ukrainian-air-raid-sirens-dataset) (MIT), via the *eTryvoga* volunteer channel.
- Code: MIT (see `LICENSE`).
