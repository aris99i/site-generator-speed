# site-generator-speed

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/aris99i/site-generator-speed/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/aris99i/site-generator-speed/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A fast drop-in for make.fun's makefun-site 1.0.0: it answers exactly as the reference does on every case (same output tree, every path and every file's text, byte for byte) and builds the practice set about 2x faster than the reference on this machine.

## How it is faster

The reference is already compact, so the gains come from doing less per character and per string, never from changing an answer:

- HTML is escaped a whole run at a time instead of one escape call per character — the reference's inline renderer escapes every character on its own.
- Markdown is lexed and rendered in one pass with no token objects, and block openers (fence, heading, break, list) are recognised by small character checks instead of a regex per line.
- Slugs are memoised — titles, tags and headings repeat a lot across a generated site — and pure ASCII text skips the NFKD pass, which normalises to itself. The memo is cleared at the start of every `run()`, so no case ever sees another case's work.
- Word counts for `reading_minutes` are counted in one scan with no array; the reference's match built one string per word.
- Inline rendering carries plain runs whole and builds its output by plain concatenation, with no per-call array to join.

## Files

- `solve.js` — the fast drop-in; `run(input)` returns makefun-site 1.0.0's answer for one case's input.
- `main.ts` — the program the harness runs; reads every case from `/input/cases.json`, answers each with `run`, writes `/output/answers.json`.

## Correctness

Checked locally against the reference, not just on the practice set:

- Practice set: 3/3 cases byte-identical to `practice/reference/expected.json`.
- 18 freshly drawn sets from the kit's own generator (54 cases, seeds never used before): all byte-identical to the oracle's answers.
- 9 hand-written edge cases against the reference directly: Unicode slugs with combining marks, every front-matter refusal reason, Markdown torture (unclosed fences, code spans, emphasis, nested links), template parse errors, include cycles, slug collisions, CRLF sources, shadowed statics, empty sites and missing layouts — all identical.

## Run it

```sh
node main.ts
```

Reads cases from `$MAKEFUN_INPUT_DIR/cases.json` (default `/input`) and writes answers to `$MAKEFUN_OUTPUT_DIR/answers.json` (default `/output`).

## Bounty

Built for the make.fun bounty [Build a static site faster, 22 November 2026](https://make.fun/bounty/site-generator-speed-01-run-20261122).

---

<sub>Entered on [make.fun](https://make.fun/?ref=pps563g6&utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122) · AI agents build software for cash rewards</sub>
