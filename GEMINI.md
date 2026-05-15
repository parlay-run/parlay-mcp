# Parlay — Prediction Market Tools

## What this is

Parlay aggregates live prediction market data across real-money venues and sentiment markets through six MCP tools: search, brief, discovery, comparison, discrepancy scanning, and platform inspection.

## When to use which tool

- `search_markets`: use when the user wants keyword search across live prediction markets or event contracts.
- `market_brief`: use when the user wants an AI-ready summary of a topic, event, candidate, asset, or macro question.
- `discover_markets`: use when the user asks what is trending, high-volume, new, fast-moving, or ending soon.
- `compare_markets`: use when the user wants side-by-side comparison of the same event across venues.
- `scan_discrepancies`: use when the user asks for arbitrage, price gaps, spreads, or cross-venue disagreement.
- `inspect_platform`: use when the user names a specific venue and wants that venue's markets or coverage.

## Venue coverage (Phase 1 validated)

- Polymarket + Kalshi = primary real-money
- Limitless = active real-money; systematic small premium on long-tail favorites (DIRECTIONAL SIGNAL, NOT executable arbitrage)
- Manifold = play-money only; hardcoded marketType:'sentiment'; excluded from real-money tools by design
- Opinion.trade = listed but ~no public liquidity (19/20 zero-volume in Phase 1 sample); NOT a broken connector
- Smarkets / Myriad / Metaculus / Probable / Baozi = catalog-only via PMXT

## Critical caveats

- Manifold spreads vs Polymarket/Kalshi are NOT executable arbitrage (Manifold is play-money)
- Cross-platform event matches require human verification — same headline ≠ same market
- No canonical mapping exists between Kalshi tickers and Polymarket condition IDs
- Always cite the venue name and timestamp when stating probabilities

## Behavioral guardrails

- Read-only tools: never claim to place trades, hold positions, or move funds
- Not financial advice: data is informational
- When the user asks for "arbitrage," surface cross-venue gaps but always note that execution risk, fees, and resolution criteria differences may eliminate the spread
