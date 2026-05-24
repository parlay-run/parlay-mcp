# Installing Parlay MCP Server

Parlay is a hosted remote MCP server. There is no local code to clone, build, or run. Installation is a configuration step only.

## Step 1: Get a Parlay Personal Access Token

Ask the user to visit https://parlay.run/settings/tokens, generate a Personal Access Token, and provide it for MCP configuration. The token starts with `parlay_pat_`.

Treat the token as a secret. Do not print it unnecessarily, log it, commit it, or store it anywhere except the user's local MCP settings file.

Do not attempt to generate the token programmatically. Token generation requires the user to log in to parlay.run in a browser.

## Step 2: Add Parlay to Cline's MCP configuration

**Strongly preferred: use Cline's Remote Servers UI.** Manual JSON editing can be brittle across Cline versions and surfaces because Cline-specific transport fields have changed or behaved differently over time (see https://github.com/cline/cline/issues/6767 for historical context). Use the UI when possible.

### Option A (preferred): Cline Remote Servers UI

1. Open Cline's MCP Servers view, then open the **Remote Servers** tab.
2. Click "Add Server".
3. Fill in:
   - **Server Name**: `parlay`
   - **Server URL**: `https://mcp.parlay.run/mcp`
   - **Transport Type**: `Streamable HTTP`
4. After the server is created, expand its entry and add a header:
   - Header name: `Authorization`
   - Header value: `Bearer PARLAY_TOKEN_FROM_USER` (replace placeholder with the actual token from the user)
5. Save.

### Option B (fallback): Manual JSON edit

If the UI is unavailable, edit Cline's MCP settings file directly. **Do NOT copy `.mcp.json` from the root of this repo verbatim into Cline.** That file is a generic template for multiple MCP clients; Cline's accepted schema is different. Adapt to the Cline-specific form below.

Locate the Cline MCP settings file:
- **Cline CLI**: `~/.cline/mcp.json` (macOS/Linux), `%APPDATA%\cline\mcp.json` (Windows)
- **Cline VS Code extension (v1.102+)**: open Cline → Configure tab → "Configure MCP Servers" (the UI surfaces the correct path automatically)
- **Cline VS Code extension (pre-v1.102, fallback)**:
  - macOS: `~/Library/Application Support/Code/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json`
  - Linux: `~/.config/Code/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json`
  - Windows: `%APPDATA%\Code\User\globalStorage\saoudrizwan.claude-dev\settings\cline_mcp_settings.json`

Add this entry to `mcpServers`. If `mcpServers` already contains other entries, **merge** the parlay entry — do not overwrite or remove existing servers.

```json
{
  "mcpServers": {
    "parlay": {
      "url": "https://mcp.parlay.run/mcp",
      "headers": {
        "Authorization": "Bearer PARLAY_TOKEN_FROM_USER"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

**Important Cline-specific notes:**

- **Token literal**: Do not rely on `${PARLAY_TOKEN}` expansion in Cline manual JSON; write the literal token in the `Authorization` header.
- **Timeout**: If you need to set a connection timeout, use Cline's own `timeout` setting (in seconds). Do NOT copy `connectionTimeoutMs` from the generic `.mcp.json` template — Cline does not recognize that field.
- **Transport fallback**: If Cline defaults to SSE transport or fails to connect, add `"type": "streamableHttp"` (note: camelCase, not `streamable-http`) to the parlay entry as a hint.

### Token safety reminder

Treat the token as a secret. Do not print, log, or commit it. The token should only exist in the Cline settings file on the user's local machine.

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
