# 🔬 /skill-deep-dive

> **Source:** Moloco Chief of Staff operating system — exported 2026-May-09
> **Scope:** Generic methodology — adapt tool names, org structure, and personas to your context.
> **Note:** Rubric learning examples reference internal projects by label only (e.g. "Q1 Learnings doc", "CTV Deep Dive") as calibration illustrations. Replace with your own examples as you iterate.

**Purpose:** Execute a deep dive, project status brief, or topic research on any subject. Deep dives are written documents — dense analytical prose, source-attributed, tables only for comparative data — intended for copy-paste into a collaborative doc and sharing with stakeholders.
**Trigger:** Any request for a deep dive, project status, topic brief, or competitive analysis.

---

## Phase 1 / Phase 2 Workflow

Every deep dive follows a two-phase workflow unless you explicitly request a full narrative immediately.

**Phase 1 — Outline:** Build and iterate on a structured outline before writing any prose. Present section headings, key data points to be sourced, and any known data gaps. Do not write narrative prose in Phase 1. Iterate on the outline until you signal readiness for Phase 2.

**Phase 2 — Full Narrative:** Write the complete document in prose and write it to your docs system immediately. Then ask: *"Can you share the final version?"* — so the output can be scored against your edits using the rubric below. Do not skip this step.

**When you detect a deep dive is being initiated (any research, analysis, or structured doc request), immediately ask:**
1. *"How many pages should this be?"*
2. *"Who is the audience?"*
...unless you have already specified both. Do not skip this step.

**At the start of every Phase 2:** Ask for the collaborative doc link that will be used for the final output. The first two lines of every deep dive page must be:
1. **Created / Last updated timestamps** — format: `**Created:** Mon DD, YYYY, H:MM AM/PM TZ | **Last updated:** Mon DD, YYYY, H:MM AM/PM TZ`. Update the "Last updated" timestamp on every subsequent write to that page.
2. **Doc link** — `**Final doc (output):** [url]`

---

## Preconditions
1. Fetch your operating rules page — hard gate. Halt if unreachable.
2. Search your docs system for an existing page matching the topic (semantic similarity, ~70% threshold).
   - **If found:** overwrite the existing page content entirely. Do not create a new page.
   - **If not found:** create a new page with title format `[YYYY-Mon-DD] — [Topic] — [Audience]`.

Memory and project knowledge are starting context only — **never sufficient on their own.** All six steps below are required before outputting any synthesis.

---

## Execution Steps

### Step 1 — Project Knowledge / Memory (Starting Context)
Check project knowledge and memory for existing context on the topic. Treat as orientation only. Never stop here.

### Step 2 — Document Search (Full Corpus Sweep)
Run at minimum two searches with distinct keyword sets: one focused on the project/partner name, one focused on the functional area or workstream. For each result set:
- Read the top 5–8 most relevant documents in full
- Prioritize: dedicated status docs, PRDs, tickets, launch announcements, escalation emails, partner-facing threads
- Flag any document that appears to be a dedicated tracker, requirements sheet, or running log — read those without exception

**Critical data rule:** Never use spend figures, performance data, or key metrics from static documents as current values. All such figures must come from live data queries. If a static document contains figures, treat them as directional only and queue a live validation.

### Step 2b — Document Gate (Decision Point)
After completing the document search, evaluate whether any returned document directly and fully answers the deep dive request.

**If such a document exists:**
- Surface it immediately with its title, URL, last updated date, and a 2–3 sentence summary
- Ask: *"I found [Document Name] (last updated [date]) which appears to directly answer this. Would you like me to surface this document, or should I continue the full deep dive?"*
- Wait for a response before proceeding

**If no such document exists:** proceed directly to Step 3 without asking.

### Step 3 — Dedicated Channels / Forums
Search for channels or threads dedicated to the topic. For any channel found:
- Read the full recent history (last 30 messages minimum)
- Note all open threads, escalations, and unresolved decisions

### Step 4 — Key Contributor Sweep (Last 14 Days)
Identify the 3–5 DRIs. For each:
- Search for recent messages from them on this topic
- Check for recent emails involving them and the topic
- Surface any escalations, blockers, or status updates they've posted

### Step 5 — Email Parsing
Search email for:
- External partner threads
- Internal escalation emails
- Cross-functional asks
- Any thread involving you or other DRIs on this topic

### Step 6 — Live Data Proposal
After synthesizing all qualitative information, identify what quantitative data is missing and propose specific live data queries. For performance/spend topics, always query:
- Metrics at two time-anchored snapshots using trailing averages — never single-day comparisons
- Platform-level trajectory
- Any metric required to support a specific claim in the outline

Output proposed queries as a clearly labeled block, formatted for copy-paste into your data tool. One topic per query. Technical parameters only. No business rationale in the query.

---

## Write-Back (Phase 2 Only)
1. Ask for the collaborative doc link before writing.
2. Write the created/updated timestamps as the very first line.
3. Write the doc link as line 2.
4. Write the full document content.
5. On every subsequent update to the page, update the "Last updated" timestamp.
6. Confirm with the doc URL.
7. Immediately ask: *"Can you share the final version?"* — do not skip this step.

**Title format:** `[YYYY-Mon-DD] — [Topic] — [Audience]`

**Hard rule:** Deep dives always live under the designated parent page for deep dives. Never place under working documents, meeting briefs, or other sections. Zero exceptions.

---

## Output Format — Phase 2 Prose Standards

All Phase 2 deep dives must meet these format requirements:

- **Prose-only body sections — for analytical/strategic deep dives.** No bullet points in narrative sections. Dense paragraphs, 3–5 sentences, declarative voice ("The goal is...", "We will...", "[Company] will..."). Inline **bold** for key terms and emphasis only.

- **SOP / operational procedure exception.** When the deep dive output is an SOP, operating mechanism, or process document (recognized by: multiple numbered steps, distinct triggers, role-ownership per step, escalation paths), use numbered mechanisms with sub-lists and step labels. Prose paragraphs are wrong for this document type. Mechanism headers (bold label + number) are correct. No summary tables. No "Open Questions" section.

- **Tables only for comparative/structured data** — spend tables, roadmap matrices, gap matrices. Never as a substitute for narrative.

- **Domain-specific vocabulary throughout.** Zero generic language ("drive value," "improve efficiency," "enhance experience").

- **Specific metrics in every claim.** No vague directional claims without a number.

- **Source attribution inline** — attribute key claims to the source doc or data query that validates them, embedded in the prose.

- **"What's Missing" section** for any deep dive with unresolved open questions. Flag each gap with: what is known, what is unknown, and the question for the relevant DRI to answer.

- **Audience calibration** — no internal mechanism language when audience is external/sales. Replace with customer-outcome framing.

- **Confidence flags** on directional or unvalidated claims: *(directional — pending data confirmation)* or *(Confidence: Medium — no hard external source)*.

---

## Rubric — Phase 1 Outline Quality (Score /100)

Use this rubric to self-evaluate the outline before presenting it. Target: >99/100. Prose quality and metadata (title format, doc link) are not evaluated in Phase 1 — they are Phase 2 concerns only.

| # | Dimension | Points | Pass Criteria |
|---|-----------|--------|---------------|
| 1 | **Structure fidelity** | 25 | Outline sections and label format match the target doc exactly. No extra sub-headers, no reformatting. Section titles match verbatim. Phase 1 must confirm document architecture before building the outline. If unclear, ask before proceeding. |
| 2 | **Data accuracy** | 25 | All spend/performance figures cited in the outline come from live queries or named live documents. Static doc figures flagged as directional only. Trailing methodology used, not single-day. No unsourced statistics. |
| 3 | **Completeness** | 20 | All key themes surfaced. Data gaps explicitly flagged. Live queries proposed for missing figures. "What's Missing" placeholders identified with named DRIs. Scope of the document explicitly confirmed: which sections are in scope, whether forward-looking sections exist, and what the target length is. |
| 4 | **Source attribution** | 15 | Every material data point in the outline attributed to its source. No unattributed assertions. Claims that cannot be sourced flagged explicitly rather than asserted confidently. |
| 5 | **Audience calibration** | 15 | Framing, vocabulary, and level of detail appropriate to the stated audience. No internal mechanism language for external audiences. |

**Total: 100 points**

---

## Rubric — Phase 2 Output Quality (Score /100)

Use this rubric to self-evaluate every Phase 2 output **before** writing to your docs system. Target: >99/100. Revise before writing if any dimension is below threshold.

When you share a final version after Phase 2, run a full comparison. Identify every category of edit made. Update rubric weights or criteria accordingly.

| # | Dimension | Points | Pass Criteria |
|---|-----------|--------|---------------|
| 1 | **Format fidelity** | 25 | Label structure matches exactly. No sub-headers imposed where the doc uses flat labeled paragraphs. No bullets in narrative sections. No language added that does not appear in the source — editorial enrichment is a format failure. No forward-looking sections unless explicitly in scope. |
| 2 | **Data accuracy** | 20 | All spend/performance figures sourced from live queries or named live documents. No figures from static docs presented as current. Trailing methodology, not single-day. No statistics asserted without a confirmed source. |
| 3 | **Length discipline** | 10 | Each paragraph is as short as possible without losing meaning. Sentences do not repeat each other. Default to cutting rather than adding. |
| 4 | **Source attribution** | 15 | Every material claim attributed inline to its source. No unattributed assertions. Claims that cannot be sourced are flagged or cut. |
| 5 | **Audience calibration** | 15 | Language and framing appropriate to the stated audience. No internal mechanism language for external audiences. |
| 6 | **Structure fidelity** | 5 | All sections match the agreed structure exactly. No extra sections added, no sections reordered. Every table row and column must be present in the source doc — no rows added. |
| 7 | **Sync freshness** | 10 | Before any write to an existing deep dive page, the current final doc is fetched and compared against the stored version. All divergences listed and resolved. |

**Total: 100 points**

---

## Self-Audit Checks

Run universal checks from your operating rules first. Skill-specific checks to be added here as failures are identified in real outputs.

---

## Hard Guardrails
- Never output a Phase 2 narrative before completing all six execution steps
- Steps 3, 4, and 5 are not optional even if Steps 1–2 return rich results
- If a dedicated channel exists, it must be read
- Contributor sweep window: **14 days**
- If an existing deep dive page is found for this topic, overwrite it — never create a new page
- Never use single-day comparisons — always trailing average at two anchored dates
- Always ask for page count and audience at the start of Phase 1, unless already specified
- Always ask for the final doc link before starting Phase 2
- Always ask "Can you share the final version?" after Phase 2 output

---

## Known Failure Modes
- Stopping at Steps 1–2 when document search returns rich results — Steps 3, 4, 5 are always required
- Creating a new deep dive page when an existing one already covers the topic
- Placing the deep dive under the wrong parent in your docs system
- Not surfacing the live data proposal block at the end of Phase 1
- Surfacing data corrections in chat without immediately writing them back to the stored page — any new information that materially changes the doc must be written before the next response
- Using single-day figures instead of trailing averages
- Sourcing performance data from static documents instead of live queries
- Forgetting to ask for the final doc link before Phase 2
- Forgetting to ask "Can you share the final version?" after Phase 2
- Writing bullet points in narrative sections instead of prose paragraphs
- Applying prose-paragraph format to SOP / operational procedure documents — SOPs use numbered mechanisms, sub-lists, and step headers by default
