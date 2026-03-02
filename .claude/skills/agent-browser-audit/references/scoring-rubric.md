# Agent Browser Audit — Scoring Rubric

Detailed point allocation for each of the 6 dimensions. Total: 100 points.

---

## 1. LLM / AI Readiness — 15 pts

This dimension checks whether the site has implemented the emerging standards for LLM and AI agent discoverability. `/llms.txt` is the primary signal — it's the one real-world sites actually ship.

### /llms.txt — 12 pts

If the fetch returns HTTP 200 but the response body contains `<!DOCTYPE` or `<html`, treat as NOT FOUND (server catch-all intercepting the path).

| Score | Criteria |
|-------|----------|
| 12    | File exists, follows llms.txt spec: root heading, content sections listed with URLs and descriptions, covers primary site areas (docs, product, blog, etc.) |
| 9     | File exists and spec-compliant but sparse — only a few sections listed, missing major content areas |
| 5     | File exists but minimal — just a title or one-line stub with no linked sections |
| 2     | File exists but is malformed, returns HTML, or content is clearly auto-generated boilerplate |
| 0     | File not found (404 or fetch error) |

### /robots.txt AI Bot Rules — 3 pts

| Score | Criteria |
|-------|----------|
| 3     | robots.txt found AND explicitly allows major AI crawlers (GPTBot, ClaudeBot, CCBot, or `*` with no disallow for AI paths) |
| 2     | robots.txt found, AI bots not explicitly mentioned (neither allowed nor disallowed) — neutral |
| 1     | robots.txt found but disallows some AI bots while allowing others |
| 0     | robots.txt explicitly disallows all AI bots (`Disallow: /` for GPTBot/ClaudeBot/CCBot) OR file not found |

---

## 2. Accessibility Tree Quality — 25 pts

### Heading Hierarchy — 8 pts

| Score | Criteria |
|-------|----------|
| 8     | Exactly one `h1` per page. Logical cascade: h1→h2→h3 with no skipped levels. All headings describe their section content meaningfully |
| 6     | One `h1`, minor hierarchy issues (e.g. occasional h2→h4 skip) but overall logical structure |
| 4     | Multiple `h1`s OR significant level skipping but some structure present |
| 2     | Headings used inconsistently — decorative headings, no logical hierarchy |
| 0     | No headings found, or all content in a single heading level |

### Landmark Regions — 9 pts

| Score | Criteria |
|-------|----------|
| 9     | All four core landmarks present and labeled: `<nav>` (with aria-label distinguishing multiple navs), `<main>`, `<footer>`, and `<aside>` where applicable. Multiple navs all have distinct aria-labels |
| 7     | `main` and `nav` present; footer present; minor labeling gaps |
| 5     | `main` and `nav` present but unlabeled, footer uses `<div>` |
| 3     | Only `main` landmark present; rest are generic divs |
| 0     | No semantic landmark elements — entire page in divs/spans |

### ARIA Label Quality — 8 pts

| Score | Criteria |
|-------|----------|
| 8     | All interactive elements have descriptive, unique accessible names. No generic labels ("button", "link", "click here", "read more"). Icon buttons have aria-label. Images have meaningful alt text |
| 6     | Most elements labeled; ≤ 10% have generic or missing labels |
| 4     | 10–30% of interactive elements have generic or missing labels |
| 2     | 30–50% of interactive elements unlabeled or using generic text |
| 0     | > 50% of interactive elements unlabeled, or systematic use of generic labels |

---

## 3. Interactive Element Coverage — 20 pts

Computed primarily from the **Coverage %** = (labeled elements / total elements) × 100, combined with absolute reachability.

### Coverage Score (15 pts)

| Score | Coverage % |
|-------|-----------|
| 15    | 95–100% — near-perfect labeling |
| 12    | 85–94%  |
| 9     | 70–84%  |
| 6     | 50–69%  |
| 3     | 30–49%  |
| 0     | < 30% — majority of elements unreachable by agents |

### Ref Reachability (5 pts)

| Score | Criteria |
|-------|----------|
| 5     | All refs returned by snapshot are stable and actionable: `click`, `type`, `find` all work correctly with snapshot refs |
| 3     | Most refs work; occasional ref mismatch or stale refs after navigation |
| 1     | Refs frequently fail or require guessing; dynamic ref IDs change on page load |
| 0     | Snapshot refs non-functional — agent cannot interact with elements despite them appearing in tree |

---

## 4. Structured Data / Schema — 15 pts

### JSON-LD Schema Markup — 8 pts

| Score | Criteria |
|-------|----------|
| 8     | Rich, accurate schema: `Organization` or `WebSite` + at least one content-type schema (`Product`, `Article`, `FAQPage`, `LocalBusiness`, etc.). `@id` and `@context` correct. No markup errors |
| 6     | Basic schema present (e.g. `Organization` only) — accurate but limited scope |
| 4     | Schema present but with missing required properties or minor errors |
| 2     | Schema markup exists but is malformed (JSON parse errors) or completely inaccurate |
| 0     | No JSON-LD found |

### Open Graph Tags — 4 pts

| Score | Criteria |
|-------|----------|
| 4     | All four core OG tags present and populated: `og:title`, `og:description`, `og:image`, `og:type` |
| 3     | Three of four present |
| 2     | Two of four present |
| 1     | One OG tag present |
| 0     | No OG tags |

### Meta Description — 3 pts

| Score | Criteria |
|-------|----------|
| 3     | Present, 50–160 characters, accurately describes page content |
| 2     | Present but too short (< 50 chars) or too long (> 160 chars) |
| 1     | Present but generic/templated (e.g. "Welcome to our website") |
| 0     | Missing |

---

## 5. Agent Navigation Fluency — 15 pts

Each of the 5 journey steps in Step 6 is worth 3 points.

### Per Step Scoring — 3 pts each

| Score | Criteria |
|-------|----------|
| 3     | Step completed successfully: correct ref found, action executed, expected outcome observed |
| 2     | Step completed with minor issues: needed multiple attempts, or partial success (e.g. navigated but new page had no `h1`) |
| 1     | Step partially failed: agent reached element but interaction produced unexpected result |
| 0     | Step failed completely: ref not found, action threw error, or page state unpredictable after action |

### Journey Steps
1. Land + Orient (read h1 + nav)
2. Navigate to primary content section
3. Locate and interact with primary CTA
4. Form interaction (score 3/3 if no form is present — N/A is not a penalty)
5. Return to homepage

---

## 6. Bot Resistance / Reliability — 10 pts

### Content Availability Headlessly — 5 pts

| Score | Criteria |
|-------|----------|
| 5     | Full page content loads without human interaction. Word count > 300. No blank/skeleton screen. All nav and main content visible |
| 3     | Most content loads; some sections deferred but primary content available |
| 1     | Significant content hidden behind JS lazy-load that doesn't auto-trigger; page loads thin skeleton |
| 0     | Page is blank, shows anti-bot message, or requires scroll/hover events to load any meaningful content |

### CAPTCHA / Anti-Bot Walls — 3 pts

| Score | Criteria |
|-------|----------|
| 3     | No CAPTCHA, no Cloudflare challenge, no "verify you are human" page — full access granted |
| 1     | Soft challenge detected (e.g. JS challenge that agent-browser resolves) but audit can proceed |
| 0     | Hard CAPTCHA wall encountered — agent cannot proceed without human intervention |

### JS Execution Reliability — 2 pts

| Score | Criteria |
|-------|----------|
| 2     | `document.title` returns expected title. `eval` calls work consistently. No console errors blocking execution |
| 1     | Minor JS errors present but audit can proceed |
| 0     | JS execution fails or returns null/undefined for basic checks — page likely server-rendered with no JS or has CSP blocking eval |

---

## Grade Thresholds Summary

| Grade | Score Range | Interpretation |
|-------|-------------|----------------|
| A     | 90–100      | Agent-ready. Best-in-class site for automated interaction. Publish as case study. |
| B     | 75–89       | Good. Agents can navigate effectively. Minor improvements recommended. |
| C     | 55–74       | Functional but agents will encounter friction on key flows. Medium-priority fixes. |
| D     | 35–54       | Significant barriers. Agent success rate will be low. Immediate attention needed. |
| F     | 0–34        | Effectively inaccessible to AI agents. Fundamental rework required. |
