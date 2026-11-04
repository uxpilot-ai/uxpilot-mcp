<div align="center">

# UX Pilot MCP

**Design, edit and review in UX Pilot from Claude, ChatGPT, Cursor, Codex, and other MCP clients.**

Your AI builds screens live on the [UX Pilot](https://uxpilot.ai) canvas, or hands the brief to UX Pilot's own design agent. Then it reviews, versions and ships the work: React prototypes, preview links, videos and media.

[![MCP](https://img.shields.io/badge/MCP-2025--11--25-000000.svg)](https://modelcontextprotocol.io/specification)
[![Website](https://img.shields.io/badge/uxpilot.ai-6C4CF1.svg)](https://uxpilot.ai/mcp)

</div>

---

The UX Pilot MCP server connects any MCP-compatible AI client to your UX Pilot workspace. Version 3.0 has 15 general tools. Each one covers a kind of work (find, read, edit, review, export and so on), and you choose the action inside the tool. That keeps the tool list short and leaves more of your AI's context for the work.

The server is hosted by UX Pilot. This repository documents how to connect to it and what it exposes. There is no server code here to build or run.

- **Server URL:** `https://mcp.uxpilot.net/mcp`
- **Transport:** Streamable HTTP (POST)
- **Protocol:** MCP 2025-11-25 (JSON-RPC 2.0)
- **Auth:** Native OAuth sign-in (recommended), or `Authorization: Bearer` API key fallback
- **Tools:** 15 (version 3.0)
- **Free plan:** connect with a free UX Pilot account and get 50 actions a week

## What your AI can do

- **Design on the canvas, live.** Your AI places a screen as HTML, then builds it one section at a time while you watch it appear on the open canvas. Each step comes back with a picture of the result, and design checks point out problems with fixes.
- **Run UX Pilot's design agent.** Hand a brief to UX Pilot's own agent for screens, whole flows or a React app, on the standard, pro or ultra tier. Or mix the two: the agent drafts and your AI refines.
- **Review.** UX reviews, persona reviews, attention heatmaps and gaze flow, on a UX Pilot screen, raw HTML or a live URL. Heatmaps and gaze flow also take a screenshot. Fixes land as a new version.
- **Make videos and media.** Product reels, ads, explainers, social clips and logo animations, exported as MP4, WebM or GIF. Images, image edits, voiceovers, music and sound effects.
- **Use your design system.** Import one from code, a repository, a website or tokens, then design on brand with its guide.
- **Ship.** Turn screens into a React app, edit its files, publish it, or share a public preview link.
- **Organize.** Pages, folders, groups, versions, canvas notes, diagrams and comments.

## Free plan

Free UX Pilot accounts can connect and use every tool:

- 50 actions a week that write or run something (imports, edits, organizing, designs, reviews, media, videos). Reading, searching, checking jobs and stopping runs are not counted. The week resets Monday 00:00 UTC.
- Design runs use the standard tier. Some features need a paid plan, the same as in the app. When one does, the tool says what is needed and links to the plans.
- AI runs spend credits, as they do in the app.

Paid plans have no weekly action limit. See [uxpilot.ai/plans](https://uxpilot.ai/plans).

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
1. Open Claude settings, go to Connectors, and pick **UX Pilot** from the directory. Or add a custom connector with the server URL: `https://mcp.uxpilot.net/mcp`
2. Sign in with your UX Pilot account and approve access.
3. In a conversation, ask Claude to find your UX Pilot pages, or to design something on the canvas.

**Claude Code:**

```bash
claude mcp add --transport http uxpilot https://mcp.uxpilot.net/mcp
```

Then run `/mcp` in Claude Code and sign in.

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
4. Enable the connector in a chat and ask ChatGPT to find your UX Pilot pages.

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

15 tools. Most take a `kind` or `action` that picks what to do, and a `params` object with that action's fields. Ask the server with `guide({ topic: "<tool>.<action>" })`, for example `guide({ topic: "edit.patch" })`, for an action's fields and an example.

| Tool | What it does | `kind` / `action` | Needs your OK |
|------|--------------|-------------------|---------------|
| `find` | Find work: pages, folders, screens and React apps by keyword, themes, symbols, design systems, diagrams, React files and code | pages, workstreams, designs, react_projects, themes, symbols, design_systems, diagrams, react_files, react_code | |
| `read` | Read one thing in full: a page with its canvas, layout or picture, a screen and its HTML, versions and diffs, a preview link, design checks, a design system and its guide, review scores, comments | page, design, designs, versions, version_diff, preview, checks, theme, symbol, design_system, design_system_guide, react_file, review_scores, comments | |
| `catalog` | What you can pick from: design tiers, media models, review and export types, and what each paid call costs | tiers, media_models, media_kinds, reviews, exports, credits | |
| `guide` | Short how-to docs: ways to design, the canvas, small steps, HTML, videos, React, design systems, reviews, the free plan, confirmations | `topic` | |
| `get_job` | Follow a running job: design runs, React edits, videos, imports, reviews, video exports, design system imports | agent, generation, review, export, design_system_import | |
| `stop_job` | Stop a design, React or video run | | |
| `edit` | Change content with no AI and no credits: patch a screen's HTML one section at a time, replace it, save a version, make a version from comments, edit React files or a diagram | patch, replace, save_version, version_from_comments, react_files, diagram | |
| `manage` | Organize, never delete: pages, templates, bookmarks, folders, groups, screens, canvas notes and shapes, images, diagrams, comments and replies, accessibility reports | create_page, page_from_template, update_page, bookmark_page, move_page, create_workstream, update_workstream, create_group, update_group, save_group, move_group, duplicate_design, move_design, update_design, save_design, add_items, update_items, update_assets, create_diagram, comment, reply, update_comment, update_reply, resolve_comment, resolve_comments, accessibility_report | |
| `import` | Bring things in: your HTML as a new screen (free), a live URL, images by URL or upload, a design system from code, a repository, a website or tokens | html, url, assets, upload, design_system | Credits (url, or html with AI refine) |
| `delete` | Delete pages, folders, groups, canvas items, images, diagrams, comments and replies, or archive a screen | page, workstream, group, design, canvas_items, canvas_assets, diagram, comment, reply | Always |
| `design` | UX Pilot's AI: the design agent for screens, flows or a new React app on the standard, pro or ultra tier, screens into a React app, React changes from a prompt, flowcharts | agent, react, react_edit, flowchart, flowchart_edit | Credits |
| `make_video` | Make or change a video from a brief: product reel, ad, explainer, social clip or logo animation, in 16:9, 9:16 or 1:1 | | Credits |
| `generate_media` | Make media for a page: images, image edits, image processing, voiceovers, music, sound effects | image, edit_image, process_image, voiceover, music, sound_effect | Credits |
| `review` | Review a screen, raw HTML or a live URL: UX review, persona review, attention heatmap, gaze flow (these two also take a screenshot). `apply: true` fixes ux, persona or accessibility issues as a new version | ux, persona, heatmap, gaze_flow, accessibility | Credits (a persona review is free) |
| `export` | Export a video as MP4, WebM or GIF, publish a public preview link, publish or rebuild a React app | video, preview, prototype, react_build | Credits or publishing |

### Confirmation

Calls that spend credits, publish or delete never run on the AI's word alone:

1. The first call answers with a summary and a `confirmationToken` instead of running.
2. Your AI tells you what will happen and asks.
3. If you agree, it calls again with the same params plus `confirmed: true` and the token. A token works once, for that exact call, and expires after 15 minutes.

### Upgrading from 2.x

Version 2.x listed 87 separate tools, such as `search_designs`, `start_design_agent` and `patch_design_html`. Each one is now an action of a 3.0 tool. For example, `search_designs` is `find` with kind `designs`, `start_design_agent` is `design` with action `agent`, and `patch_design_html` is `edit` with action `patch`. The 2.x tool list is still served at `https://mcp.uxpilot.net/mcp?toolset=legacy` for now, and it will be removed in a later release.

## Quick start

1. Pick the AI client you want to use with UX Pilot.
2. Add the server URL with the native sign-in steps above.
3. If your client needs static headers, generate an API key and replace `ep_your_api_key` in the fallback config.
4. Ask your assistant, for example:
   - *"Design a pricing page for my app on the UX Pilot canvas, one section at a time."*
   - *"Have UX Pilot's design agent make a 3-screen onboarding flow on the pro tier."*
   - *"Run a UX review on my checkout screen in UX Pilot and fix what it finds."*
   - *"Make a 15-second 9:16 product reel for my app and export it as MP4."*

## Support

- Product and account help: [uxpilot.ai](https://uxpilot.ai)
- MCP overview: [uxpilot.ai/mcp](https://uxpilot.ai/mcp)
- Issues with this integration: open an [issue](https://github.com/uxpilot-ai/uxpilot-mcp/issues)
- MCP specification: [modelcontextprotocol.io](https://modelcontextprotocol.io/specification)
