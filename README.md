# sitegen-fast

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/rivafdlh89/sitegen-fast/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/rivafdlh89/sitegen-fast/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for makefun-site 1.0.0, entered in
[site-generator-speed-01-run-20261122](https://make.fun/bounty/site-generator-speed-01-run-20261122):
`solve.js` returns byte-identical output to makefun-site 1.0.0 on every case, and
`main.ts` is the harness driver that reads `/input/cases.json` and writes
`/output/answers.json`.

## How it is faster

A faithful reimplementation of makefun-site's build (front matter, template dialect,
Markdown subset, slug rule, report), rewritten for speed:

- **Bulk HTML escaping** — plain runs are escaped in one scan instead of one
  regex replace per character (the reference's hottest spot).
- **Range-based inline rendering** — constructs are found with `indexOf` scans and
  rendered over string ranges, without the reference's recursive substring copies.
- **Index-based front-matter parsing** — no split/join of whole files.
- **Single-pass Markdown rendering** — blocks render straight into the output
  array; no token objects.
- **Cached slugs** — `slugify` results are memoised per case (tags repeat heavily).
- **Prototype-chain template scopes** — loop scopes link by prototype, so path
  lookup reads straight down the chain instead of walking scope objects.
- **No per-case caching** — every `run(input)` builds from its input alone.

## Practice results

On the kit's practice set (3 cases, 250–480 Markdown sources each):

- Correctness: **3/3 cases byte-identical** to makefun-site 1.0.0.
- Speed: **~2.7× faster** than the house baseline (~225 ms vs ~601 ms per set,
  mean over repeated runs on this machine).

## Layout

- `solve.js` — the entry: `run(input)` returns makefun-site's answer.
- `main.ts` — the harness driver (the house baseline's driver, unchanged).
