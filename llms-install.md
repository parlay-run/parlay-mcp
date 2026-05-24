# Installing Parlay MCP Server

Parlay is a hosted remote MCP server. There is no local code to clone, build, or run. Installation is a configuration step only.

## Step 1: Get a Parlay Personal Access Token

Ask the user to visit https://parlay.run/settings/tokens, generate a Personal Access Token, and provide it for MCP configuration. The token starts with `parlay_pat_`.

Treat the token as a secret. Do not print it unnecessarily, log it, commit it, or store it anywhere except the user's local MCP settings file.

Do not attempt to generate the token programmatically. Token generation requires the user to log in to parlay.run in a browser.

## Step 2: Add Parlay to Cline MCP settings

Prefer Cline's built-in MCP settings UI:

1. Open the Cline MCP Servers view.
2. Open the Configure tab.
3. Click Configure MCP Servers.
4. Add the `parlay` entry below under the top-level `mcpServers` object.

For Cline CLI, the MCP settings file is usually:

- `~/.cline/mcp.json`

For the Cline VS Code extension, Cline may open its MCP settings file automatically from the Configure tab. If you must locate it manually, common paths are:

- macOS: `~/Library/Application Support/Code/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json`
- Linux: `~/.config/Code/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json`
- Windows: `%APPDATA%\Code\User\globalStorage\saoudrizwan.claude-dev\settings\cline_mcp_settings.json`

Add this entry to `mcpServers`. Replace `PARLAY_TOKEN_FROM_USER` with the token from Step 1.

```json
{
  "mcpServers": {
    "parlay": {
      "url": "https://mcp.parlay.run/mcp",
      "transport": "streamable-http",
      "headers": {
        "Authorization": "Bearer PARLAY_TOKEN_FROM_USER"
      },
      "connectionTimeoutMs": 30000
    }
  }
}
```

If `mcpServers` already contains other entries, merge the `parlay` entry into the existing object. Do not overwrite or remove existing MCP servers.

## Step 3: Verify

After saving the config file, Cline should reload or reconnect its MCP servers. Confirm that the `parlay` server appears in the connected MCP servers list and that these tools are visible, possibly with a `parlay__` prefix:

- `search_markets`
- `compare_markets`
- `scan_discrepancies`
- `market_brief`
- `discover_markets`
- `inspect_platform`

Test with a simple query:

```text
Search prediction markets for "election".
```

## Troubleshooting

- 401 Unauthorized: The PAT is invalid, expired, or malformed. Ask the user to regenerate a token at https://parlay.run/settings/tokens.
- Connection refused or server unavailable: Check that the URL is exactly `https://mcp.parlay.run/mcp` and the transport is `streamable-http`.
- No tools visible: Confirm Cline reloaded the MCP settings, the `parlay` entry is under `mcpServers`, the token is present in the `Authorization` header, and the token has the required account access.
- Existing MCP servers disappeared: Restore the previous settings file and merge the `parlay` entry instead of replacing the entire `mcpServers` object.

## Notes

- Parlay is read-only. There are no trade execution tools, no order placement tools, and no fund custody tools.
- Parlay does not require users to provide upstream venue API keys for Polymarket, Kalshi, Limitless, or other venues. Venue access is handled server-side.
- Plan limits and pricing can change. See https://parlay.run/pricing for current details.
