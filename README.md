# VoxOdds MCP server

Remote [Model Context Protocol](https://modelcontextprotocol.io) server for **[VoxOdds](https://voxodds.com)** — live prediction-market odds (Polymarket, Kalshi) with all-in executable quotes, fee-aware EV checks, verified resolution rules, and an audited AI-vs-market forecasting track record. Free, no API key.

- **Endpoint:** `https://voxodds.com/mcp` (Streamable HTTP, JSON-RPC 2.0, stateless, no auth)
- **Official registry:** `com.voxodds/voxodds` · **Glama connector:** https://glama.ai/mcp/connectors/com.voxodds/voxodds
- **REST alternative:** OpenAPI at `https://voxodds.com/api/openapi.json`, Swagger UI at `https://voxodds.com/docs`, agent notes at `https://voxodds.com/llms.txt`

## Use it

Claude Code:
```bash
claude mcp add --transport http voxodds https://voxodds.com/mcp
```

`mcp.json` (Cursor, VS Code, and other clients that read this format):
```json
{
  "mcpServers": {
    "voxodds": { "url": "https://voxodds.com/mcp" }
  }
}
```

Claude.ai / ChatGPT — add `https://voxodds.com/mcp` under Settings → Connectors.

Try it with curl:
```bash
curl -s https://voxodds.com/mcp \
  -H 'Content-Type: application/json' -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_market_odds","arguments":{"question":"Will Lando Norris be the 2026 F1 champion?"}}}'
```

## Tools

| Tool | What it does |
| --- | --- |
| `get_market_odds(question)` | Resolve a natural-language question to one live market and return its implied probabilities. Refuses ambiguous matches. |
| `list_trending_markets(category?, search?, limit?)` | Trending markets by volume, optionally filtered. |
| `compare_platforms(query?, limit?)` | Same event priced on Polymarket vs Kalshi, with the spread. |
| `get_executable_quote(platform, market_id, side?, amount_usd?)` | All-in fill for one exact contract: order-book depth, venue taker fees, average price. |
| `compare_executable_quotes(polymarket_id, kalshi_ticker, ...)` | Executable comparison across venues for a reviewed equivalent pair. |
| `list_executable_opportunities(amount_usd?, side?)` | Ranked all-in opportunities across YES and NO of every reviewed equivalent pair. |
| `list_arbitrage_candidates(max_capital_usd?)` | Size-matched opposite-side coverage whose captured all-in cost is below the payout (unfilled candidates; legs are non-atomic). |
| `list_sportsbook_surebets(sport_key?, capital_usd?)` | Sportsbook surebets; returns a disabled notice until a live odds feed is configured. |
| `check_bet(event, side, odds_offered?, sport?)` | Pre-bet check: conservative event resolution, then EV, break-even odds and Kelly against the market reference. |
| `find_best_price(event, side, sport?)` | Best available price across configured books; prediction-market midpoints are never presented as executable. |
| `get_edge_signals(limit?)` | Current edge signals from the VoxOdds edge engine. |
| `get_research_theses()` | Research-desk theses with supporting markets. |
| `get_track_record()` | The audited AI-vs-market experiment: Brier scores vs contemporaneous prices, losses included. |
| `submit_forecast(market_id, outcome, probability, forecaster_id)` | Record your own probability on a live market (append-only) and build a public Brier track record. |
| `get_forecaster_record(forecaster_id)` | Public scorecard for any forecaster id. |
| `get_world_cup_odds()` / `get_world_cup_brief()` / `get_world_cup_matchday(date?)` | Archived World Cup 2026 tools. |

## Notes
- Displayed probabilities are not executable quotes; verify order books, fees, fills, eligibility and resolution rules before acting. Research only; check venue eligibility and jurisdiction.
- Venue links returned by the tools may carry a referral code (the price is the same for you).
- Scored-forecast dataset: `https://voxodds.com/api/v1/scoreboard/dataset.csv` (CC BY 4.0).
- Operated by Soft Dev FZ LLC (Fujairah Creative City, UAE). Contact: apps@brainystack.co
