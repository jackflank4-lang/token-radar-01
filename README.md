# token-radar-01

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/jackflank4-lang/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/jackflank4-lang/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar: it reads a sealed snapshot of every protocol DefiLlama tracks, works out which ones still record no token and are not a wrapped or staked product of one that trades, and ranks them by an implied launch value priced from the market itself — each category's comparable multiple is the median market-cap-to-TVL ratio of that category's live-token protocols in the snapshot — then hands in a web dashboard showing the board, a detail view for each protocol with the model worked out and the live comparables behind it, and the steps to farm it.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

The program writes `board.json`, `data.js`, `dashboard/`, `screenshots/` and `dossier.json` into the output directory. It needs Node.js 22.18 or later and nothing else.

---

<sub>Entered on [make.fun](https://make.fun/?ref=g7tqc4ex&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
