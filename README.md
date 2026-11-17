# tiny-chess-01

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/chenrui012-sketch/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/chenrui012-sketch/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A complete chess engine in a single `main.py` of **3,293 bytes** (limit 4,096). Python 3.13 standard library only. It reads, imports or execs no other file in this repository.

## Run it

```sh
MAKEFUN_ENV_URL=<env-url> python3 main.py
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=qeryy9q8&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>

## How it works

`main.py` is the kit's `makefun compress` packing of the readable
engine source: an LZMA stream the stub decompresses and execs. The
engine is a 0x88 board, alpha-beta search to a fixed **300,000
nodes per move** (deterministic — no clock): PVS, late-move
reductions, null-move pruning (R=2), transposition table, two
killer moves per ply, history heuristic, MVV-LVA ordering, and a
captures-only quiescence search with check evasions and delta
pruning (promotions at full value). Evaluation is material plus
CPW piece-square tables (quantised to 10 cp, left-right symmetric,
stored half-width) plus a +10 cp tempo bonus, maintained
incrementally so evaluation is O(1). Full legal chess — castling,
en passant, promotion, check, checkmate, stalemate, threefold
repetition and the fifty-move rule — verified by perft (startpos
197,281 @ d4, Kiwipete 97,862 @ d3, and four further positions
all exact).

## Protocol

The engine talks to the competition environment at
`$MAKEFUN_ENV_URL`: `POST /game {}` to get the game in play,
`POST /move {game, move}` (UCI) to play a move, until the env
answers `status: "done"`.

## Measured strength

Practice trials against the house engines (6 games each; the
baseline to beat, sunfish-4k, scores 1,365 Elo on this set):
trial 0 **1,545** (3–3–0), trial 1 **1,451** (2–4–0), trial 2
**1,451** (2–4–0) — **mean 1,482**, undefeated in all 18 games
(7 wins, 11 draws, 0 losses). Depth is typically 7 plies in the
middlegame (~4.7 s/move locally, max ~6.3 s, inside the 10 s/move
limit). The practice floor to enter the final is 1,154 Elo.

## Files

- `main.py` — the whole engine (3,293 bytes).
- `LICENSE` — MIT.
- `.makefun/owner.json` — owner wallet for the bounty.
