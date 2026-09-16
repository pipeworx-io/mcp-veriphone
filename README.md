# mcp-veriphone

Veriphone MCP — wraps the Veriphone phone-validation API (veriphone.io)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `veriphone_verify` | Validate a phone number and return whether it is valid, its line type (mobile/fixed_line/voip/etc), carrier, region, country, and canonical formats (E.164, international, local). Pass numbers in E.164 form (e.g. "+14155552671"); for local numbers supply default_country. Example: veriphone_verify({ phone: "+14155552671", _apiKey: "your-key" }) |
| `veriphone_credits` | Check the remaining verification credits / quota for your Veriphone API key — usage counter, plan limit, and pay-as-you-go balance. Example: veriphone_credits({ _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "veriphone": {
      "url": "https://gateway.pipeworx.io/veriphone/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/veriphone/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "veriphone": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-veriphone"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-veriphone
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Veriphone data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
