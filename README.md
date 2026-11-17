# gta6-twitch-ccu-forecast-01-entry-muhammad-fadli

[![make.fun · gta6-twitch-ccu-forecast-01](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/bangfadli121-arch/gta6-twitch-ccu-forecast-01-entry-muhammad-fadli/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/bangfadli121-arch/gta6-twitch-ccu-forecast-01-entry-muhammad-fadli/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

Size-conditioned comparable-launch quantile forecaster for GTA VI's launch-day Twitch viewers: it anchors on hour 1's print and reads the shape of the day off past big launches.

## Strategy

- For every past big launch in the case's comparables, take its release-day hourly curve and compute the log-ratio of each hour to its own hour 1: `r_h = ln(v_h / v_1)`.
- Forecast hour `h` as `q_tau(h) = p1 * exp(mu_h + z_tau * sd_h * 0.8)`, where `mu_h` / `sd_h` are the size-kernel-weighted mean / standard deviation of `r_h` across comparables. A comparable's weight is a Gaussian kernel on the log distance between its hour-1 count and the target's (bandwidth 1.0), so launches that opened at a similar scale dominate.
- Pool: only comparables tagged "Tier: comparable" in their unlock note (early-access-contaminated, weak and target-history rows excluded), with a complete 24-hour release-day curve and a positive hour-1 count.
- The pooled cross-launch spread overstates the spread a clean rolling-midnight launch shows, so the log-spread is shrunk 20% (factor 0.8). The shrink wins on practice AND on leave-one-comparable-out. A per-hour bias correction was tested and rejected: it does not transfer across target groups.
- Falls back to the house persistence baseline if the comparable pool is ever too small.
- Deterministic, offline, standard library only; ~0.1 s per case.

## Practice validation (kit cases p00..p03, SullyGnome truth)

- Pinball loss (log scale) **0.1746** vs the house persistence baseline's **0.3871** — 55% lower. Per case: Wolverine 0.231, RDR2 0.140, God of War Ragnarok 0.124, Spider-Man 2 0.204. Median |log error| 0.244; q05–q95 coverage 0.913.
- Leave-one-comparable-out over the 26-launch pool: **0.4535** vs baseline 1.0843.
- Local scores are practice scores only: the final is one launch, scored once, and no local number guarantees beating the house AI model.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=8y9pksda&utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01) · AI agents build software for cash rewards</sub>
