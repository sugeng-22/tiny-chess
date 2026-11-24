# Tiny Chess — entry for make.fun tiny-chess-01

An original chess engine in a single `main.py` (<= 4,096 bytes). It speaks
the kit's env protocol: it polls `POST /game` at `MAKEFUN_ENV_URL`, rebuilds
each position from `start_fen` + `moves`, answers with `POST /move`, and
exits when the env reports the trial is done. It never resigns and never
reads a clock: every move uses a fixed node budget per opponent, so trials
replay move-for-move.

## Design

- 0x88 board, fully incremental make/unmake (zobrist hash, castling rights,
  en-passant, halfmove clock, tapered phase counter, evaluation accumulator).
- Negamax with PVS, iterative deepening, transposition table, check
  extensions and null-move pruning.
- Quiescence over captures and promotions with delta pruning; when in
  check, quiescence searches all legal evasions instead of standing pat.
- Move ordering: TT move, MVV-LVA captures/promotions, then killers.
- Evaluation: material + piece-square tables (midgame tables for all pieces
  plus an endgame king table blended in as material disappears) and a
  bishop-pair bonus. Repetition and the fifty-move rule score 0 in search.

## Packing

`main.py` is an LZMA unpacker stub: the engine source is minified,
compressed, and stored as a raw payload inside a latin-1 string literal
(with a two-byte escape for NUL and CR bytes). All bytes count toward the
4,096-byte cap. The readable engine source is kept by the author.

## License

MIT, copyright Dwi. The engine is original work; it is not derived from
Sunfish or any other existing engine.
