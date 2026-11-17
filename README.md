# tiny-chess-01 — a 4 KB alpha-beta chess engine

A complete chess engine in a single `main.py` of **4,092 bytes**
(limit 4,096). Python 3.13 standard library only. It reads, imports
or execs no other file in this repository.

## How it works

`main.py` is a one-line unpacker: an LZMA-compressed, base85-encoded
payload that decompresses and execs the engine at import time. The
engine itself is a 0x88 board, alpha-beta search engine:

- **Search**: iterative deepening to a fixed **300,000 nodes per
  move** (deterministic — no clock, so a final trial replayed
  move-for-move gives the same moves). Alpha-beta with principal
  variation search, late-move reductions, null-move pruning (R=2),
  a transposition table, two killer moves per ply, a history
  heuristic, MVV-LVA capture ordering, and a captures-only
  quiescence search with check evasions and delta pruning
  (promotions counted at full value).
- **Evaluation**: material plus CPW piece-square tables
  (quantised to 10 cp, left-right symmetric, stored half-width),
  plus a +10 cp tempo bonus. Score is maintained incrementally
  (a contribution table per piece/square), so evaluation is O(1).
- **Correctness**: full legal chess — castling (both sides),
  en passant, promotion (all four pieces), check, checkmate,
  stalemate, threefold repetition and the fifty-move rule are all
  handled by the environment; the engine itself generates only
  pseudo-legal moves and filters out those that leave its king in
  check. Verified by perft: startpos 8,902 @ d3 / 197,281 @ d4,
  Kiwipete 97,862 @ d3, and four further standard positions all
  exact.

## Protocol

The engine talks to the competition environment at
`$MAKEFUN_ENV_URL`: `POST /game {}` to get the game in play,
`POST /move {game, move}` (UCI) to play a move, until the env
answers `status: "done"`.

## Measured strength

Practice trials against the house engines (6 games each; the
baseline to beat, sunfish-4k, scores 1,365 Elo on this set and
1,364 / 1,451 / 1,279 on trials 0 / 1 / 2):

- Trial 0 (packed entry, 300k nodes/move): **Elo 1,545** —
  3 won, 3 drawn, 0 lost (3 by checkmate, 3 by threefold
  repetition). Depth reached is typically 7 plies in the
  middlegame at this node budget (~4.7 s/move locally, max ~6.3 s,
  inside the 10 s/move limit).
- Trial 1: **Elo 1,451** — 2 won, 4 drawn, 0 lost.
- Trial 2: **Elo 1,451** — 2 won, 4 drawn, 0 lost.
- **Mean 1,482** across the three practice trials, against the
  sunfish-4k baseline mean of 1,365 — and undefeated in all 18
  practice games (7 wins, 11 draws, 0 losses).

The practice floor to enter the final is 1,154 Elo.

## Files

- `main.py` — the whole engine (4,092 bytes).
- `LICENSE` — MIT.
- `.makefun/owner.json` — owner wallet for the bounty.

## Run it

Set `MAKEFUN_ENV_URL` to the environment URL and run
`python3 main.py`. No other setup; no dependencies beyond the
standard library.
