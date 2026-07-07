<div align="center">

# UX Pilot MCP

**Generate UI designs and wireframes from natural language, right inside your AI tools.**

Connect [UX Pilot](https://uxpilot.ai) to Cursor, Claude, Codex, VS Code, Windsurf, and any other MCP-compatible client.

[![MCP](https://img.shields.io/badge/MCP-2025--11--25-000000.svg)](https://modelcontextprotocol.io/specification)
[![Website](https://img.shields.io/badge/uxpilot.ai-6C4CF1.svg)](https://uxpilot.ai)

</div>

---

The UX Pilot MCP server lets AI clients generate and iterate on UX designs through natural language. Ask your assistant to "generate a dashboard wireframe" and get back a real UX Pilot design with a shareable URL.

The server is hosted by UX Pilot. This repository documents how to connect to it and what tools it exposes. No server code lives here, so there is nothing to build or run locally.

- **Server URL:** `https://mcp.uxpilot.net/mcp`
- **Transport:** Streamable HTTP (POST)
- **Protocol:** MCP 2025-11-25 (JSON-RPC 2.0)
- **Auth:** Native OAuth sign-in, or `Authorization: Bearer` API key

## Authentication

You can connect in one of two ways.

### Native sign-in (recommended)

If your AI tool shows a UX Pilot sign-in or authorization screen, choose that flow. You log in with your UX Pilot account, review the requested access, and approve the connection. No API key to copy or paste, and access stays tied to your account and plan.

### API key

For clients that do not yet support native sign-in, generate a key from the **API Keys** tab in your UX Pilot workspace and send it as a bearer token:

```
Authorization: Bearer ep_your_api_key
```

> Enterprise workspaces: your workspace admin manages MCP/API keys. Ask an admin to generate a key if you do not see the API Keys tab.

## Setup

Replace `ep_your_api_key` with your real key in each snippet below.

<details open>
<summary><b>Claude Code</b></summary>

Native sign-in:

```bash
claude mcp add --transport http uxpilot https://mcp.uxpilot.net/mcp
```

Then run `/mcp` and approve the UX Pilot connection.

Or with an API key:

```bash
claude mcp add --transport http uxpilot https://mcp.uxpilot.net/mcp \
  --header "Authorization: Bearer ep_your_api_key"
```

</details>

<details>
<summary><b>Cursor</b></summary>

Add to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (project):

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

Then export the key in your shell (raw key, no `Bearer ` prefix; Codex adds it):

```bash
export UXPILOT_API_KEY="ep_your_api_key"
```

</details>

<details>
<summary><b>Claude Desktop / any stdio-only client</b></summary>

Clients that do not yet speak Streamable HTTP can bridge through [`mcp-remote`](https://www.npmjs.com/package/mcp-remote):

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

The server exposes five tools.

| Tool | Description | Parameters |
|------|-------------|------------|
| `generate_design` | Generate a single UI screen (wireframe or high-fidelity UI design). | `prompt`, `type` (`wireframe` \| `ui`), `platform` (`web` \| `mobile` \| `tablet`), `darkMode`, `deepDesign`, `designSystem` |
| `generate_flow_text` | Suggest screen names and descriptions for a multi-screen app. | `prompt`, `screenCount` (2-10) |
| `plan_design_flow` | Plan a multi-screen user flow and return the screens for review. Does not consume credits. | `prompt` |
| `confirm_design_flow` | Generate multiple screens after the plan is confirmed. Consumes credits. | `prompt`, `type` (`wireframe` \| `ui`), `screens` (`[{name, description}]`), `designSystem` |
| `retrieve_designs` | Retrieve the status, result, and URL of a previously generated design. | `designId` |

## Quick start

1. Use native sign-in if your client offers it. Otherwise open the **API Keys** tab in UX Pilot and generate a key.
2. Copy the config snippet for your client above.
3. Replace `ep_your_api_key` with your real key, then restart the client.
4. Ask your assistant: *"Generate a dashboard wireframe with UX Pilot."*

## Support

- Product and account help: [uxpilot.ai](https://uxpilot.ai)
- Issues with this integration: open an [issue](https://github.com/uxpilot-ai/uxpilot-mcp/issues)
- MCP specification: [modelcontextprotocol.io](https://modelcontextprotocol.io/specification)
