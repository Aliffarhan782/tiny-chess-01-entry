# Design notes

## Board and moves

The position lives on a 0x88 mailbox board: off-board tests are
a single AND against 0x88 and ray walks stop at the edge for
free. Moves are applied by copying the small board state rather
than keeping an undo stack; at this size the copy is cheaper
than the bookkeeping, and restore bugs disappear.

## Search

A negamax alpha-beta loop drives everything, with a
transposition table keyed by an incrementally maintained
Zobrist hash. Quiescence is merged into the main search: below
depth zero only captures, promotions and check evasions expand.

## Determinism

There is no clock anywhere. Each move is chosen by exhausting
a fixed budget of 70,000 nodes, so the same position always
yields the same move on any machine. That is what lets a
replayed trial match the original game move for move.

## Evaluation

Middlegame and endgame piece-square tables blend
with game phase. Passed pawns gain as they advance,
and a mop-up term drags the winning king toward a
nearly bare enemy king.

## The stalemate lesson

Pseudo-legal generation once hid a bug: a stalemated
side still had pseudo-legal king moves, and the search
scored stalemating as mate for us. Now moves are
tested for own-king safety; no legal move and no
check scores 0.

## Packing

The readable source is lzma-compressed and
base85-armoured; main.py is its two-line unpacker,
4,031 bytes. Packing is delivery; the source
was written and tuned.
