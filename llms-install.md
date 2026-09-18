# RoboPartPicker MCP server — install notes for agents

RoboPartPicker exposes a hosted, read-only MCP server. There is nothing to install and no
API key for public reads.

Endpoint: `https://robopartpicker.com/mcp` (Streamable HTTP)
Registry name: `com.robopartpicker/robopartpicker`

## Add to a client

For clients configured with an `mcpServers` block (Claude Desktop, Cursor, Cline, Windsurf):

```json
{
  "mcpServers": {
    "robopartpicker": {
      "type": "streamable-http",
      "url": "https://robopartpicker.com/mcp"
    }
  }
}
```

For Claude Code:

```bash
claude mcp add --transport http robopartpicker https://robopartpicker.com/mcp
```

## Confirm it works

```bash
curl -s https://robopartpicker.com/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"curl","version":"1.0"}}}'
```

Expect `serverInfo.name` = `RoboPartPicker`. Then `tools/list` returns `search_projects`,
`get_project`, `search_components`, `compare_components` and `validate_rpps`.

## Notes

- Read-only. No write tools, no authentication for public data.
- Prices returned are observed catalog data, not binding quotes.
- BOM lines carry an evidence state (verified / probable / unresolved / unpriced); unresolved
  lines are kept visible rather than dropped.
