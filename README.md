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

77 tools, grouped by what they do. Tools marked **· credits** consume account credits; credit-consuming and destructive actions require explicit confirmation.

<details open>
<summary><b>Discover and inspect (12)</b></summary>

| Tool | Description |
|------|-------------|
| `list_workstreams` | List your workstreams/folders with page summaries |
| `list_pages` | List your pages with URLs, counts, and thumbnails |
| `get_page_context` | Get a page's groups, designs, canvas, and comments |
| `get_design` | Get one design's details, preview URL, optional HTML |
| `get_design_preview` | Get a design's preview/page URL and status (no HTML) |
| `get_design_versions` | Get a design's version/parent family |
| `list_design_collections` | List your custom design-system collections |
| `get_design_collection` | Get a collection's assets, styles, and docs |
| `list_themes` | List available brand/style themes |
| `get_theme` | Get a theme's full JSON for on-brand output |
| `list_symbols` | List reusable editor components/symbols |
| `get_symbol` | Get a symbol's HTML/CSS content |

</details>

<details>
<summary><b>Organize your workspace (19)</b></summary>

| Tool | Description |
|------|-------------|
| `create_workstream` | Create a workstream/folder |
| `update_workstream` | Rename or re-describe a workstream |
| `delete_workstream` | Delete a workstream and detach its pages |
| `create_page` | Create a blank page |
| `create_page_from_template` | Create a page/group from a template |
| `update_page` | Update page name, prompt, or AI context |
| `delete_page` | Delete a page |
| `move_page_to_workstream` | Move a page into or out of a folder |
| `toggle_page_bookmark` | Bookmark or unbookmark a page |
| `create_group` | Create a design group on a page |
| `update_group` | Update a group's name, font, or designs |
| `delete_group` | Delete a group and its designs |
| `move_group_to_page` | Move a group and its designs to another page |
| `set_group_saved` | Save or unsave a group to your library |
| `duplicate_design` | Duplicate a design onto a page/group |
| `move_design_to_page` | Move a design to another page/group |
| `archive_design` | Archive and detach a design |
| `update_design_metadata` | Update a design's title, prompt, position, size |
| `set_design_saved` | Save or unsave a design to your library |

</details>

<details>
<summary><b>Generate with UX Pilot AI (5)</b></summary>

| Tool | Description |
|------|-------------|
| `generate_design` | Generate one screen from a prompt **· credits** |
| `plan_design_flow` | Plan a multi-screen flow for review (no credits) |
| `confirm_design_flow` | Generate the confirmed multi-screen flow **· credits** |
| `get_generation_status` | Poll generation, import, and build status |
| `retrieve_designs` | Poll status/URLs/HTML for designs (legacy) |

</details>

<details>
<summary><b>Bring in external AI output (15)</b></summary>

| Tool | Description |
|------|-------------|
| `import_html_design` | Import external HTML as a previewable design **· credits** |
| `import_live_url` | Capture a public URL as a design **· credits** |
| `add_canvas_assets` | Add URL-backed image/reference assets to a page |
| `upload_canvas_asset` | Upload base64/data-URL images to a page |
| `update_canvas_assets` | Update existing canvas assets and linked items |
| `delete_canvas_assets` | Delete canvas assets |
| `add_canvas_items` | Add sticky notes, annotations, and flow nodes |
| `update_canvas_items` | Move, relabel, or restyle canvas items |
| `delete_canvas_items` | Delete canvas items |
| `generate_flowchart` | Generate a flowchart graph from a prompt |
| `edit_flowchart` | Edit an existing flowchart from an instruction |
| `list_diagrams` | List saved diagrams on a page |
| `create_diagram` | Create a saved sitemap/journey/process diagram |
| `update_diagram` | Update a diagram's metadata, nodes, or edges |
| `delete_diagram` | Delete a diagram |

</details>

<details>
<summary><b>Review, audit, and accessibility (8)</b></summary>

| Tool | Description |
|------|-------------|
| `run_ux_review` | Run a UX review, persist findings as comments **· credits** |
| `get_review_status` | Poll an async UX review job |
| `apply_ux_review_fixes` | Apply review findings as a child version **· credits** |
| `run_persona_audit` | Run a persona-based audit, persist findings |
| `apply_persona_audit_fixes` | Apply persona findings as a child version **· credits** |
| `save_accessibility_report` | Save an external axe-style a11y report |
| `apply_accessibility_fixes` | Apply a11y violations as a child version **· credits** |
| `compare_review_scores` | Compare before/after review scores (read-only) |

</details>

<details>
<summary><b>Versions and comments (12)</b></summary>

| Tool | Description |
|------|-------------|
| `create_design_version` | Create a version preserving parent history |
| `compare_design_versions` | Compare two designs/versions (metadata + HTML diff) |
| `create_version_from_comments` | Create a child version from fixed HTML and comments |
| `create_comment` | Comment on a page or anchored to a design/shape |
| `update_comment` | Edit your comment's text, mentions, or position |
| `delete_comment` | Delete a comment and its replies |
| `reply_to_comment` | Reply to a comment thread |
| `update_comment_reply` | Edit your reply |
| `delete_comment_reply` | Delete a reply |
| `resolve_comment` | Resolve or reopen a comment |
| `resolve_comments_with_version_summary` | Reply to and resolve comments with a version summary |
| `summarize_comments` | List and summarize page comments |

</details>

<details>
<summary><b>Prototype and publish (5)</b></summary>

| Tool | Description |
|------|-------------|
| `generate_react_prototype` | Generate a React app from designs **· credits** |
| `edit_react_prototype` | Edit an existing prototype |
| `build_react_prototype` | Queue a build/publish refresh for a prototype |
| `publish_prototype` | Publish a prototype as a hosted URL |
| `publish_design_preview` | Publish a design's HTML as a hosted preview **· credits** |

</details>

> Plus `request_action_confirmation`, a safety utility that mints a confirmation token for risky actions.

## Quick start

1. Pick the AI client you want to use with UX Pilot.
2. Use the native sign-in steps above with the server URL.
3. If your client needs static headers, generate an API key and replace `ep_your_api_key` in the fallback config.
4. Ask your assistant: *"Create a UX Pilot page, import this HTML as a design, then run a UX review."*

## Support

- Product and account help: [uxpilot.ai](https://uxpilot.ai)
- Issues with this integration: open an [issue](https://github.com/uxpilot-ai/uxpilot-mcp/issues)
- MCP specification: [modelcontextprotocol.io](https://modelcontextprotocol.io/specification)
