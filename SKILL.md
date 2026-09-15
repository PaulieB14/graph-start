---
name: graph-routing
type: skill
title: Zero to Query — The Graph routing
description: >
  Decide which part of The Graph serves a data need — Token API, an existing subgraph, a new subgraph, or
  Substreams — and check whether a contract is already indexed before building. Use when choosing between
  Graph products, looking up a contract address, checking per-chain coverage, or avoiding rebuilding an
  index that already exists.
resource: .
tags: [thegraph, subgraph, substreams, token-api, pinax, routing, evm, solana]
timestamp: 2026-09-14T00:00:00Z
---

# Zero to Query

Companion data for routing a data need to the right part of The Graph.

## Stance on reliability

This dataset reports **existence**, not health. Each catalogue entry records what ONE query returned at the
timestamp shown. It is **not** a certification that the endpoint works now.

- Always run `{ _meta { block { number } } }` against a subgraph before relying on it, and compare
  `_meta.block.number` to the chain head.
- `queryFeesGrt` is **lifetime and historical**. It is evidence people paid to use something, not evidence
  it works today. One catalogued subgraph with ~31,675 GRT of fees returned nothing to our probe.
- Absence from `contracts.json` is **not** proof nothing indexes an address. `thegraph.com/explorer` is
  authoritative.

## Files

| File | Contents |
|---|---|
| `routes.json` | Decision rules as data — evaluate in order, first match wins |
| `chains.json` | 94 networks with `sg` / `ss` / `fh` / `tapi` service flags |
| `contracts.json` | 784 addresses → protocol, chains, indexing subgraph IDs |
| `catalog.json` | 32 subgraphs: IDs, gateway URLs, entities, example query, field warnings |

## Decision order

1. **Contract given?** `contracts.json` → a hit with `subgraphIds` means it is already indexed; go query
   that instead of building. A hit with only a Token API source means the protocol is already decoded —
   query by protocol.
2. **Read the chain's services** from `chains.json`.
3. **Evaluate `routes.json`** against `{chain, goal}`. Goals: `balances`, `transfers`, `dex`, `nft`,
   `markets`, `protocol_state`, `own_contract`, `bulk`.
4. **Before using a subgraph**, read its `fieldWarning`, then run the freshness probe.

## The two credentials

They are not interchangeable.

- **Subgraph Studio key** → `gateway.thegraph.com` subgraph queries. 100K queries/month free.
  <https://thegraph.com/studio/>
- **The Graph Market token** → `api.pinax.network` Token API. <https://thegraph.market>

Several Token API endpoints need no credential at all — see `openEndpoints` in `routes.json`.

## Field traps worth knowing

- `reserveUSD` (Uniswap-V2-like): never use as `orderBy`. Ranking by it surfaced untracked pairs reporting
  ~8.8e33 USD with `volumeUSD` of 0. Rank by `trackedReserveETH`.
- `totalValueLockedUSD` (Uniswap-V3-like): never use as `orderBy` — inflated by illiquid spam pools.
  Rank by `volumeUSD`.
- `totalValueLockedUSD` (Aave/Messari): mirrors `totalDepositBalanceUSD`; not net of borrows.
- `profitUSD` (Aave/Messari): known to be wrong by ~1e6. Do not serve without a warning.

## Building, if nothing exists

Reuse before writing — The Graph's own docs carry *Reuse Existing Substreams Before Building*. Search
StreamingFast, Pinax and TopLedger packages on substreams.dev first. Agent skills: `substreams-dev`,
`substreams-ethereum`, `substreams-solana`, `substreams-sql`, `substreams-sink-deploy-local`,
`substreams-convert`.
