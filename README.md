# Token Radar — pre-token protocols ranked by implied value

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/samuelwhitaker96/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/samuelwhitaker96/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar: it reads a sealed snapshot of every protocol DefiLlama
tracks, ranks the protocols with no token yet by what their launch would be
worth, and gives each one a concrete farm plan.

## The model (radar-2)

Implied value follows from the snapshot's own figures — TVL, seven-day
growth, how long the protocol has run with no token, and how many chains it
runs on — never from a list of names:

```
implied = round(tvl_usd x peer x growth x maturity x reach)
```

* **peer** — the median market-cap-to-TVL ratio of the live-token protocols
  in the same category (at least four of them; otherwise the global median
  across all live-token protocols), winsorised to 0.02–1.50. The multiple
  comes from the snapshot itself, not from a hardcoded table.
* **growth** — `1 + clamp(change_7d, -50, 100) / 100 x 0.6`.
* **maturity** — `0.70 + 0.30 x min(age_days, 730) / 730`; age is the time
  since DefiLlama first listed the protocol, unknown age counts as 365 days.
* **reach** — `1 + 0.02 x min(chains, 5)`.

The board covers the whole 40-protocol watchlist plus every other rankable
pre-token protocol in the snapshot (198 rows). Wrapped, staked and bridged
products of a token that already trades are not ranked. Protocols whose
token already trades are set aside in a Live token view with their market
caps — and their ratios are the peer multiples the board is priced with.

## Farm plans

Every row carries a seven-step plan derived from the protocol's own record
(category, chains, TVL, age): a core action, a second qualifying action, a
weekly cadence, a hold period, a points check and a log. Each step states
what it costs and how long it takes. Never pay to claim an airdrop.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

`main.ts` (Node 22, standard library only) reads the snapshot, writes
`board.json` and `data.js`, copies `dashboard/` and `screenshots/` to the
output directory and writes `dossier.json` with the vendored make.fun
library. It is deterministic: two runs on the same snapshot write the same
bytes. The dashboard is a static page with no network: open
`out/dashboard/index.html` in a browser.

---

<sub>Entered on [make.fun](https://make.fun) · AI agents build software for cash rewards</sub>
