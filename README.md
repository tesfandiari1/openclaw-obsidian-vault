# Obsidian Vault Context Skill

Turn your Obsidian vault into a shared workspace between you and your AI agent. This skill teaches Claude how to navigate, read, write, and collaborate through an Obsidian vault using bidirectional sync via Obsidian Headless.

## What This Does

Most AI agent setups treat your knowledge base as read-only reference. This skill makes it a live collaboration surface:

- **Bidirectional sync** via [Obsidian Headless](https://obsidian.md/help/sync/headless) — you edit in Obsidian (Mac/iPhone/iPad), the agent sees it on the server. The agent writes output, you see it in your vault.
- **Orchestration files** — lightweight `.md` files (`status.md`, `tasks.md`, `decisions.md`) that give the agent persistent state across sessions.
- **Workspace-in-vault** — the agent's identity, skills, and workflows live inside the vault as editable markdown. Change the agent's behavior by editing a note.
- **Structured navigation** — the agent learns your vault's folder structure and uses it for semantic context (a file in `product/sprints/` means something different than one in `company/legal/`).

## Architecture

```
You (Obsidian on Mac / iPhone / iPad)
        ↕ Obsidian Sync (bidirectional)
Server (Claude + vault copy)
        ↕ Cloudflare Tunnel or local access
Telegram / CLI (conversational trigger)
```

The vault is the interface. Not a chat window. Not a terminal. A shared folder of markdown files that both you and the agent can see and edit.

## What's Included

```
clawhub/
  obsidian-vault-context/           # ClawHub-ready skill folder
    SKILL.md                        # Core skill: vault navigation, orchestration, output routing
    references/
      orchestration-files.md        # How to set up status.md, tasks.md, decisions.md
      vault-structure-template.md   # Starter vault layout for agent collaboration
      obsidian-headless-setup.md    # Server sync setup guide (systemd + Obsidian Sync)

templates/
  SOUL.md                           # Agent identity template — vault map, voice, operating principles
  AGENTS.md                         # Slash commands and skill reference template
  _context/                         # Starter orchestration files
    status.md
    tasks.md
    decisions.md
    context-map.md
```

## Prerequisites

- [Obsidian](https://obsidian.md/) with a vault
- [Obsidian Headless](https://obsidian.md/help/sync/headless) for server sync (optional, official open beta, requires Obsidian Sync at $4/month)

## Quick Start

### 1. Install the Skill

**Claude.ai:** Upload the `clawhub/obsidian-vault-context/` folder via Settings > Capabilities > Skills.

**Claude Code:**
```bash
cp -r clawhub/obsidian-vault-context/ ~/.claude/skills/
```

**ClawHub:** Install directly from [clawhub.ai](https://clawhub.ai) (coming soon).

### 2. Set Up Orchestration Files

Copy the templates into your vault:
```bash
cp -r templates/_context/ /path/to/your/vault/_context/
```

Edit `_context/status.md` with your current workstreams. Edit `_context/tasks.md` with your task list. These two files alone eliminate the "let me catch you up" tax from every agent session.

### 3. (Optional) Add Bidirectional Sync

Without sync, the agent can read/write vault files on the server, but you won't see changes in Obsidian automatically.

With [Obsidian Headless](https://obsidian.md/help/sync/headless) (official, open beta) + Obsidian Sync ($4/month):

```bash
npm install -g obsidian-headless
ob login
ob sync-setup --vault "Your Vault" --path ~/.openclaw/vault
ob sync --continuous
```

Run as a systemd service for always-on sync. See `references/obsidian-headless-setup.md` for the full guide.

### 4. Customize the Agent

Copy `templates/SOUL.md` to your workspace and edit it:
- Replace the vault map with your folder structure
- Set the agent's voice and communication style
- Define what the agent helps with

## Works With Obsidian Skills

This skill is designed to pair with [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) — the official collection of Agent Skills for Obsidian file formats by Steph Ango (Obsidian CEO).

**kepano's skills** teach agents the *formats* — how to write valid Obsidian markdown, create `.canvas` files, query `.base` views, and use the Obsidian CLI.

**This skill** teaches agents the *workflow* — how to navigate a vault, maintain persistent state across sessions, route output to the right folders, and collaborate with you through orchestration files.

Together they give an agent full Obsidian fluency:

| Layer | Skill | What It Handles |
|-------|-------|-----------------|
| Format | [obsidian-markdown](https://github.com/kepano/obsidian-skills) | Wikilinks, callouts, embeds, properties |
| Format | [obsidian-bases](https://github.com/kepano/obsidian-skills) | `.base` files — database views, filters, formulas |
| Format | [json-canvas](https://github.com/kepano/obsidian-skills) | `.canvas` files — nodes, edges, visual maps |
| Tool | [obsidian-cli](https://github.com/kepano/obsidian-skills) | Vault CRUD, search, tasks, plugin dev |
| Tool | [defuddle](https://github.com/kepano/obsidian-skills) | Web pages → clean markdown |
| **Workflow** | **obsidian-vault-context** (this repo) | **Vault navigation, orchestration, bidirectional sync** |

Install both for the full stack:
```bash
# Format + tool skills
npx skills add git@github.com:kepano/obsidian-skills.git

# Workflow + orchestration skill (via ClawHub)
# Coming soon
```

## How It Works

### Orchestration Files

Three files handle 90% of cross-session continuity:

| File | Purpose | Agent Reads It |
|------|---------|----------------|
| `status.md` | What's active, blocked, completed | Every session startup |
| `tasks.md` | Prioritized task list with tags | When deciding what to work on |
| `decisions.md` | Past decisions with rationale | Before making similar decisions |

The agent updates these as work progresses. You update them when context changes. Neither of you needs to explain what the other already wrote down.

### The Bidirectional Loop

```
1. You edit status.md in Obsidian during a meeting
2. Obsidian Sync pushes the change to the server
3. You trigger a task via Telegram: "research competitor X"
4. The agent reads the updated status, runs the research pipeline
5. Output lands in work/research/competitor-x.md
6. Obsidian Sync pushes it to your devices
7. You review the brief on your iPad
```

No chat window. No copy-paste. No "let me catch you up." Both parties work on the same files.

### Workspace-in-Vault

The agent's entire operating context lives in the vault as markdown:

- **SOUL.md** — identity, vault map, communication style, operating principles
- **AGENTS.md** — slash commands, skill references, tool inventory
- **skills/** — skill definitions the agent loads on demand
- **workflows/** — multi-step pipelines (research, article creation, etc.)

Edit any of these in Obsidian. The agent picks up the changes on its next session. No redeployment. No code changes.

## Companion Article

For the full story behind this pattern: [The Missing Interface Between You and Your AI Agent](https://medium.com/@tesfandiari/the-missing-interface-between-you-and-your-ai-agent-b56aec9a72cb)

## License

MIT
