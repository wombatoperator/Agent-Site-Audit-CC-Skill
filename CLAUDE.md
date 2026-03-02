# AI Agent Accessibility Audit Workspace

This is the working directory for AI agent accessibility audits using `agent-browser`.

## Tool

`npx agent-browser` (v0.15.1) — headless browser with an accessibility-tree-first API designed for agent automation.

Key commands:
- `npx agent-browser --session-name <domain> open <url>` — open a URL in a named session
- `npx agent-browser --session-name <domain> wait --load networkidle` — wait for full page load
- `npx agent-browser --session-name <domain> snapshot` — full accessibility tree
- `npx agent-browser --session-name <domain> snapshot -i -c` — interactive elements only, compact
- `npx agent-browser --session-name <domain> find role <role> text` — find elements by role
- `npx agent-browser --session-name <domain> click <ref>` — click an element by ref
- `npx agent-browser --session-name <domain> type <ref> <text>` — type into an input
- `npx agent-browser --session-name <domain> eval "<js>"` — execute JavaScript in page context
- `npx agent-browser --session-name <domain> screenshot --annotate <path>` — annotated screenshot

## Output

All audit reports and screenshots are saved to `./audit-reports/`:
- `./audit-reports/<domain>-<YYYY-MM-DD>.txt` — scored audit report
- `./audit-reports/<domain>-annotated.png` — annotated screenshot

## Usage

To run a full audit:
```
/agent-browser-audit <url>
```

Example:
```
/agent-browser-audit https://example.com
```

## Skill Location

`~/.claude/skills/agent-browser-audit/SKILL.md`

Scoring rubric: `~/.claude/skills/agent-browser-audit/references/scoring-rubric.md`
