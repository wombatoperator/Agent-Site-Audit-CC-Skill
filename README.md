# AI Agent Accessibility Index — Advertising Edition

A structured audit framework for measuring how well advertising industry websites work with AI agents, automated buyers, and LLM-powered workflows.

Built with Vercel's [`agent-browser`](https://github.com/vercel-labs/agent-browser) and Claude Code.

---

## Why This Exists

AI agents are increasingly embedded in how media gets bought, researched, and measured. Agent-based shopping, LLM-powered RFPs, automated platform discovery, and AI-mediated research are no longer hypothetical — they are live workflows at agencies and brands today.

Yet most adtech company websites were not built with agents in mind. They have:
- Heading structures that make document modeling impossible
- Interactive elements agents can't identify or target
- No `/llms.txt` to declare their content to LLMs
- Structured data schemas that are empty stubs

This project audits the advertising ecosystem's websites against a 100-point rubric and publishes the results as a free resource for the community.

**If your platform is invisible to agents, it will be invisible in AI-mediated buying.**

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

## Results To Date

| Site | Category | Score | Grade | llms.txt | JSON-LD | Nav Journey |
|---|---|---|---|---|---|---|
| [vercel.com](./audit-reports/vercel.com-2026-03-01.txt) | Cloud / Infra | 82 | B | ✓ Full | Organization + | 15/15 |
| [thetradedesk.com](./audit-reports/thetradedesk.com-2026-03-01.txt) | DSP | 73 | C | ✗ | None | 15/15 |
| [iab.com](./audit-reports/iab.com-2026-03-01.txt) | Industry Body | 67 | C | ✓ Full | @graph | 12/15 |
| [golivehq.co](./audit-reports/golivehq.co-2026-03-01.txt) | Web Design | 67 | C | ✗ | Stubs | 14/15 |

**Annotated screenshots** and full scored reports are in [`./audit-reports/`](./audit-reports/).

> Notable: The Trade Desk (score 73) and IAB (score 67) both operate at the intersection of data, AI, and advertising — yet neither has completed the basic structured data and LLM-readiness work that would make them machine-readable.

---

## Proposed Audit List

The following 18 sites have been selected to represent a cross-section of the programmatic advertising ecosystem. Priority given to companies where agent-accessibility has direct commercial implications — platforms where AI agents will increasingly be the ones discovering, evaluating, and activating ad technology.

### Demand-Side Platforms (DSPs)
| # | Company | URL | Why Interesting |
|---|---|---|---|
| 1 | Magnite | magnite.com | Largest independent SSP; heavy docs/partner portal |
| 2 | DoubleVerify | doubleverify.com | Verification layer — agents querying brand safety signals |
| 3 | Integral Ad Science | integralads.com | Head-to-head with DV; known for gated research |
| 4 | Criteo | criteo.com | Commerce media pivot; rich product/retail data schema expected |
| 5 | StackAdapt | stackadapt.com | Fast-growing mid-market DSP; strong content marketing |

### Supply-Side Platforms (SSPs)
| # | Company | URL | Why Interesting |
|---|---|---|---|
| 6 | PubMatic | pubmatic.com | Public company; investor-grade structured data expected |
| 7 | Index Exchange | indexexchange.com | Private, opaque — strong test of accessibility-first without public pressure |
| 8 | OpenX | openx.com | Privacy/CTV focus; multiple content types to navigate |

### Identity & Data
| # | Company | URL | Why Interesting |
|---|---|---|---|
| 9 | LiveRamp | liveramp.com | Identity resolution leader; agents will query their partner ecosystem |
| 10 | Lotame | lotame.com | Mid-market data; baseline for independent data cos |
| 11 | Quantcast | quantcast.com | AI-first positioning — do their own docs reflect it? |

### Measurement & Verification
| # | Company | URL | Why Interesting |
|---|---|---|---|
| 12 | Comscore | comscore.com | Currency measurement; buyers query methodology docs |
| 13 | VideoAmp | videoamp.com | Challenger currency; newer site, likely better structured |
| 14 | HUMAN Security | humansecurity.com | Bot management — ironic if their own site is agent-hostile |

### CTV & Streaming
| # | Company | URL | Why Interesting |
|---|---|---|---|
| 15 | FreeWheel | freewheel.tv | Comcast-owned CTV ad server; complex B2B portal |
| 16 | Roku OneView | advertising.roku.com | Consumer brand with B2B adtech layer — navigation test |

### Industry Infrastructure
| # | Company | URL | Why Interesting |
|---|---|---|---|
| 17 | IAB Tech Lab | iabtechlab.com | Standards body for tech specs; agents querying specifications |
| 18 | Amazon Advertising | advertising.amazon.com | Largest retail media network; highest stakes for agent-based buying |

---

## How to Run an Audit

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

```
/agent-browser-audit <url>
```

Example:

```
/agent-browser-audit https://www.magnite.com/
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

Reports and screenshots are saved to `./audit-reports/`.

---

## Skill Files

The audit is powered by a Claude Code skill:

```
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

- Audits are point-in-time snapshots; sites change frequently
- Gated content (login walls) is not assessed beyond the gate
- Dynamic SPAs may require a wait-for-selector step on some sites
- The agent navigation journey is a standard 5-step path — specialist flows (e.g. DSP onboarding, API documentation) are not covered

---

## Background

This project grew out of work on [`adtech-mcp`](https://github.com/madiisa-real/holodeck) — a Model Context Protocol server that gives AI agents structured knowledge of the programmatic advertising ecosystem. In building that database, it became apparent that many of the platforms being profiled were not themselves agent-accessible. The audit framework was designed to measure and publish that gap.

**The hypothesis:** Platforms that invest in agent accessibility will see disproportionate representation in AI-mediated research, recommendations, and buying workflows. The companies that get recommended by agents will be the ones agents can actually read.

---

## License

MIT. Audit reports and screenshots are published under CC BY 4.0.

Data sourced from public-facing websites only. No authenticated sessions, no proprietary data.

---

*Built with [Claude Code](https://claude.ai/claude-code) + Vercel's [agent-browser](https://github.com/vercel-labs/agent-browser)*
