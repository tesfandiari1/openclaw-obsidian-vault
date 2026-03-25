---
name: obsidian-vault-context
description: |
  Teach an OpenClaw agent to use an Obsidian vault as a shared workspace.
  Covers vault navigation, orchestration file management, output routing,
  and bidirectional collaboration patterns. Use when the agent needs to
  read vault context, write output to the vault, update status/task files,
  or navigate the vault's folder structure. Also triggers on "check status",
  "what's in the vault", "update tasks", "save this to the vault",
  "where should I put this", or any vault-related file operation.
metadata:
  author: tristin
  version: 1.0.0
  requires: obsidian-headless (optional, official Obsidian open beta, for bidirectional sync)
---

# Obsidian Vault Context

Use the Obsidian vault as a live collaboration surface between you and the user. The vault is the shared workspace: you both read and write to it, and changes are visible to both parties in real time.

## Core Principle

The vault is the interface. Not chat. Not a terminal. A shared folder of markdown files that both you and the user can see and edit. Treat it like a shared codebase, not a reference library.

## Vault Navigation

### Reading the Map

On session startup, read the vault's context map (if it exists):
```
_context/context-map.md
```
This file maps active projects to their source files. Use it to know where to look before searching blindly.

If no context map exists, scan the top-level folder structure and infer context from folder names.

### Folder Semantics

Folder placement carries meaning. A file's location tells you what it's about:

| Folder Pattern | Meaning |
|----------------|---------|
| `foundations/` or `core/` | Strategic docs, vision, frameworks |
| `product/` | Product specs, features, sprints, architecture |
| `company/` or `business/` | Operations, legal, marketing, hiring |
| `research/` or `engine/` | Technical research, analysis, data |
| `journal/` | Session logs, daily notes, weekly reviews |
| `decisions/` | Past decisions with rationale |
| `archive/` | Superseded files (read-only reference) |

Respect the structure. When writing new files, place them where they semantically belong.

### Wikilinks

Obsidian uses `[[wikilinks]]` for cross-references. When referencing vault files in your output, use wikilink syntax so the user can click through in Obsidian:
```markdown
See [[foundations/product-vision]] for the full roadmap.
```

## Orchestration Files

These lightweight `.md` files give you persistent state across sessions. They live in a `_context/` folder (or `_Claude/` in some setups).

### Required Files

| File | Purpose | Update Frequency |
|------|---------|-----------------|
| `status.md` | Current state of active workstreams: what's done, what's next, what's blocked | Every session (on changes) |
| `tasks.md` | Prioritized task list with tags | When tasks change |
| `decisions.md` | Living log of decisions with evidence and rationale | When decisions are made |

### Optional Files

| File | Purpose |
|------|---------|
| `context-map.md` | Index mapping projects to vault source files |
| `scratchpad.md` | Working memory for mid-session notes (clear daily) |
| `learnings.md` | Operational knowledge: CLI quirks, SDK behaviors, user preferences |

### Rules

1. **Read before writing.** Always check the current state before updating.
2. **Update immediately.** Don't defer status/task changes to session end. Write them as they happen.
3. **Append, don't overwrite.** For decisions and learnings, add new entries. Don't rewrite history.
4. **Keep it lean.** Orchestration files are signals, not essays. One line per task. One paragraph per decision.

## Output Routing

When producing work (research, drafts, analysis), save it to the vault so the user sees it in Obsidian:

| Output Type | Save To | Format |
|-------------|---------|--------|
| Research briefs | `work/research/` or `research/` | Markdown with sources |
| Article drafts | `work/drafts/` or `drafts/` | Markdown with frontmatter |
| Final deliverables | `work/output/` or `output/` | Polished markdown |
| Quick notes | `_context/scratchpad.md` | Append |

### Frontmatter

Add YAML frontmatter to any file that will be referenced again:
```yaml
---
title: "Research Brief: Competitor Analysis"
type: research
status: draft
created: 2026-03-25
updated: 2026-03-25
tags:
  - research
  - competitors
---
```

## Collaboration Patterns

### The Bidirectional Loop

When Obsidian Headless is configured, changes flow both ways:
- **User edits in Obsidian** (Mac, iPhone, iPad) -> syncs to server -> you read the updated file
- **You write to vault** (research, drafts, status updates) -> syncs to user's Obsidian app

This means:
1. You don't need to present all output in chat. Write it to the vault.
2. The user doesn't need to paste context into chat. You read it from the vault.
3. Both of you can work on the same document asynchronously.

### Passive Visibility

The user sees your work by opening Obsidian, not by asking "what did you do?" Design your output for discoverability:
- Use clear file names (not `output-1.md`, use `competitor-analysis-march-2026.md`)
- Place files in expected folders
- Update `status.md` so the user knows what changed

### Skill and Workflow Editing

If the user edits a skill file (like this one) in Obsidian, pick up the changes on your next session. Skills are living documents, not frozen configs.

## Compaction Safety

When context compaction triggers (the model summarizes older conversation to free up tokens), write critical working state to disk before it's lost:

1. Write current progress to `_context/scratchpad.md`
2. Update `_context/status.md` with any state changes
3. These files + the skill definitions reconstitute the session after compaction

## Getting Started

If this is a fresh vault with no orchestration files:

1. Create `_context/status.md` with the user's current workstreams
2. Create `_context/tasks.md` with their active task list
3. Optionally create `_context/context-map.md` mapping projects to source files
4. Ask the user about their vault structure and update your navigation accordingly

The pattern compounds: each file you add reduces the context tax on every future session.
