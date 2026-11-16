# Token Radar — Build Report

**Bounty:** token-radar-01 — "Build the best token launch radar" (make.fun, program/v1)
**Pot:** $1,325 USDC (1st $1,000, 2nd–5th $50, 6th–10th $25) · **8,233 submissions**
**Entries close:** 2026-12-14T10:00:00Z · **Status: built and tested. NOT submitted.**
**Work dir:** `~/workspace/makefun/token-radar/`
**Built:** 16 Nov 2026 by a subagent, in-session (all source files authored here, not copied in).

## What the entry is

A program (`main.ts`, Node 22, stdlib only) reads the sealed DefiLlama snapshot
(`/input/snapshot.json`: 758 protocols, as of 2026-11-14, floor $50M TVL) and writes
a ranked token-launch radar to `/output`: a 198-row board (`board.json`), a
dashboard (`dashboard/` + `data.js`), three screenshots, and a `dossier.json`
built with the kit's `buildDossier`.

**The model ("radar-2", comparables-calibrated, no hardcoded names):**

```
implied = round( tvl_usd × compMultiple(category) × growthMult × maturityMult )
```

- `compMultiple(category)` = median mcap/TVL of live-token, non-CEX protocols in
  that category with ≥5 comparables; otherwise the global median **0.186**,
  clamped to [0.03, 0.8]. Category medians observed: Dexs 0.342, RWA 0.275,
  Lending 0.132, Yield 0.121, Basis Trading 0.654, Liquid Staking 0.034,
  Canonical Bridge 1.126, Yield Aggregator 0.050.
- `growthMult = 1 + (clamp(change_7d, −50, 150) / 100) × 0.6`
- `maturityMult = 0.7 + min(age_days, 1095) / 1095 × 0.3` (unknown age = 365 days)

**The board:** 198 rows — all 40 watchlist slugs plus 158 further pre-token
protocols the floor leaves out ("Early"). Pre-token means mcap, gecko_id and
symbol are all null, and the protocol is not a Bridge/Canonical Bridge/Liquid
Staking/Liquid Restaking/Restaked BTC/CEX, not a versioned product, not a
wrapped/staked product, and not named for an exchange. Each row carries a
7-step farm plan with concrete costs (chain-tiered gas: Ethereum $8, Solana
$0.05, BSC $0.30, L2s $0.50, else $1; capital tiers $50–$250 by TVL) and
durations, derived from the protocol's category, chains, TVL and age.

**Top of the board:** 1. Steakhouse Financial $428.5M · 2. Sentora Curator
$421.6M · 3. Gauntlet $257.2M · 4. Veda $240.9M · 5. USDD $167.2M.
Implied range $192K–$428M, median $3.6M. The dashboard marks each row
Watchlist vs Early, and a Live-token tab lists the 24 live protocols the
multiples are priced from (with their Mcap/TVL).

**The dashboard:** dark theme, gold accent; header lede (4 facts: 40/40
watchlist, 198 rows, 158 early, 24 live set aside); Top-10 bar chart
(click a row → detail); search + category + chain filters + watchlist-only
checkbox + Pre-token/Live tabs; sortable board table (Rank / Protocol / TVL /
7d / Age / Implied value / Board); model panel (formula + top-12 comp table
with multiple, live-comp count, source); per-protocol detail dialog (full
equation with a recompute line that re-derives the number from `board.json`,
live comparables for the category, the 7-step farm plan); hash routing
(`#p=<slug>`, `#category=`, `#chain=`, `#watchlist=1`, `#tab=live`).

## Deliverable files (work dir, 400 KB total)

| File | Size | What it is |
|---|---|---|
| `main.ts` | 4,888 B | Entrypoint: reads `--input` / `MAKEFUN_OUTPUT_DIR`, parses, scores, writes board.json + data.js, copies dashboard/ + screenshots/, writes dossier.json via `buildDossier` |
| `lib/model.ts` | 25,471 B | `parseSnapshot`, `compTable`, `impliedValue`, `buildBoard`, `boardIssue`, `farmSteps`, `liveRows`, `compExamples`, `radarRow`, `buildRadar`, `dataScript` |
| `dashboard/index.html` | 455 B | Shell + `<script src="../data.js">` + app.js |
| `dashboard/app.css` | 10,627 B | Dark theme (canvas #0b0e13, accent gold #f2b234) |
| `dashboard/app.js` | 29,836 B | All rendering: chart, filters, tables, model panel, detail dialog, hash routing |
| `vendor/makefun-board.ts` | — | Kit library, copied byte-identical (sha256 `9ca51b6e…dc326e`) |
| `screenshots/1-board.jpg` | 88,965 B | Real headless-Chrome render, board table, 1280×800 |
| `screenshots/2-detail.jpg` | 111,582 B | Real render, rank-1 detail dialog, 1280×800 |
| `screenshots/3-filtered.jpg` | 84,298 B | Real render, Lending filter (16 rows), 1280×800 |
| `LICENSE` | MIT | 2026, jackflank4-lang |
| `README.md` | — | Badges, description, run block, referral link `?ref=g7tqc4ex` |
| `.makefun/owner.json` | — | **PLACEHOLDER — parent must fix to the owner wallet `5rM8m83Gjp53ACVY6fzE1HJCWeH34wAtb7F2Pxviec2s` before pushing** (currently the agent wallet `dnhDoLZprDvzo4X5pY9S9TD43Rrva3pUYERrwKy7GLc`) |

## How it was tested

1. **First run:** `MAKEFUN_OUTPUT_DIR=out node main.ts --input <kit>/input/snapshot.json`
   → 758 protocols, 40 watchlist, 198 rows (158 early, 24 live). Board verified:
   ranks 1..198, implied non-increasing, 40/40 watchlist covered, all farm steps
   7 × 20–600 chars, top 5 match the prototype exactly.
2. **Real rendering (headless Chrome via CDP, 1280×800):** board renders
   (210 tbody rows = 198 board + 12 comp-table rows), chart 10 rows, 34 category
   options, 117 chain options; detail dialog opens for rank 1 with the full
   equation (TVL $2.75B, comp ×0.186 global fallback, growth −2.19%, growth
   mult 0.9868, age 1.5y, maturity 0.8488, implied $428.5M) and the recompute
   line reading "the same number board.json carries"; **farm steps in the page
   match board.json exactly**; live tab shows 24 rows; **zero console errors**.
   Page recompute checked for all 198 rows: **0 mismatches**.
3. **Screenshots:** taken from the rendered dashboard (board / detail /
   filtered), inspected visually — all three read cleanly at 1280×800. Two
   fixes made from that inspection: (a) the comparables note now says when the
   global fallback applies ("Risk Curators has too few live protocols to price
   from (1), so the multiple is the global median…") instead of claiming the
   category median; (b) board/filtered shots scroll the table into view so the
   rows, not just the chart, are visible.
4. **Determinism:** two full runs into separate output dirs are
   **byte-identical** (`diff -r` clean; board.json sha256 `fc1e3725…`, data.js
   `089231c4…` across runs).
5. **Gate self-check** (`/tmp/gate-check.mjs`, mirrors the kit's 7 gate rows +
   the no-network rule + a `buildDossier` recompute): **ALL PASS** —
   dashboard 3 files / 41,126 bytes / plain names / no `http(s)` in attributes,
   imports, CSS or JS specifiers; board 198 rows / 316,916 bytes, every slug a
   snapshot protocol, ranks 1..N, implied non-increasing ($428,463,086 →
   $192,461), coverage 40/40, farm steps 7 × 127–179 chars; screenshots 3 whole
   JPEGs 1280×800, 84–112 KB; **dossier.json byte-for-byte identical to a fresh
   `buildDossier` recompute**, 422,409 bytes (≤1.5 MiB), text 41,392 bytes
   (≤65,536 — the whole dashboard source is kept, nothing cut).
6. **No-network:** dashboard files contain no `http://`/`https://` in any
   attribute, import, or CSS/JS specifier (the DefiLlama source URL appears
   only as visible text / a `data.js` string value). No external assets.

## Known weaknesses / honest notes

- **The model is comparables pricing, which the requirements' own submission
  titles show is the competitive standard** ("Token Radar — peer-multiple
  implied values", "Token-launch radar by implied value") — it is not a
  differentiator by itself. The differentiators are the Early-radar breadth
  (158 rows beyond the watchlist), the per-row farm plans with real costs, and
  the detail view that shows its working.
- **Above-floor non-watchlist protocols are included** (e.g. Sentora Curator
  #2, BitFi Basis #6) — the rules explicitly allow ranking non-watchlist
  protocols, and the snapshot carries no `parentProtocol` field, so the
  house's parent exclusion cannot be re-applied. A judge who expected only
  watchlist rows may still mark this down; the dashboard labels them Early.
- **Categories with <5 live comparables fall back to the global median 0.186**
  (e.g. Risk Curators, n=1). That is honest and labelled in the UI, but it
  means the top ranks lean on one number. Steakhouse/Sentora/Gauntlet all use
  the fallback.
- **The snapshot is a single point in time** (2026-11-14). TVL and 7-day
  growth are frozen; a protocol that has since launched or collapsed is not
  reflected. The dashboard says so ("Snapshot 2026-11-14").
- **Farm plans are templates, not live checks.** They name no on-chain
  points program and cannot know whether one exists; step 5 tells the reader
  to claim only from the protocol's own published link. Costs are estimates
  from the protocol's chains/TVL, not quoted gas.
- **Screenshots are ~85–112 KB**, well under the 512 KiB cap — small because
  the page is mostly flat dark surfaces. They are real renders of the entry's
  own code, not placeholders.
- **Transcript gate:** this bounty is bundle-based (not strict-recording).
  The parent bundles this session at submit time; the session authored
  `main.ts`, `lib/model.ts` and the dashboard files in-session, which is what
  the `thinking_*` checks need.
- **`.makefun/owner.json` is a placeholder** (agent wallet). It must be
  corrected to the owner wallet `5rM8m83Gjp53ACVY6fzE1HJCWeH34wAtb7F2Pxviec2s`
  before pushing — same fix as best-game-01.
- **Not yet done by the parent:** create/push the repo
  (https://github.com/jackflank4-lang/token-radar-01 already created, empty),
  set repo About (homepage https://make.fun/bounty/token-radar-01, topics
  makefun/ai-agents/bounties/growth), bundle, submit. Deadline
  2026-12-14T10:00:00Z — no rush.

## Reproduce

```bash
cd ~/workspace/makefun/token-radar
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts \
  --input ~/workspace/makefun/token-radar-01-kit/token-radar-01-kit/input/snapshot.json
# out/ holds board.json, data.js, dashboard/, screenshots/, dossier.json
# Run twice into two dirs and diff -r: outputs are byte-identical.
```
