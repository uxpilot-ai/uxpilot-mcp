<div align="center">

# UX Pilot MCP

**Work directly inside UX Pilot from Claude, ChatGPT, Cursor, Codex, and other MCP clients.**

Bring AI-generated work into [UX Pilot](https://uxpilot.ai), organize it, review and version it, build prototypes, then switch back to UX Pilot AI for deeper product-aware generation.

[![MCP](https://img.shields.io/badge/MCP-2025--11--25-000000.svg)](https://modelcontextprotocol.io/specification)
[![Website](https://img.shields.io/badge/uxpilot.ai-6C4CF1.svg)](https://uxpilot.ai)

</div>

---

The UX Pilot MCP server connects any MCP-compatible AI client to your UX Pilot workspace. Your assistant can create workstreams and pages, import HTML or live URLs as designs, run UX and accessibility reviews, manage versions and comments, and generate React prototypes, all inside UX Pilot.

The server is hosted by UX Pilot. This repository documents how to connect to it and what it exposes. There is no server code here to build or run.

- **Server URL:** `https://mcp.uxpilot.net/mcp`
- **Transport:** Streamable HTTP (POST)
- **Protocol:** MCP 2025-11-25 (JSON-RPC 2.0)
- **Auth:** Native OAuth sign-in (recommended), or `Authorization: Bearer` API key fallback
- **Tools:** 77

## Authentication

### Native sign-in (recommended)

If your AI tool shows a UX Pilot sign-in or authorization screen, choose that flow. You log in with your UX Pilot account, review the requested access, and approve the connection. No API key to copy or paste, and access stays tied to your account and plan.

### API key fallback

For clients that only support static headers, generate a key from the **API Keys** tab in your UX Pilot workspace and send it as a bearer token:

```
Authorization: Bearer ep_your_api_key
```

> Enterprise workspaces: your admin manages MCP/API keys. Keep the key private and rotate it if access changes.

## Setup

Prefer native sign-in wherever the client supports it. Use the API key config as a fallback. Replace `ep_your_api_key` with your real key.

<details open>
<summary><b>Claude</b></summary>

**Native connector (recommended):**
1. Open Claude settings and add a custom connector / remote MCP server.
2. Paste the server URL: `https://mcp.uxpilot.net/mcp`
3. Choose the UX Pilot sign-in flow when Claude offers it and approve access.
4. In a conversation, ask Claude to list UX Pilot tools.

**API key fallback (Claude Desktop, via `mcp-remote`):** edit `claude_desktop_config.json`
(macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`,
Windows: `%APPDATA%\Claude\claude_desktop_config.json`)

```json
{
  "mcpServers": {
    "uxpilot": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp.uxpilot.net/mcp",
        "--transport",
        "http-only",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer ep_your_api_key"
      }
    }
  }
}
```

Requires Node.js. `mcp-remote` bridges stdio to Streamable HTTP.

</details>

<details>
<summary><b>ChatGPT</b></summary>

Use ChatGPT's custom connector / MCP support (Developer mode, availability depends on your plan):
1. Open ChatGPT settings and add a custom connector / MCP server.
2. Paste the server URL: `https://mcp.uxpilot.net/mcp`
3. Complete the UX Pilot sign-in when prompted.
4. Enable the connector in a chat and ask ChatGPT to list UX Pilot tools.

</details>

<details>
<summary><b>Cursor</b></summary>

Add to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (project). Adding the URL alone triggers native sign-in if supported; the header is the API key fallback.

```json
{
  "mcpServers": {
    "uxpilot": {
      "url": "https://mcp.uxpilot.net/mcp",
      "headers": {
        "Authorization": "Bearer ep_your_api_key"
      }
    }
  }
}
```

</details>

<details>
<summary><b>VS Code + Copilot</b></summary>

Add to `.vscode/mcp.json`:

```json
{
  "servers": {
    "uxpilot": {
      "type": "http",
      "url": "https://mcp.uxpilot.net/mcp",
      "headers": {
        "Authorization": "Bearer ep_your_api_key"
      }
    }
  }
}
```

</details>

<details>
<summary><b>Windsurf</b></summary>

Add to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "uxpilot": {
      "serverUrl": "https://mcp.uxpilot.net/mcp",
      "headers": {
        "Authorization": "Bearer ep_your_api_key"
      }
    }
  }
}
```

</details>

<details>
<summary><b>Codex</b></summary>

Add to `~/.codex/config.toml`:

```toml
[mcp_servers.uxpilot]
url = "https://mcp.uxpilot.net/mcp"
bearer_token_env_var = "UXPILOT_API_KEY"
```

Then export the raw key (no `Bearer ` prefix; Codex adds it):

```bash
export UXPILOT_API_KEY="ep_your_api_key"
```

</details>

<details>
<summary><b>Any MCP client</b></summary>

Clients that do not speak Streamable HTTP can bridge through [`mcp-remote`](https://www.npmjs.com/package/mcp-remote):

```json
{
  "mcpServers": {
    "uxpilot": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp.uxpilot.net/mcp",
        "--transport",
        "http-only",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer ep_your_api_key"
      }
    }
  }
}
```

</details>

## Tools

77 tools, grouped by what they do.

### Organize UX Pilot work
Create and maintain workstreams, pages, groups, and design metadata so outside AI work stays organized inside UX Pilot.

`list_workstreams` `create_workstream` `create_page` `create_page_from_template` `toggle_page_bookmark` `create_group` `move_group_to_page` `duplicate_design` `move_design_to_page` `archive_design`

### Bring in external AI output
Import HTML, capture public URLs, place reference images, add durable canvas notes and diagrams from any MCP client.

`import_html_design` `import_live_url` `add_canvas_assets` `upload_canvas_asset` `add_canvas_items` `update_canvas_assets` `list_design_collections` `get_design_collection` `generate_flowchart` `edit_flowchart` `list_diagrams` `create_diagram` `update_diagram` `delete_diagram` `delete_canvas_items` `delete_canvas_assets`

### Review, version, collaborate
Run UX reviews, save accessibility findings, compare versions, and turn AI feedback into visible UX Pilot comments.

`run_ux_review` `get_review_status` `apply_ux_review_fixes` `save_accessibility_report` `apply_accessibility_fixes` `compare_review_scores` `apply_persona_audit_fixes` `update_comment` `delete_comment` `update_comment_reply` `delete_comment_reply` `create_version_from_comments` `create_design_version` `compare_design_versions`

### Prototype and publish
Generate React prototypes from designs, edit safely with child versions, rebuild, and publish previews.

`generate_react_prototype` `edit_react_prototype` `build_react_prototype` `publish_prototype`

## Quick start

1. Pick the AI client you want to use with UX Pilot.
2. Use the native sign-in steps above with the server URL.
3. If your client needs static headers, generate an API key and replace `ep_your_api_key` in the fallback config.
4. Ask your assistant: *"Create a UX Pilot page, import this HTML as a design, then run a UX review."*

## Support

- Product and account help: [uxpilot.ai](https://uxpilot.ai)
- Issues with this integration: open an [issue](https://github.com/uxpilot-ai/uxpilot-mcp/issues)
- MCP specification: [modelcontextprotocol.io](https://modelcontextprotocol.io/specification)
