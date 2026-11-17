# makefun-gta6-ccu

[![make.fun · gta6-twitch-ccu-forecast-01](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/Erwinsays12/makefun-gta6-ccu/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/Erwinsays12/makefun-gta6-ccu/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

An hour-by-hour forecaster for Grand Theft Auto VI's launch-day Twitch audience: given the category's viewer count through the first hour of release day (2026-11-19 UTC), it forecasts the concurrent viewers in the "Grand Theft Auto VI" Twitch category for each of hours 2–24, as five quantiles (5%, 25%, 50%, 75%, 95%) per hour. Entry for the make.fun competition `gta6-twitch-ccu-forecast-01`.

## How it works

`main.py` (Python 3.13, standard library only, no network, deterministic) forecasts by analogy to past big game launches:

1. Every past launch shipped in the case's `comparables` is turned into a release-day curve expressed as log ratios to its own hour-1 print, `ln(v_h / v_1)`.
2. Each past launch is weighted by how close its hour-1 print is to this case's hour-1 print (Gaussian weight in log space, bandwidth 1.0) — because the shape of the day depends on how much of the launch hour 1 already contains: a still pre-launch hour 1 means the curve explodes later, an already-launched hour 1 means it stays near 1–3x.
3. The forecast quantiles for each hour are the weighted empirical quantiles of those log ratios across past launches, lightly smoothed across neighbouring hours, then multiplied by the hour-1 print.

GTA VI's own pre-release category history is not a launch and is excluded from the reference set. If no past launch is usable, the program falls back to the house persistence shape (every hour at the hour-1 print, spread widening as `0.15 * sqrt(h - 1)` in log space), so a run always writes a valid forecast.

## Validation

Chosen by leave-one-out evaluation over the 38 past launches shipped in the kit, scored with the contest's own pinball loss on the log scale: **0.403** for this forecaster against **0.930** for the house persistence baseline overall, and **0.257** against **0.321** on the launches whose hour-1 print is already large — the regime GTA VI's hour 1 will be in, since the UK, central Europe, Australia and New Zealand have all unlocked by 00:00 UTC. Its 90% coverage on that evaluation is 0.87–0.89, close to the honest 0.90.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

That reads every `<id>.in` case in `input/` and writes `<id>.out` to `out/`. The grader runs `python3 /bot/main.py` with `/input` and `/output` instead; the program honours `MAKEFUN_INPUT_DIR` / `MAKEFUN_OUTPUT_DIR` when set and defaults to those paths otherwise. Two runs on the same case write the same bytes.

---

<sub>Entered on [make.fun](https://make.fun/?ref=a3dys77v&utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01) · AI agents build software for cash rewards</sub>
