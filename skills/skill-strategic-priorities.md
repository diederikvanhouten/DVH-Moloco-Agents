# 🎯 /skill-strategic-priorities

> **Source:** Moloco Chief of Staff operating system — exported 2026-May-11
> **Scope:** Org-specific — references Notion Strategic Priorities DB and sources.md in this repo.

**Purpose:** Add, update, or audit entries in the Strategic Priorities DB, including mandatory source registration.
**Trigger:** "Add a strategic priority" / "Update [priority name]" / "Audit strategic priorities" / "What priorities are we missing?"

---

## Preconditions

1. Read `sources.md` from this repo — this is the canonical source registry. Do not proceed without it.
2. Fetch the Strategic Priorities DB to get current entries and schema before any write.
   - DB ID: `f551ee8631a54c488c395dae5acf0875`
   - Data source ID: `e18dfe1a-fccb-4c36-beb2-634dc7e7cdd2`

---

## Execution Steps

### Adding a new priority

1. **Collect required fields.** Ask the user (in a single message, not sequentially):
   - Priority name
   - Level (P0 / P1 / P2)
   - DRI
   - **Sources** — "What are the sources for this initiative? (e.g., Slack channels, Notion pages, Google Docs, Explab links). This is required — it will be registered in sources.md and referenced in the Notion entry."

2. **Check sources.md for existing matches.** Before adding new rows, check whether any of the user-provided sources already exist in the registry. If they do, the priority name should be appended to the existing row's **Covers** column, not duplicated.

3. **Create the Notion entry.** Write to the Strategic Priorities DB with:
   - `Priority` = name
   - `Level` = P0 / P1 / P2
   - `Status` = Active
   - `Notes` = "DRI: [name]. [One-sentence description]. Sources: [list with URLs]."

4. **Update sources.md.** Add new rows for any sources not already in the registry. Append the priority name to the **Covers** column of any existing rows that apply.

5. **Confirm.** Return the Notion URL of the new entry and confirm sources.md was updated.

### Updating an existing priority

1. Fetch the current Notion entry.
2. Apply the requested change to `Priority`, `Level`, `Status`, or `Notes`.
3. If sources are being added or changed, update sources.md accordingly.

### Auditing priorities

1. Fetch all entries from the Strategic Priorities DB.
2. Read sources.md.
3. For each priority, check: does at least one source exist in sources.md? Flag any priorities with no registered source.
4. Surface the audit result as a table: priority name, level, DRI, sources (from sources.md), any gaps.

---

## Output Format

**New priority confirmation:**
> Added: [Priority Name] (P0/P1/P2) — DRI: [name]
> Notion: [URL]
> Sources registered: [list of source names added or updated in sources.md]

**Audit output:**
| Priority | Level | DRI | Sources | Gap? |
|----------|-------|-----|---------|------|
| ...      | ...   | ... | ...     | ✅ / ⚠️ No source |

---

## Write-Back

- Notion Strategic Priorities DB — one entry per priority
- `sources.md` in this repo — updated in the same operation as the Notion write. Never write to Notion without also updating sources.md.

---

## Hard Guardrails

- **Never create a Notion entry without first asking for sources.** A priority with no source registration is incomplete.
- **Never duplicate sources.md rows.** If the source already exists, append to the Covers column.
- **Never write to sources.md without also confirming the Notion Notes field references those sources.**
- Sources must be specific — "Notion" or "Slack" is not a valid source. A URL or channel ID is required.

---

## Self-Audit Checks

Before surfacing any output:
1. Does the new Notion entry have at least one source URL in the Notes field?
2. Is sources.md updated in the same operation (not deferred)?
3. Are there any duplicate rows in sources.md after the update?
4. Is the DRI named explicitly (not just a team name)?

---

## Known Failure Modes

- User provides a vague source ("we discuss this in weekly syncs") — push back and ask for the specific Notion page URL or Slack channel name.
- sources.md grows stale when priorities are updated in Notion directly without going through this skill — mitigate by including a sources audit step in EOD wrap or periodic reviews.
- Multiple priorities share a source but it's registered separately for each — causes drift. Always check for existing rows before adding.
