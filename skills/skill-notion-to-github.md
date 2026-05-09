---
name: notion-to-github
description: Publishes Notion skills from the Chief of Staff operating system to the jl-chief-of-staff GitHub repository. Use this skill whenever the user asks to "push skills to GitHub", "sync notion skills to the repo", "publish skills", "export skills to GitHub", or "share skills with the team". The skill fetches every subpage from the Notion operating rules index, evaluates each for confidential content, pushes only generic reusable skills to GitHub as markdown files, and produces a full audit report with per-skill decisions, sanity checks, and a final GitHub cleanliness verification.
---

# Notion Skills → GitHub Publisher

**Purpose:** Export generic, reusable Chief of Staff skills from Notion to the `moloco/jl-chief-of-staff` GitHub repository for wider adoption. The skill acts as a gatekeeper: it reads every Notion skill page, makes an explicit push/skip decision for each with documented reasoning, and never pushes confidential or Moloco-specific operational content.

---

## Pre-Flight Checklist

Before starting, verify two things:

**1. Notion access.** Fetch the Operating Rules page (`319cdb351336810692a3c98c5f1099bb`). If unreachable, halt and flag — do not proceed with a cached or assumed list of skills.

**2. GitHub authentication.** Check whether `GITHUB_TOKEN` is set in the environment (`echo $GITHUB_TOKEN`). If absent, check for a `gh` CLI session (`gh auth status`). If neither is available, halt and tell the user: "No GitHub credentials found. Please set `GITHUB_TOKEN` as an environment variable (a Personal Access Token with `repo` scope) and retry."

---

## Step 1 — Build the Skill Inventory

From the Operating Rules page, extract every subpage listed under the Skill Directory table. This is the complete population for evaluation — do not add or omit pages. For each entry, record:

- **Skill name** (from the table Command column, e.g., `skill-weekly-brief`)
- **Notion URL** (the linked subpage URL)
- **Fetch status** (success / unreachable)

Fetch all subpages **in parallel** (multiple `notion-fetch` calls in the same turn). This is mandatory for speed — do not fetch sequentially.

Do not skip pages that fail to fetch — mark them as `FETCH_ERROR` in the report and continue.

---

## Step 2 — Sensitivity Evaluation

For each successfully fetched skill page, run the following two-gate evaluation. Both gates must pass for a skill to be pushed.

### Gate 1 — Hard Sensitivity Blockers (any match = SKIP)

Scan the full page content for any of the following. A single match on any item triggers a skip with the specific matched term cited in the report.

| Signal | Description |
|--------|-------------|
| `Helios` | M&A project code name — company-confidential |
| `Arrow` in context of `project-arrow`, `Project Arrow`, `#project-arrow` | Deal code name — company-confidential |
| `IPO`, `S-1`, `pre-IPO`, `going public` | Non-public material financial events |
| `#private-investor-analysis` | Investor/IPO Slack channel |
| Named employee + behavioral/performance language | Any passage describing a specific named individual's communication style, preferences, landmines, performance rating, evaluation, or calibration data. Pattern: person name followed within 50 words by terms like "prefers", "style", "landmine", "doesn't like", "communication", "avoid with", "strengths", "weaknesses", "calibration", "promotion", "rating", "PIP", "below expectations" |
| Customer name + spend or revenue figures | Named external client (advertiser, publisher, partner) paired with specific dollar figures, DRR, GAS, or SOV numbers that are not described as hypothetical examples |
| Employee compensation or equity data | Any mention of salary, bonus, equity, strike price, RSU |
| Legal proceeding or regulatory filing reference | Active litigation, regulatory inquiry, or draft filings |
| `#legal-` channel references tied to active matters | Slack channels with names suggesting active legal work |

### Gate 2 — Generic Reusability Check (must pass to push)

A skill passes Gate 2 only if a reasonable product or AI leader at a **different company** could pick it up and use it without needing Moloco-specific context. Ask:

1. **Does the skill's core value depend on internal tools?** If the skill is 80%+ about operating Searchlight, Glean, or other Moloco-proprietary systems — and those tool-specific steps can't be removed without destroying the skill — mark as `SKIP: INTERNAL-TOOL-DEPENDENCY`.

2. **Does the skill's core value depend on specific internal org structure?** Skills whose primary output is a brief calibrated to Moloco's specific teams, direct reports by name, recurring meetings (WBR, Tal's Staff, OS Standup, Core LT), or Moloco-specific cadences fail this gate. Mark as `SKIP: ORG-SPECIFIC`.

3. **Is the skill's methodology abstractly reusable?** Writing frameworks, document structure rules, research protocols, process templates — these pass Gate 2 even if they contain incidental Moloco vocabulary, as long as the vocabulary is illustrative rather than operational. When in doubt, lean toward pushing with a note rather than skipping.

**Decision rubric:**

| Gate 1 | Gate 2 | Decision |
|--------|--------|----------|
| Pass (no blockers) | Pass | `PUSH` |
| Pass (no blockers) | Fail | `SKIP` with Gate 2 reason |
| Fail | Any | `SKIP` with Gate 1 matched terms |

---

## Step 3 — Prepare Markdown for Pushed Skills

For each `PUSH` decision, format the skill content for GitHub:

1. **Strip Notion metadata.** Remove `<page>`, `<ancestor-path>`, `<properties>` XML wrappers. Keep only the clean content.

2. **Add a standard header block** (after the emoji-prefixed skill name as H1):
   ```markdown
   > **Source:** Moloco Chief of Staff operating system — exported [YYYY-Mon-DD]
   > **Scope:** Generic methodology — adapt tool names, org structure, and personas to your context.
   ```

3. **Preserve all content faithfully.** Do not rewrite, improve, or editorialize the skill content. The goal is accurate export, not curation.

4. **Filename:** Use the skill's command name, lowercase, with hyphens, `.md` extension. Examples:
   - `skill-deep-dive.md`
   - `skill-1pager-decision-doc.md`
   - `skill-slack-memo.md`

5. **Target directory in repo:** `skills/` (create if it doesn't exist in the repo — the GitHub API will create it implicitly when the first file is pushed).

---

## Step 4 — Push to GitHub via API

Push each approved skill to `moloco/jl-chief-of-staff` using the GitHub Contents API. Use Python with `urllib` (no third-party libraries needed):

```python
import urllib.request
import urllib.error
import json
import base64
import os

GITHUB_TOKEN = os.environ.get('GITHUB_TOKEN', '')
REPO = 'moloco/jl-chief-of-staff'
BRANCH = 'main'  # adjust if default branch differs

def push_skill_to_github(filename, content_str):
    """Push or update a skill file in the GitHub repo."""
    path = f"skills/{filename}"
    url = f"https://api.github.com/repos/{REPO}/contents/{path}"
    
    headers = {
        'Authorization': f'token {GITHUB_TOKEN}',
        'Content-Type': 'application/json',
        'Accept': 'application/vnd.github.v3+json',
        'User-Agent': 'moloco-chief-of-staff-skill-publisher'
    }
    
    encoded_content = base64.b64encode(content_str.encode('utf-8')).decode('utf-8')
    
    # Check if file already exists (to get its SHA for update)
    sha = None
    try:
        req = urllib.request.Request(url, headers=headers)
        with urllib.request.urlopen(req) as resp:
            existing = json.loads(resp.read())
            sha = existing.get('sha')
    except urllib.error.HTTPError as e:
        if e.code != 404:
            raise
    
    # Create or update
    payload = {
        'message': f'Export {filename} from Notion Chief of Staff skills',
        'content': encoded_content,
        'branch': BRANCH
    }
    if sha:
        payload['sha'] = sha
    
    data = json.dumps(payload).encode('utf-8')
    req = urllib.request.Request(url, data=data, headers=headers, method='PUT')
    
    try:
        with urllib.request.urlopen(req) as resp:
            result = json.loads(resp.read())
            return {
                'status': 'updated' if sha else 'created',
                'url': result['content']['html_url'],
                'sha': result['content']['sha']
            }
    except urllib.error.HTTPError as e:
        error_body = e.read().decode('utf-8')
        return {'status': 'error', 'error': f"HTTP {e.code}: {error_body}"}
```

Run this for each approved skill. Capture the returned URL and SHA for the report.

**If the default branch is not `main`**, first check with:
```
GET https://api.github.com/repos/moloco/jl-chief-of-staff
```
and read the `default_branch` field.

---

## Step 5 — Update README.md

After all skill files are pushed, update `README.md` in the repo root to reflect the current state of the `skills/` directory. This step is mandatory on every run — whether skills were added, updated, or unchanged.

**5.1 — Fetch the current skills directory listing.** Call:
```
GET https://api.github.com/repos/moloco/jl-chief-of-staff/contents/skills
```
This returns the authoritative list of files currently in `skills/`. Use this — not the push list — as the source of truth for the README table. It captures files pushed in previous runs that are not part of the current batch.

**5.2 — Fetch the current README.** Call:
```
GET https://api.github.com/repos/moloco/jl-chief-of-staff/contents/README.md
```
Record the SHA (required for the update PUT). Decode the base64 content.

**5.3 — Rebuild the skills table.** For each `.md` file in `skills/`, produce one table row:

```markdown
| [`{filename}`](skills/{filename}) | {trigger phrase} | {one-sentence purpose} |
```

- **Trigger phrase:** Extract from the skill file's `**Trigger**` or `**Trigger (manual):**` line. Use the shortest representative phrase (e.g., `"EOD wrap" / "Wrap up today"`).
- **Purpose:** Extract from the skill file's `**Purpose:**` line. Truncate to one sentence if longer.
- **Order:** Alphabetical by filename.

**5.4 — Write back the updated README.** Use the same `push_skill_to_github`-style PUT call against `README.md` (not `skills/README.md`). Use the SHA from step 5.2. Commit message: `Update README.md — sync skills table [YYYY-Mon-DD]`.

**5.5 — Confirm.** Record the returned GitHub URL for README.md in the audit report.

---

## Step 6 — Generate the Audit Report

After all push/skip decisions are made and all GitHub pushes complete, output this report in the conversation. This is the primary deliverable — format it clearly.

```
## Notion → GitHub Skills Audit
Run date: [YYYY-Mon-DD HH:MM PT]
Repo: github.com/moloco/jl-chief-of-staff / skills/

### Inventory Summary
Total skills evaluated: [N]
Pushed to GitHub:       [N]
Skipped:               [N]
Fetch errors:          [N]

### Per-Skill Results

| # | Skill Name | Notion URL | Decision | Location (GitHub) | Notes |
|---|------------|------------|----------|-------------------|-------|
| 1 | skill-deep-dive | [url] | PUSH | skills/skill-deep-dive.md ([link]) | — |
| 2 | skill-meeting-1x1 | [url] | SKIP: ORG-SPECIFIC | — | Named employee behavioral profiles (Aparna, Diederik, etc.) |
| 3 | skill-weekly-brief | [url] | SKIP: ORG-SPECIFIC | — | Moloco-specific org cadence, WBR/LT structure |
| ... | ... | ... | ... | ... | ... |

### Skip Reasons (Detail)
For each skipped skill, one paragraph: what was found, which gate it failed, and why specifically.

### Sanity Checks
- [ ] Count of md files in GitHub `skills/` directory matches pushed count
- [ ] Each pushed file is fetchable via GitHub API (check SHA)
- [ ] README.md updated and confirmed (Step 5)
- [ ] No pushed file contains any Gate 1 blocker terms (second-pass scan — see Step 7)
```

---

## Step 7 — Final GitHub Cleanliness Verification

After the report is generated, perform a second-pass safety check on everything that was pushed:

1. **Re-fetch each pushed file** from GitHub using the Contents API (`GET /repos/moloco/jl-chief-of-staff/contents/skills/{filename}`) and decode the base64 content.

2. **Run the Gate 1 blocker scan** on the decoded content — same exact terms as Step 2. This is a machine-checkable confirmation, not a re-evaluation.

3. **Report outcome:**
   - `✅ CLEAN` — no Gate 1 terms found in any pushed file
   - `⚠️ FLAG` — term found in a pushed file that was missed in Step 2. If this occurs: immediately call the GitHub DELETE endpoint to remove the file, add the skill to the SKIP list in the report with reason `GATE-1-MISS: [term]`, and alert the user explicitly.

Output the verification result at the bottom of the audit report.

---

## Hard Guardrails

- **Never push without explicit Gate 1 and Gate 2 evaluation for every skill.** Batch-approving or approving without reading the content is a hard failure.
- **Never push to a branch other than the repo's default branch** unless JL explicitly requests otherwise.
- **Never modify skill content during export.** The markdown pushed must faithfully represent the Notion content. Adding, removing, or improving content — even to make it "more generic" — is not permitted. The standard header block (Step 3.2) is the only addition allowed.
- **Always update README.md after every push run** (Step 5). Completing a push without updating the README is a hard failure — even if no new skills were added (existing skills may have been updated).
- **Never skip the final GitHub cleanliness check** (Step 7). This is mandatory even if Gate 1 found no blockers.
- **If GITHUB_TOKEN is missing or the API returns 401/403**, halt immediately and surface the credential error to JL. Do not attempt workarounds.
- **This skill must never push itself** — if a skill named `notion-to-github` or equivalent is found in the Notion index, skip it automatically.

---

## Known Failure Modes

- Fetching the Notion index but missing subpages that appear as inline blocks rather than listed pages — always check the full `<content>` for `<page url=...>` tags, not just the table rows.
- Approving a skill that passes Gate 1 word-scan but contains behavioral employee profiles described with neutral language ("meeting cadence", "preferred format", "working style") — read the full context, not just keyword hits.
- Pushing to GitHub but failing to verify the SHA in the response — always confirm the push succeeded before marking `PUSH` in the report.
- Not checking for an existing file before PUT — always GET first to retrieve SHA, otherwise the update will fail with a 409 conflict.
- Treating the `#project-arrow-core` Slack channel ID as merely a channel reference — this is a sensitive project code name and triggers Gate 1 regardless of context.
- Missing skills that appear as child pages of the operating rules page but are not listed in the Skill Directory table (e.g., `SoX Reference Guide`, `DSP Launches Tracker`, `Skill: Slack-Memo` duplicate) — evaluate all child pages, not just table-listed ones.
- Rebuilding the README skills table from the push list rather than the live `skills/` directory listing — always GET `skills/` from the API after pushing to get the authoritative file list, which includes skills from prior runs.
- Updating README.md with an outdated trigger or purpose extracted from a cached version — always read the skill file content directly from GitHub (GET `skills/{filename}`) when building each table row.
