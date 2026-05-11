# Parlay — Prediction Market MCP Server

[![smithery badge](https://smithery.ai/badge/parlay-run/parlay)](https://smithery.ai/servers/parlay-run/parlay)

A unified MCP server for prediction markets. Search and compare across Polymarket, Kalshi, Limitless, and Manifold from inside Claude, ChatGPT, Gemini, OpenClaw, and any MCP-compatible AI client.

Parlay aggregates real-money prediction markets and sentiment markets through one hosted endpoint, with mechanical separation between the two so that real-money analysis never gets contaminated with play-money signal.

## Tools

| Tool | Purpose | Plan |
|------|---------|------|
| `search_markets` | Cross-venue keyword search for live prediction markets and event contracts | Free |
| `market_brief` | Synthesized brief on a topic, combining real-money signals with community sentiment | Free |
| `discover_markets` | Browse trending, high-volume, fast-moving, or high-disagreement markets | Pro |
| `compare_markets` | Compare the same event contract across venues side-by-side — probability, liquidity, settlement | Pro |
| `scan_discrepancies` | Surface cross-venue price discrepancies as a discovery feed (informational, not trade recommendations) | Pro |
| `inspect_platform` | Drill into a single named venue | Pro |

Every tool response carries unified metadata: data freshness, venues queried, venues failed, market type (`real_money` / `sentiment` / `mixed`), match confidence (`high` / `medium` / `low` / `not_applicable`), liquidity status, risk flags, and a standard non-trade-recommendation disclaimer.

Typical queries Parlay handles well:

- *"Search bitcoin prediction markets across all venues."*
- *"What's trending in AI markets today?"*
- *"Compare 2028 election odds across venues."*
- *"Brief me on Fed rate decision markets."*
- *"What's on Kalshi for Q4 inflation?"*

## Connect

```
MCP URL:   https://mcp.parlay.run/mcp
Auth:      OAuth
Transport: Streamable HTTP
```

### Claude (Desktop, web, mobile, Cowork)

Parlay connects through Claude's Custom Connectors interface. The same flow works across all Claude surfaces.

1. Open Claude settings (click your profile icon → **Settings**).
2. In the sidebar, select **Connectors**.
3. Scroll to the bottom and click **Add custom connector**.
4. Enter URL: `https://mcp.parlay.run/mcp`
5. Click **Add**, then **Connect** to complete the OAuth authorization.

Parlay's tools will appear in your tool list on the next message.

> **Note:** Custom connectors are available on Free, Pro, Max, Team, and Enterprise plans. Free Claude users are limited to one custom connector at a time. Do **not** configure Parlay through `claude_desktop_config.json` — that file is for local stdio MCP servers only; Parlay is a remote MCP server.

### Cursor, Cline, OpenClaw, and other MCP clients

Add `https://mcp.parlay.run/mcp` to your client's MCP server configuration. The OAuth flow will trigger on first tool call. Refer to your client's MCP setup documentation for the exact configuration file location and remote-MCP support status.

## Coverage

**Primary real-money venues:** Polymarket, Kalshi

**Secondary PMXT-backed venues/sources:** Limitless, Opinion.trade, Smarkets, Myriad, Metaculus, Probable, Baozi

Secondary sources may have incomplete pricing, volume, or liquidity data. Parlay flags these cases with metadata such as `secondary_venue_data`, `volume_unconfirmed`, and `depth_unknown_on_one_platform`, so downstream tools and AI agents can treat them with appropriate caution.

**Sentiment venues:** Manifold — isolated from real-money comparison and discrepancy tools.

```
                  Claude / ChatGPT / Gemini / OpenClaw
                              ↕  (MCP over HTTPS)
                    ┌────────────────────┐
                    │  mcp.parlay.run    │
                    │   (this server)    │
                    └────────┬───────────┘
                             │
                ┌────────────┴────────────┐
                ↓                         ↓
       Real-money data layer    Sentiment data layer
       Polymarket, Kalshi       Manifold
       + secondary venues       (isolated)
```

## Pricing

Parlay offers a free plan for basic market search and briefs. Pro unlocks the full toolset, including discovery, cross-venue comparison, discrepancy scanning, and platform inspection. See [parlay.run/pricing](https://parlay.run/pricing) for current pricing.

## Compliance posture

- **Read-only.** No order placement, no position management, no fund custody.
- **No private credentials handled.** Users never share venue API keys with Parlay.
- **Sentiment isolation.** Manifold is mechanically excluded from real-money tools (`compare_markets`, `scan_discrepancies`). The `sentiment_market_excluded` risk flag is emitted whenever it is filtered out.
- **Settlement and liquidity risk flags.** Markets with weak settlement criteria, missing volume data, or stale signals carry explicit risk flags in their metadata.
- **Standard disclaimers.** Every tool response carries a non-trade-recommendation disclaimer in its metadata block.

## Built on

Parlay's real-money venue access is built on [PMXT](https://www.pmxt.dev), an open-source unified SDK for prediction market venues. Parlay adds the AI intelligence layer on top: event comparability, settlement-risk metadata, sentiment isolation, and tool orchestration for MCP. PMXT trading methods are explicitly **not** exposed by Parlay — the hosted product is read-only by design.

## Resources

- [parlay.run](https://parlay.run) — main site
- [parlay.run/pricing](https://parlay.run/pricing) — current pricing
- [Privacy Policy](PRIVACY.md)
- [Terms of Service](TERMS.md)
- Issues and questions: file an issue on this repository

## License

Proprietary. See [TERMS.md](TERMS.md) for full usage terms.

The contents of this repository are published for transparency and review. They are **not** licensed for redistribution, modification, or commercial use. Parlay is delivered as a hosted service; usage of the service is governed by the Terms of Service.
