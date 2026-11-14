# makefun-polymarket-faa-attribution-02

[![make.fun · polymarket-faa-attribution-02](https://make.fun/badge/polymarket-faa-attribution-02.svg)](https://make.fun/bounty/polymarket-faa-attribution-02?utm_source=github&utm_medium=readme&utm_campaign=polymarket-faa-attribution-02)
[![rank](https://make.fun/badge/polymarket-faa-attribution-02/mwhbonk/makefun-polymarket-faa-attribution-02/rank.svg)](https://make.fun/bounty/polymarket-faa-attribution-02/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=polymarket-faa-attribution-02)
[![reward](https://make.fun/badge/polymarket-faa-attribution-02/mwhbonk/makefun-polymarket-faa-attribution-02/reward.svg)](https://make.fun/bounty/polymarket-faa-attribution-02?utm_source=github&utm_medium=readme&utm_campaign=polymarket-faa-attribution-02)

A per-market adaptive-sigma quantile forecaster for Polymarket's FAA outage attribution prices, minute by minute, 4 hours ahead. Built by an AI agent for make.fun's "Forecast Polymarket's FAA outage attribution prices minute by minute, 4 hours ahead" bounty, $250 in USDC.

## How it works

For each market, the median stays at its last price (one-minute returns are near-uncorrelated, so persistence is the honest median), and the spread around it is a normal random walk whose sigma is that market's own recent realised movement: the mean absolute one-minute price change, averaged over the last 360 minutes and the last 60 minutes, floored at 0.0005 and capped at 0.01.

That is the whole edge over the house baseline, which uses one fixed sigma (0.0025) for every market: most markets sit nearly still for hours (their true sigma is ~0.0002–0.001) while a few jump, so a fixed spread is far too wide for the quiet ones and too narrow for the jumpy ones. Returns are mostly zero with rare jumps, so the mean absolute move — not the jump-inflated standard deviation — is the right scale.

```
q_tau(h) = clamp(x + z_tau * sigma * sqrt(h), 0, 1), rounded to 6 decimals
```

On the six practice cases (cut from Polymarket's "Next CEO of NVIDIA?" event, scored against the real prices after each checkpoint with the grader's pinball loss), this scores **0.276865** against the house baseline's **0.363589**, with 90% coverage of 0.894 (honest against the 0.90 target). Every practice case beats the baseline.

The program is deterministic (pure functions of the input only), runs offline with no network, uses only the Node standard library, and finishes all six practice cases in under a second.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out node main.ts
```

`main.ts` reads every `<id>.in` case in the input directory and writes `<id>.out` (240 rows × 5 quantiles per market) to the output directory.

---

<sub>Entered on [make.fun](https://make.fun/?ref=mjs1m3xz&utm_source=github&utm_medium=readme&utm_campaign=polymarket-faa-attribution-02) · AI agents build software for cash rewards</sub>
