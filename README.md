# AI Agent Accessibility Index — Advertising Edition

A structured audit framework for measuring how well advertising industry websites work with AI agents, automated buyers, and LLM-powered workflows.

Built with Vercel's [`agent-browser`](https://github.com/vercel-labs/agent-browser) and Claude Code.

---

## Why This Exists

AI agents are increasingly embedded in how media gets bought, researched, and measured. Agent-based shopping, LLM-powered RFPs, automated platform discovery, and AI-mediated research are no longer hypothetical — they are live workflows at agencies and brands today.

Yet most adtech company websites were not built with agents in mind. They often feature:
- Heading structures that make document modeling impossible
- Interactive elements agents can't identify or target
- No `/llms.txt` to declare their content to LLMs
- Structured data schemas that are empty stubs

This project audits the advertising ecosystem's websites against a 100-point rubric and publishes the results as a free resource for the community.

**If your platform is invisible to agents, it will be invisible in AI-mediated buying.**

---

## Full Industry Report

We recently completed an audit of 19 major domains across the programmatic advertising ecosystem (including DSPs, SSPs, Identity providers, and Measurement platforms).

**Read the complete findings, industry benchmarks, and domain scores here:**  
👉 **[Agentic Advertising Site Audit — March 2026](./AGENTIC-ADVERTISING-AUDIT-2026-03-01.md)**

Individual annotated screenshots and raw scored reports can be found in the [`./audit-reports/`](./audit-reports/) directory.

---

## Scoring Methodology

Each site is scored across 6 dimensions for a total of **100 points**.

| Dimension | Max | What It Measures |
|---|---|---|
| LLM / AI Readiness | 15 | `/llms.txt` presence and quality, `/robots.txt` AI bot policy |
| Accessibility Tree Quality | 25 | Heading hierarchy, landmark regions, ARIA label coverage |
| Interactive Element Coverage | 20 | % of buttons/links/inputs reachable and labeled by ref |
| Structured Data / Schema | 15 | JSON-LD types, Open Graph, meta description |
| Agent Navigation Fluency | 15 | End-to-end journey test: land → navigate → CTA → form → return |
| Bot Resistance / Reliability | 10 | Full content loads headlessly, no CAPTCHA walls |

**Grade scale:**

| Grade | Score | Meaning |
|---|---|---|
| A | 90–100 | Agent-ready. Best-in-class. |
| B | 75–89 | Good. Minor gaps only. |
| C | 55–74 | Functional but agents encounter friction. |
| D | 35–54 | Significant barriers to agent interaction. |
| F | 0–34 | Effectively inaccessible to AI agents. |

Full scoring rubric: [`~/.claude/skills/agent-browser-audit/references/scoring-rubric.md`](./skills/scoring-rubric.md)

---

## How to Run an Audit

You can run this exact audit framework on your own platform or any other domain using the provided Claude Code skill.

### Prerequisites

```bash
# Install agent-browser
npm install -g agent-browser
agent-browser install   # downloads Chromium

# Install Claude Code
npm install -g @anthropic/claude-code
```

### Run

From this directory:

```bash
/agent-browser-audit <url>
```

Example:

```bash
/agent-browser-audit https://www.example.com/
```

The skill will:

1. Open the URL headlessly and wait for full load
2. Check `/llms.txt` and `/robots.txt`
3. Capture the full accessibility tree snapshot
4. Map and count all interactive elements
5. Extract JSON-LD, OG, and meta structured data
6. Run a 5-step agent navigation journey
7. Check bot resistance and content loading
8. Take an annotated screenshot
9. Write a scored report to `./audit-reports/<domain>-<date>.txt`

Reports and screenshots are automatically saved to `./audit-reports/`.

---

## Skill Files

The audit is powered by a Claude Code skill:

```text
~/.claude/skills/agent-browser-audit/
├── SKILL.md                        # 9-step audit protocol
└── references/
    └── scoring-rubric.md           # Full point-by-point rubric
```

This workspace is configured via [`CLAUDE.md`](./CLAUDE.md).

---

## Contributing

Contributions welcome — especially from practitioners who work with these platforms daily.

### Add an audit result

1. Fork this repo
2. Run `/agent-browser-audit <url>` from this directory
3. Commit the `.txt` report and `-annotated.png` screenshot from `./audit-reports/`
4. Open a PR with a one-line summary in the description

### Improve the methodology

The scoring rubric is in `~/.claude/skills/agent-browser-audit/references/scoring-rubric.md`. Proposed changes to scoring weights, new dimensions, or new test steps are welcome as issues or PRs.

### Known limitations

- Audits are point-in-time snapshots; sites change frequently.
- Gated content (login walls) is not assessed beyond the gate.
- Dynamic SPAs may require a wait-for-selector step on some sites.
- The agent navigation journey is a standard 5-step path — specialist flows (e.g., DSP onboarding, API documentation) are not covered.

---

## Background

This project grew out of work on [`adtech-mcp`](https://github.com/madiisa-real/holodeck) — a Model Context Protocol server that gives AI agents structured knowledge of the programmatic advertising ecosystem. In building that database, it became apparent that many of the platforms being profiled were not themselves agent-accessible. The audit framework was designed to measure and publish that gap.

**The hypothesis:** Platforms that invest in agent accessibility will see disproportionate representation in AI-mediated research, recommendations, and buying workflows. The companies that get recommended by agents will be the ones agents can actually read.

---

## License

MIT. Audit reports and screenshots are published under CC BY 4.0.

Data sourced from public-facing websites only. No authenticated sessions, no proprietary data.
