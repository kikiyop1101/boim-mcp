# Installing the BOIM MCP server (for AI coding agents such as Cline)

BOIM is a **remote** MCP server. There is nothing to clone, build or install, and no API key is needed for the free tier.

- Endpoint: `https://boim.io/api/mcp`
- Transport: Streamable HTTP (read-only, 6 tools)
- Auth: none (free tier returns up to 5 results per call plus `not_shown` and a `more` link)

## Cline

Add this entry to `cline_mcp_settings.json` (Cline → MCP Servers → Configure), then restart the session:

```json
{
  "mcpServers": {
    "boim": {
      "url": "https://boim.io/api/mcp",
      "type": "streamableHttp"
    }
  }
}
```

Verified on 2026-10-20 with Cline CLI 3.0.73: after adding the entry, a new session listed the `boim` server and `boim__find_businesses` (region `세종`, category `미용실`) returned 580 businesses.

## Check that it works

Ask: "세종 보람동 미용실 찾아 줘" (hair salons in Boram-dong, Sejong) — the agent should call `find_businesses`.

Other clients (Claude, ChatGPT, Cursor, VS Code): see [README.md](README.md).
