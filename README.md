<div align="center">

<img src="assets/neo-logo.png" width="128" height="128" alt="Neo" />

# Neo

**A local-first agentic AI coding assistant for macOS.**

<br />

<a href="https://github.com/erphq/neo-releases/releases/latest/download/Neo_0.1.0_aarch64.dmg">
  <img src="https://img.shields.io/badge/Download%20for%20macOS-black?style=for-the-badge&logo=apple&logoColor=white&labelColor=black" alt="Download for macOS" height="44" />
</a>

<br /><br />

![macOS](https://img.shields.io/badge/macOS-10.13+-000?logo=apple&logoColor=white)
![Apple Silicon](https://img.shields.io/badge/Apple%20Silicon-M1%2FM2%2FM3%2FM4-000?logo=apple&logoColor=white)
![Notarized](https://img.shields.io/badge/Notarized-Apple%20Developer%20ID-success?logo=apple&logoColor=white)
[![Latest release](https://img.shields.io/github/v/release/erphq/neo-releases?label=latest&color=blue)](https://github.com/erphq/neo-releases/releases/latest)

[**What is Neo**](#what-is-neo) · [**Features**](#features) · [**Install**](#install) · [**Auto-updates**](#auto-updates) · [**Support**](#support--issues)

</div>

<br />

This repository hosts signed + notarized macOS builds of [**Neo**](https://github.com/erphq/neo). Source code lives in the main [erphq/neo](https://github.com/erphq/neo) repo — this one only holds release artifacts.

<br />

## What is Neo

Neo is a **native desktop agentic coding assistant** — a full reasoning loop that observes, plans, acts, and reflects on your codebase. It runs locally as a Tauri v2 app, talks to any LLM through OpenRouter, and ships with 26 built-in tools plus Model Context Protocol (MCP) support for unlimited extension.

Unlike cloud IDEs, nothing leaves your machine except the LLM API call itself. Unlike terminal CLIs, you get a proper native UI with block-level streaming, a permission engine, thread history, and 14 themes.

> *"I know kung fu."* — You, after Neo writes your entire test suite.

<br />

## Features

### Agentic reasoning loop

Neo runs a multi-turn **Thought → Action → Observation** cycle up to 40 iterations per message. Each turn streams text and tool calls, executes them, feeds observations back into the next turn, and keeps going until the task is done.

- **Parallel read, serial write.** Read-only tools (`read`, `glob`, `grep`, `ls`, `web_fetch`) run concurrently via `Promise.all`. Mutating tools (`edit`, `write`, `bash`) run sequentially to prevent race conditions. Same pattern as database query optimizers.
- **First-class cancellation.** Every tool execution receives an `AbortSignal`. Cancel mid-loop → zero partial state. No half-written files, no zombie processes.
- **Loop detection.** A sliding-window detector catches degenerate patterns (same tool 5× in a row, A → B → A oscillation, repeating errors) and injects corrective hints before tokens burn.

### 26 built-in tools

| Category | Tools |
| --- | --- |
| **File ops** (10) | `read`, `write`, `edit`, `multiedit`, `glob`, `grep`, `ls`, `read_many_files`, `replace`, `apply_patch` |
| **Shell** (1) | `bash` — 2-minute timeout, dangerous-command detection, background tasks, 30KB output cap |
| **Web** (2) | `web_fetch`, `web_search` |
| **Memory** (2) | `memory_read`, `memory_write` — persistent, cross-session |
| **Tasks** (2) | `todowrite`, `todoread` — scratchpad the agent maintains itself |
| **Skills** (2) | `list_skills`, `use_skill` — reusable, domain-specific prompt packs |
| **Interaction** (1) | `question` — structured multi-choice wizard for ambiguous requests |
| **Planning** (2) | `plan_enter`, `plan_exit` — read-only sandbox for drafting before executing |
| **Sub-agents** (1) | `task` — spawns an isolated agent with its own 20-iteration budget |
| **MCP** (∞) | Any tool from any connected MCP server |

All tools are defined via a typed `Tool.define()` API with Zod schema validation, automatic output truncation, and per-call execution timing.

### Model Context Protocol (MCP)

Drop a `.mcp.json` in your workspace and Neo auto-discovers and connects to your MCP servers on startup. Every MCP tool goes through the same permission checks, output truncation, and state tracking as built-in tools — no second-class citizens.

```json
{
  "servers": {
    "postgres": { "command": "npx", "args": ["-y", "@mcp/postgres"] },
    "github":   { "command": "npx", "args": ["-y", "@mcp/github"] }
  }
}
```

### Context engineering

Three layers of context management, because every token counts.

- **`NEO.md` — project rules.** A markdown file in your workspace that gets injected into every system prompt. Your steering wheel: tech stack conventions, forbidden paths, test commands.
- **`MEMORY.md` — persistent memory.** A markdown file the agent reads and writes itself. No vector DB, no embeddings, no indexing pipeline. Cross-session state — architecture decisions, known issues, gotchas — survives restarts and new workspaces.
- **Conversation compression.** When history exceeds 30 messages, Neo compresses older turns into structured summaries while preserving the last 8 verbatim. Token budget is estimated before and after. File paths, tool calls, and past decisions are never lost.

### Skills

Reusable prompt packs for domain-specific workflows. A skill is a folder with a `SKILL.md` — Neo discovers them automatically, exposes them via `list_skills` and `use_skill`, and the agent picks the right one for the task. Ship your own, share them across workspaces.

### Permission engine

Every tool call flows through a typed permission check before it runs.

- **Read-only tools** → auto-allow.
- **Write tools** (`edit`, `write`, `bash`) → prompt the user inline.
- **Dangerous commands** (`rm -rf`, `sudo`, `git push --force`, `chmod 777`) → always prompt, even if the ruleset says allow.
- **Session approvals.** "Allow always" persists for the session, resets on clear. Configurable per-tool, per-pattern.

### Sub-agents

Neo can spawn isolated sub-agents via the `task` tool — each gets its own 20-iteration reasoning budget and independent context window. Useful for parallelizable work (research N things, then synthesize) without polluting the main thread.

### Thread management + session recording

- Every conversation is a thread, persisted across restarts.
- Sessions recorded as JSONL in `.neo/sessions/` — full replay, debug, search.
- Sidebar thread list, global search, rename/pin/delete.

### Native macOS chrome

Built with Tauri v2 and React 19 — not an Electron wrapper around a web view.

- Custom titlebar with traffic lights, workspace path, new-window, settings, new-thread buttons
- Auto-detects installed editors (VS Code, Cursor, Zed, JetBrains, Sublime) and offers one-click open
- Reveal in Finder, Open in Terminal — straight from the workspace bar
- Block-level streaming UI — see tool calls render as structured cards as they execute, not as raw JSON

### Themes

14 built-in themes (Dracula, Monokai, Solarized Light/Dark, Nord, Tokyo Night, Catppuccin variants, and more), plus custom themes via `~/.neo/themes/*.json`. Auto-switch between light and dark on a time schedule. Per-theme accent color override, density control (compact / comfortable), custom font family + size.

### In-app auto-updates

This repo is the update source. Silent background check on launch, dot on the Settings icon when a new version ships, one-click install from **Settings → About**. Update payloads are minisign-signed; the public key is embedded in the app and unverified tarballs are rejected before install.

<br />

## Install

1. Click the download button above (or pick from [Releases](https://github.com/erphq/neo-releases/releases))
2. Open the `.dmg`, drag **Neo** into **Applications**
3. Launch from `/Applications/Neo.app`

No Gatekeeper prompt. No right-click → Open workaround. It just opens.

**First-run setup:** open Settings → paste an [OpenRouter](https://openrouter.ai) API key → open a folder → start building.

<details>
<summary>Why there's no Gatekeeper warning</summary>

The app is:
- **Code-signed** with a Developer ID Application certificate (Deskera Holdings Ltd., team `DMZC7LUBK8`)
- **Notarized** by Apple's notary service — both the `.app` bundle and the `.dmg` container
- **Stapled** — Gatekeeper verifies the notarization ticket offline, no internet needed on first launch

Verify the download yourself:

```bash
spctl --assess --type open --context context:primary-signature -vv ~/Downloads/Neo_*_aarch64.dmg
# → accepted
# → source=Notarized Developer ID
# → origin=Developer ID Application: Deskera Holdings Ltd. (DMZC7LUBK8)

xcrun stapler validate ~/Downloads/Neo_*_aarch64.dmg
# → The validate action worked!
```

</details>

<br />

## Auto-updates

Neo checks this repository silently on launch. When a new version is published, a green dot appears on the Settings icon in the titlebar.

To update, open **Settings → About → Download and install**. The app downloads, verifies the signature, installs, and relaunches itself.

You can also trigger a check manually from **Settings → About → Check for Updates**.

Update artifacts are signed with a dedicated ed25519 key (minisign). The app embeds the matching public key at build time — any tarball that doesn't verify is rejected before install.

<br />

## What's in a release

Every tag (`vX.Y.Z`) ships:

| Asset | Purpose |
| --- | --- |
| `Neo_X.Y.Z_aarch64.dmg` | macOS installer (what humans download) |
| `Neo_X.Y.Z_aarch64.app.tar.gz` | In-app auto-update payload |
| `Neo_X.Y.Z_aarch64.app.tar.gz.sig` | Minisign signature for the payload |
| `latest.json` | Update manifest consumed by the Tauri updater |

Current builds target **Apple Silicon only**. Intel builds can be added if there's demand — open an issue upstream.

<br />

## Support & issues

Bug reports, feature requests, and discussion: [erphq/neo/issues](https://github.com/erphq/neo/issues).

---

<div align="center">
<sub>Built with <a href="https://tauri.app">Tauri</a>. Signed by Apple. Distributed by humans who prefer not to dance around Gatekeeper.</sub>
</div>
