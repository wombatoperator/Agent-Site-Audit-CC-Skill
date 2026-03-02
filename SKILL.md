---
name: agent-browser-audit
description: Audit a website for AI agent accessibility. Use when asked to audit, score, or test how well a site works for AI agents, automated browsing, or agent-based advertising interactions.
argument-hint: <url>
---

You are orchestrating an AI Agent Accessibility Audit for: **$ARGUMENTS**

Spawn a sub-agent using the Agent tool with these exact parameters:
- `subagent_type`: `"general-purpose"`
- `model`: `"haiku"`
- `description`: `"Audit $ARGUMENTS for AI agent accessibility"`
- `prompt`: the full audit instructions below (substitute the actual URL for $ARGUMENTS)

The sub-agent will execute all browser commands and write the final report. Wait for it to complete, then confirm the results to the user.

---

**PROMPT TO PASS TO THE SUB-AGENT (substitute actual URL):**

You are conducting a structured AI Agent Accessibility Audit using `npx agent-browser` (v0.15.1). The target URL is: **$ARGUMENTS**

Complete all 9 steps below in order. Do not skip steps. Record all findings as you go — they feed the final report.

---

## Step 1 — Setup

1. Accept `$ARGUMENTS` as the target URL.
2. Derive `<domain>` by stripping the protocol and trailing slash (e.g. `example.com`).
3. Generate a unique session name to avoid daemon collisions when multiple audits run in parallel:
   ```bash
   SESSION=$(echo "<domain>-$(date +%s)") && echo "SESSION=$SESSION"
   ```
   **Record this SESSION value. Use it for every `--session-name` flag for the rest of this audit — never use the bare domain.**
4. Open the page and wait for full load:
   ```bash
   npx agent-browser --session-name $SESSION open $ARGUMENTS && npx agent-browser --session-name $SESSION wait --load networkidle
   ```
5. Ensure the output directory exists:
   ```bash
   mkdir -p ./audit-reports
   ```

---

## Step 2 — Proactive AI File Check

Use `npx agent-browser --session-name <domain> eval` to fetch each file from the site origin and record: **found / not found** plus a brief content summary if found.

```bash
npx agent-browser --session-name <domain> eval "fetch('/llms.txt').then(r=>r.ok?r.text():Promise.reject(r.status)).catch(e=>'NOT FOUND: '+e)"


**Important:** If the `/llms.txt` fetch returns a 200 but the content is HTML (contains `<!DOCTYPE` or `<html`), treat it as NOT FOUND — the server's catch-all router is intercepting the path.

For `robots.txt`, scan the content for these user-agents and their allow/disallow rules:
- `GPTBot`, `ClaudeBot`, `CCBot`, `anthropic-ai`, `PerplexityBot`, `Googlebot-Extended`

Record per file:
- **llms.txt**: found/not-found, list of content sections declared, whether it follows the llms.txt spec

---

Capture and analyze the accessibility tree:

```bash

npx agent-browser --session-name <domain> snapshot


npx agent-browser --session-name <domain> snapshot -i -c
```

Analyze and record:
- **Heading hierarchy**: Is there exactly one `h1`? Do `h2`s follow `h1`? No skipped levels (h1→h3)?
- **Landmark regions**: Are `nav`, `main`, `footer`, `aside` present and labeled?
- **ARIA labels**: Do all interactive elements have descriptive labels (not just "button", "link")?
- **Missing roles**: Any interactive-looking elements without proper roles?
- **Total element count**: How many nodes in the accessibility tree?

---

## Step 4 — Interactive Elements Audit

Using the refs from the `-i -c` snapshot in Step 3:

```bash
npx agent-browser --session-name <domain> find role button text

npx agent-browser --session-name <domain> find role link text
```

For each interactive element type, record:
- **Total count** (buttons, links, inputs, selects, textareas)
- **Labeled count** — has a non-generic accessible name
- **Unlabeled count** — missing label, aria-label, or uses generic text like "click here", "read more"
- **Ref reachability** — do the refs returned in snapshot match what `find` returns?

Compute: **Interactive Element Coverage %** = (labeled / total) × 100

---

## Step 5 — Structured Data Check

```bash
npx agent-browser --session-name <domain> eval "JSON.stringify([...document.querySelectorAll('script[type=\"application/ld+json\"]')].map(s=>{try{return JSON.parse(s.textContent)}catch(e){return 'PARSE_ERROR'}}),null,2)"

npx agent-browser --session-name <domain> eval "JSON.stringify(Object.fromEntries([...document.querySelectorAll('meta[property^=\"og:\"]')].map(m=>[m.getAttribute('property'),m.content])),null,2)"

npx agent-browser --session-name <domain> eval "document.querySelector('meta[name=\"description\"]')?.content ?? 'NOT FOUND'"

npx agent-browser --session-name <domain> eval "JSON.stringify(Object.fromEntries([...document.querySelectorAll('meta[name^=\"twitter:\"]')].map(m=>[m.getAttribute('name'),m.content])),null,2)"
```

Record:
- **JSON-LD schema types** found (e.g. `Organization`, `Product`, `Article`, `BreadcrumbList`, `FAQPage`, `WebSite`, `LocalBusiness`)
- **OG tags**: og:title, og:description, og:image, og:type — present/missing
- **Meta description**: present/missing, character count
- **Twitter cards**: present/missing

---

## Step 6 — Agent Navigation Journey

Simulate a realistic agent navigation journey using refs from the Step 3 snapshot. Log each step with: ref used, action, result (success/fail/error).

Perform these steps in sequence:

1. **Land + orient**: Read the page `h1` text and primary `nav` link labels
   ```bash
   npx agent-browser --session-name <domain> eval "document.querySelector('h1')?.innerText"
   ```

2. **Navigate to primary content**: Click the most prominent non-logo nav link (use ref from snapshot)
   ```bash
   npx agent-browser --session-name <domain> click <ref>
   npx agent-browser --session-name <domain> wait --load networkidle
   npx agent-browser --session-name <domain> eval "document.querySelector('h1')?.innerText"
   ```

3. **Locate primary CTA**: Find and interact with the main call-to-action button
   ```bash
   npx agent-browser --session-name <domain> find role button text
   npx agent-browser --session-name <domain> click <cta-ref>
   ```

4. **Attempt form interaction** (if a search box or contact form is present):
   ```bash
   npx agent-browser --session-name <domain> type <input-ref> "test query"
   ```

5. **Navigate back** to homepage:
   ```bash
   npx agent-browser --session-name <domain> eval "history.back()"
   npx agent-browser --session-name <domain> wait --load networkidle
   ```

Record for each step: what was attempted, ref used, outcome, any error messages from agent-browser.

---

## Step 7 — Bot Resistance Check

```bash
npx agent-browser --session-name <domain> eval "document.title"

npx agent-browser --session-name <domain> eval "document.body.innerText.trim().length"

npx agent-browser --session-name <domain> eval "document.body.innerText.trim().split(/\s+/).length"

npx agent-browser --session-name <domain> find text "captcha"
npx agent-browser --session-name <domain> find text "verify you are human"
npx agent-browser --session-name <domain> find text "are you a robot"

npx agent-browser --session-name <domain> eval "document.querySelectorAll('[data-lazy],[loading=\"lazy\"]').length"
```

Record:
- Page title retrieved: yes/no
- Body text length (characters)
- Word count
- CAPTCHA/anti-bot elements found: yes/no (list any refs)
- Lazy-load elements that may block agent access
- Overall assessment: **Fully accessible headlessly** / **Partially accessible** / **Blocked**

---

## Step 8 — Annotated Screenshot

```bash
npx agent-browser --session-name <domain> screenshot --annotate ./audit-reports/<domain>-annotated.png
```

Confirm the file was saved. This image is included in the client deliverable.

---

## Step 9 — Score + Report

### Scoring

Compute a score out of 100 using the rubric below. See `references/scoring-rubric.md` for detailed criteria.

| Dimension                     | Max Pts | Your Score |
|-------------------------------|---------|------------|
| LLM / AI Readiness            | 15      |            |
| Accessibility Tree Quality    | 25      |            |
| Interactive Element Coverage  | 20      |            |
| Structured Data / Schema      | 15      |            |
| Agent Navigation Fluency      | 15      |            |
| Bot Resistance / Reliability  | 10      |            |
| **TOTAL**                     | **100** |            |

**Grade scale:**
- **A** — 90–100: Agent-ready. Best-in-class.
- **B** — 75–89: Good. Minor gaps only.
- **C** — 55–74: Functional but agents will struggle with key flows.
- **D** — 35–54: Significant barriers to agent interaction.
- **F** — 0–34: Effectively inaccessible to AI agents.

---

### Report

Write the final report to: `./audit-reports/<domain>-<YYYY-MM-DD>.txt`

Use today's date in `YYYY-MM-DD` format. Use box-drawing characters for tables and headers (reference the `financial-audit` skill style).

The report must contain these 10 sections:

```
╔══════════════════════════════════════════════════════════════╗
║         AI AGENT ACCESSIBILITY AUDIT REPORT                  ║
║  Domain : <domain>                                           ║
║  Date   : <YYYY-MM-DD>                                       ║
║  Tool   : agent-browser v0.15.1                              ║
╚══════════════════════════════════════════════════════════════╝

1. EXECUTIVE SUMMARY
   Grade: <X>  |  Score: <N>/100
   [3-line narrative: overall verdict, strongest area, biggest gap]

2. LLM / AI READINESS                                  [X/15 pts]
   ┌─────────────────┬────────┬──────────────────────────────┐
   │ File            │ Status │ Summary                      │
   ├─────────────────┼────────┼──────────────────────────────┤
   │ /llms.txt       │        │                              │
   │ /robots.txt     │        │ AI bots: allowed/blocked/... │
   └─────────────────┴────────┴──────────────────────────────┘

3. ACCESSIBILITY TREE ANALYSIS                         [X/25 pts]
   Heading hierarchy  : [findings]
   Landmark regions   : [findings]
   ARIA label quality : [findings]
   Total tree nodes   : [N]

4. INTERACTIVE ELEMENT COVERAGE                        [X/20 pts]
   ┌──────────────┬───────┬─────────┬───────────┐
   │ Element Type │ Total │ Labeled │ Coverage  │
   ├──────────────┼───────┼─────────┼───────────┤
   │ Buttons      │       │         │           │
   │ Links        │       │         │           │
   │ Inputs       │       │         │           │
   └──────────────┴───────┴─────────┴───────────┘

5. AGENT NAVIGATION JOURNEY LOG                        [X/15 pts]
   Step 1 — Land + Orient      : [result]
   Step 2 — Navigate Content   : [result]
   Step 3 — CTA Interaction    : [result]
   Step 4 — Form Interaction   : [result / N/A]
   Step 5 — Return to Home     : [result]

6. STRUCTURED DATA FINDINGS                            [X/15 pts]
   JSON-LD schemas : [list types or NONE]
   OG tags         : [present/partial/missing]
   Meta description: [present (N chars) / missing]
   Twitter cards   : [present/missing]

7. BOT RESISTANCE ASSESSMENT                           [X/10 pts]
   Page title retrieved : [yes/no]
   Body text length     : [N chars / N words]
   CAPTCHA detected     : [yes/no]
   Lazy-load barriers   : [N elements]
   Verdict              : [Fully accessible / Partial / Blocked]

8. TOP 3 CRITICAL ISSUES
   ① [Most impactful issue — specific, actionable]
   ② [Second issue]
   ③ [Third issue]

9. RECOMMENDATIONS
   QUICK WINS (implement in < 1 week):
   • [Recommendation 1]
   • [Recommendation 2]

   STRATEGIC (longer-term improvements):
   • [Recommendation 1]
   • [Recommendation 2]

10. CLOSING NOTES
    [Any caveats, pages not tested, dynamic content limitations, etc.]

════════════════════════════════════════════════════════════════
   Report generated by Claude Code · agent-browser-audit skill
════════════════════════════════════════════════════════════════
```

After writing the report, confirm to the user:
- Report path: `./audit-reports/<domain>-<YYYY-MM-DD>.txt`
- Screenshot path: `./audit-reports/<domain>-annotated.png`
- Final grade and score
- Top 3 critical issues (brief)
