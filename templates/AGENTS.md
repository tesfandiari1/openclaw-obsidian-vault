# AGENTS.md — Command & Skill Reference

*Define slash commands your agent responds to. Customize to your workflow.*

## Slash Commands

### /status
Read `_context/status.md` and `_context/tasks.md`. Summarize what's active, blocked, and next.

### /research [topic]
Deep research using available search tools:
1. Parse topic into a specific research question
2. Run multi-source investigation
3. Save structured report to `work/research/<slug>-research.md`
4. Present key findings with sources

### /write [description]
Short-form writing (emails, messages, copy):
1. Identify content type and audience
2. Draft with purpose, stakes, and clear call to action
3. Self-review for clarity and conciseness

### /remember [information]
Save to long-term memory. Confirm what was saved.

### /recall [query]
Search long-term memory. Return matches with context.

### /summarize [URL or text]
Summarize content. For URLs, fetch and extract. For text, distill.

### /help
List all commands with one-line descriptions.

## Skills

*Add your installed skills here. Example:*

- **obsidian-vault-context** — Vault navigation, orchestration files, output routing

## Tools

*List the tools available to your agent. Example:*

- **Web Search** — Tavily, Exa, or similar
- **Memory** — LanceDB or similar persistent memory
- **File tools** — Read/write vault and workspace files

## Vault Access

Full vault at `/path/to/vault/`. Key folders:
- `_context/` — Runtime state
- `work/` — Agent output (drafts, research, output)

Write output to `work/`. Never modify vault files outside `work/` unless asked.
