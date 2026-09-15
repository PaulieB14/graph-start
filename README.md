# The Graph: where do I start?

A routing guide for The Graph. Pick a chain, paste a contract address, and find out whether something
already indexes it before you build your own.

Static site — no build step, no framework. `index.html` plus four data files.

## Data files

| File | Contents |
|---|---|
| `routes.json` | Decision rules as data. Evaluate in order, first match wins. |
| `chains.json` | 94 networks × subgraphs / substreams / firehose / Token API. |
| `contracts.json` | 784 contract addresses → protocol, chains, indexing subgraphs. |
| `catalog.json` | 32 subgraphs: IDs, entities, an example query, observed field warnings. |
| `llms.txt` | Index and usage order, llms.txt convention. |
| `SKILL.md` | Skill file, same frontmatter shape as `api.pinax.network/SKILL.md`. |

All served with `Access-Control-Allow-Origin: *`, so agents can fetch them directly.

## Provenance

Coverage comes from The Graph Networks Registry v0.7.121. Token API coverage is taken from a live call to
`api.pinax.network/v1/networks` and matched on CAIP-2, because the registry carries stale identifiers for
Polygon, Solana and HyperEVM. Contract addresses are DEX factories from `/v1/evm/dexes` across nine chains,
plus contracts indexed by catalogued subgraphs. Subgraph IDs and lifetime query fees come from The Graph
network subgraph; every catalogue entry had its schema read and one query executed against the live gateway.

Verified 2026-09-14.

## What this is not

A complete index of The Graph, or a health check. These files report what **exists** and what a single query
returned at the timestamp shown. Lifetime query fees are historical and say nothing about whether a
deployment answers today — always run `{ _meta { block { number } } }` yourself before relying on one.
[thegraph.com/explorer](https://thegraph.com/explorer) is authoritative for search.
