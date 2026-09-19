# RoboPartPicker MCP server

Remote [Model Context Protocol](https://modelcontextprotocol.io) server for
[RoboPartPicker](https://robopartpicker.com) — a catalog of source-linked robotics
projects, bills of materials and components.

- **Endpoint:** `https://robopartpicker.com/mcp` (Streamable HTTP, no auth for public reads)
- **Registry name:** `com.robopartpicker/robopartpicker`
- **Product repository:** [brainbook0/robopartpicker](https://github.com/brainbook0/robopartpicker)
- **Discovery:** `https://robopartpicker.com/.well-known/mcp.json`
- **Server card:** `https://robopartpicker.com/.well-known/mcp/server-card.json`
- **OpenAPI:** `https://robopartpicker.com/openapi.json`

This repository holds the registry manifest and client configuration for the hosted server.
The server itself runs on Cloudflare Workers at robopartpicker.com.

## Tools

| Tool | What it does |
|---|---|
| `search_projects` | Search open robotics projects by keyword, category or kind. |
| `get_project` | Fetch one project with its source repo, license, files and BOM summary. |
| `search_components` | Search the technical component catalog by name, category or maker. |
| `compare_components` | Compare two to four components. Same category never implies verified drop-in compatibility. |
| `validate_rpps` | Validate a Robotics Project Package Specification (RPPS) document. |

## Connect a client

### Claude Desktop / Cursor / Cline

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

### Claude Code

```bash
claude mcp add --transport http robopartpicker https://robopartpicker.com/mcp
```

### Verify without a client

```bash
curl -s https://robopartpicker.com/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"curl","version":"1.0"}}}'
```

A healthy response reports `serverInfo.name` = `RoboPartPicker` and a `tools` capability.

## Example

Ask an agent: *"Find an open 6-axis robot arm project with a bill of materials, and tell me
which BOM lines are still unresolved."* A `search_projects` call with `q=rover` returns
entries such as:

```json
{
  "slug": "nasa-jpl-open-source-rover",
  "name": "NASA JPL Open Source Rover",
  "license": "Apache-2.0"
}
```

## What the data is, honestly

- BOM lines are derived from explicit BOM files, documentation, CAD metadata and
  URDF/robot-description files in each upstream repository. Every line keeps its evidence
  state — verified, probable, unresolved, unpriced — instead of being silently dropped.
- Prices are **observed catalog data, not quotes**. Supplier coverage is partial and prices
  drift.
- Upstream projects keep their own licenses. This server reads and links; it does not
  relicense anyone's design.
- Software projects, commercial models without manufacturer BOMs, and physical projects
  without an explicit BOM report distinct honest states rather than guessed parts.

## License

The contents of this repository (manifest, documentation, configuration) are MIT licensed.
Robotics project data returned by the API remains subject to each upstream project's own
license.
