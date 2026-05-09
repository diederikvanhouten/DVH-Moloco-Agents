# 🌙 /skill-eod-wrap

> **Source:** Moloco Chief of Staff operating system — exported 2026-May-09
> **Scope:** Generic methodology — adapt tool names, org structure, and personas to your context.
> **Note:** The alias map in Zoom Processing Core contains real internal names (e.g., "Frenzo" ↔ "Francesco Renzo"). Replace with your own aliases. Notion page IDs (Meeting Briefs root, To Do List DB, etc.) must be replaced with your own workspace IDs.

**Purpose:** Process all completed Zoom meetings for a given day (or set of days) — prepend Zoom AI summaries to matching Notion meeting brief pages, create briefs for unmatched meetings, log owned action items to the To Do List DB, and produce a compact wrap summary. Runs idempotently — safe to trigger more than once.
**Trigger (manual):** "Wrap up today" / "EOD wrap" / "End of day" / "Daily wrap" / "Summarize today's meetings" / "Wrap up [date]"
**Trigger (automated):** Called by /skill-weekly-brief at the start of every daily brief run, targeting the previous business day(s).

---

## Preconditions
1. Fetch Operating Rules page (`35bcdb3513368176a3a6f9c18ac43233`) — hard gate. Halt if unreachable.
2. Call `user_time_v0` — mandatory before any Zoom query or timestamp write. Establishes the user's current local timezone. Never assume PST.
3. **Determine target date list:**
   - Manual trigger with a specific date: process that single date.
   - Manual trigger with no date / "today": process today.
   - Automated trigger from /skill-weekly-brief:
     - If today is **Tuesday–Friday**: target = yesterday only.
     - If today is **Monday**: target = [last Friday, last Saturday, last Sunday] — processed sequentially.
     - Saturday/Sunday: no automated trigger expected; if invoked, process yesterday.
4. All Universal Timestamp Rules from the Operating Rules index apply to this skill.

---

## Per-Date Execution (run Phases 1–6 once for each date in the target list)

### Phase 1 — Discover Meetings for Target Date
1. Call `user_time_v0`. Record current local time and timezone.
2. Compute UTC window: `from` = 00:00:00 local target date in UTC, `to` = 23:59:59 local target date in UTC. Cap `to` at current moment if target date is today.
3. Call `Zoom for Claude:search_meetings` with `from`, `to`, and the user's timezone.
4. Filter: keep only `meeting_status = HISTORY`. Discard scheduled, cancelled, or in-progress.
5. Verify returned meetings fall within the target date in local time — timezone offsets near midnight can bleed prior-day meetings in. Discard any meeting whose local `meeting_start_time` is not on the target date.
6. If 0 qualifying meetings: record `0 meetings` for this date in the summary. Continue to Phase 3 (still create/check the daily page and write its flag). Do not abort the full run.

### Phase 2 — Fetch Assets Per Meeting
1. For each qualifying meeting, call `Zoom for Claude:get_meeting_assets` with `meeting_uuid`.
2. Extract: (a) AI summary quick recap + full text, (b) next steps / action items, (c) participant list, (d) My Notes if present.
3. If AI summary is null: call `Zoom for Claude:get_recording_resource` with `types = "summary,nextStep,transcript"` as fallback.
4. **If `has_summary_permission: false` (no AI summary access):** Do NOT log as unresolvable gap. Instead, check `my_notes.transcript.transcript_items` from `get_meeting_assets`. If `transcript_items` has content, use it as the summary source — extract key discussion points, user contributions, and action items from the transcript and populate the Zoom Summary Block accordingly. Note the source: `⚠️ AI summary not accessible (no permission). Summary derived from My Notes transcript.`
5. If no summary AND no transcript AND no recording: flag meeting as `⚠️ No content — may still be processing`. Proceed with other meetings.

### Phase 3 — Locate or Create the Notion Daily Page
1. Compute the target date string in format `YYYY-Mon-DD` (e.g., `2026-Apr-28`).
2. Fetch Meeting Briefs root page (`35bcdb351336817f990ede78e968f26f`). Scan child pages for exact title match.
3. If not found in root: fetch the Archived Briefs page. Scan child pages for the same title.
4. **If still not found:** Create a new daily page titled `[YYYY-Mon-DD]` under Meeting Briefs root now. Record its URL.
5. **If found in either location:** Record the daily page URL. Fetch its full content to enumerate all subpage titles (individual meeting brief pages).

### Phase 3.5 — Idempotency Check (Day Level)
1. Fetch the daily page content. Check whether the very first line contains `🔁 EOD Wrap fetched:`.
2. **If the flag is present:** This day has already been processed. Skip Phases 4–6 entirely for this date. Record in the run summary: `[Date] — already processed, skipped`. Continue to the next date in the target list.
3. **If the flag is absent:** Proceed to Phase 4.

### Phase 4 — Match Zoom Meetings to Notion Brief Pages
1. For each Zoom meeting from Phase 1, attempt to match to a Notion brief subpage from the daily page.
2. Apply the **Matching Algorithm** defined in Zoom Processing Core (below).
3. One Notion brief page may match at most one Zoom meeting. If two Zoom meetings match the same brief, take the better match and flag the other as unmatched.

### Phase 5 — Write Zoom Summary to Notion
**Step A — Write the day-level EOD flag (always, before any individual briefs):**
1. Call `user_time_v0` for the write timestamp.
2. Prepend the following to the very top of the daily page using `update_content`:
```
🔁 EOD Wrap fetched: [Day, Date · HH:MM Pacific]
---
```
This line is the idempotency marker. It must be the first line of the daily page.

**Step B — Write Zoom Summary Blocks to individual brief pages:**
Apply the **Zoom Summary Block format** and **idempotency check** defined in Zoom Processing Core (below).

**For each confident match (existing brief page):** fetch immediately before writing, check idempotency, prepend block, confirm write, record URL. Existing content shifts down.

**For each unmatched Zoom meeting (no existing brief):** create a new brief subpage under the daily page. **Title rule:** if the Zoom topic is generic ("Zoom Meeting", "Zoom Call"), title the page `[Name 1] / [Name 2] — [HH:MM Pacific]` using participant names. Otherwise use the Zoom topic as-is. Populate with the Zoom Summary Block. Confirm write. Record URL.

### Phase 6 — Extract and Log Action Items
1. Scan next-steps, AI summary, and My Notes from ALL meetings with available content.
2. Apply the **Action Item Extraction Rules** and **To Do Write Rules** defined in Zoom Processing Core (below).
3. Source link = Notion brief page URL from Phase 5. If unavailable: move to Flagged Items.
4. Write all new To Do items before generating output.

---

## Output Format
After all target dates are processed, produce a single compact summary:

**EOD Wrap — [Date or date range] — [HH:MM Pacific]**

**Run Summary**
- Dates processed: [list]
- Meetings found: X total (Y with AI summary, Z without)
- Briefs updated: N (M matched to existing, P created new)
- Briefs skipped (already fetched): Q
- Action items logged: R new, S dedup-updated

**Meetings Summarized** — one sub-heading per date, followed by bullet points. Format:
**[Day, Date]:**
- [Brief title] ([Notion link]) — [1-sentence key topic or action count]

Never output meetings as inline text separated by ·. Always use per-day sub-headings with bullet points.

**Action Items Logged** — table: Action | Priority | Due | Source Meeting

**Flagged for Review**
- Ambiguous matches pending confirmation
- Meetings with no summary (list with retry suggestion)
- Stale deadline references

**Gaps** — API failures, skipped dates, write errors

---

## Write-Back
Phase 5 writes (Notion brief pages + daily flag) and Phase 6 writes (To Do items) happen before output. No approval loop. Every write is confirmed with URL in the output.

---

## Weekly Brief Integration
When /skill-weekly-brief runs, it must call this skill immediately before generating its own output, using the automated trigger logic (see Preconditions step 3). The EOD wrap summary is prepended to the weekly brief output under a `📋 EOD Wrap` section. If the wrap reports 0 meetings on all target dates, include that result in the brief rather than omitting it.

---

## Hard Guardrails
- Never write a Zoom Summary Block without fetching the target page immediately before. Stale fetches cause double-write.
- Never write the day-level EOD flag without first confirming the page exists and is writable.
- If the day-level flag is already present: skip the entire day — do not re-process any meetings.
- If a brief page already starts with `## 🎥 Zoom Summary`: skip that brief. Do not write again.
- Never log an action item without a source link. No link = Flagged Items.
- Never create a To Do item from a prior-year signal. Signal year gate is hard.
- Never attribute an action item to the user based on weak inference. When in doubt: Flagged Items.
- Ambiguous matches require confirmation before any write.
- `allow_deleting_content: true` is never used without explicit confirmation.

---

## Self-Audit Checks
1. Every action item in "Action Items Logged" has a source link.
2. No Complete items from To Do DB appear in output.
3. All timestamps carry an explicit timezone label.
4. Meeting topic names reproduced exactly as Zoom returned them.
5. No Zoom Summary Block written to a brief that already had one.
6. Day-level EOD flag written to every daily page processed (found, created, or previously existing without flag).
7. Every unmatched meeting has a confirmed new Notion page or is in Flagged Items.
8. Monday trigger processes exactly 3 dates: last Fri, last Sat, last Sun.

---

## Zoom Processing Core
*Canonical definitions shared with /skill-zoom-action-items. Any rule change must be made here — the other skill references this section by name.*

### Matching Algorithm
- Strip noise tokens from both sides: "Weekly", "Bi-weekly", "Sync", "Meeting", "1:1", "1x1", "Call", "Zoom", "Wkly".
- Match if: (a) ≥1 person name appears in both (first name sufficient), OR (b) ≥60% token overlap of the shorter title.
- Known alias map (replace with your own): "Frenzo" ↔ "Francesco Renzo"; "DJ" ↔ "Donghwan Jeon"; "Paankhuri" ↔ "ACE"; "Rahul" ↔ "CTV"; "Aparna" ↔ "Ecosystems"; "Willamette" ↔ "Tal".
  - "JL" ↔ "Jean-Luc Nahon"
  - "Ethan" ↔ "Ethan Kottke"
  - "Emilien" ↔ "Emilien Pouradier"
  - "Doug" ↔ "Doug Pereira"
  - "Ernest" ↔ "Ernest Yip"
  - "Hyunjoon" ↔ "Hyunjoon Choi"
  - "Guillem" ↔ "Guillem Sanchez"
  - "Sailesh" ↔ "Sailesh Mohapatra"
  - "Santiago" ↔ "Santiago Melo"
  - "Ruoyu" ↔ "Ruoyu Zhu"
- **Match outcomes:**
  - **Confident (≥60% overlap or name match):** Proceed with write.
  - **Ambiguous (30–59%):** Flag for confirmation before writing. Surface both titles: *"Is '[Zoom topic]' the same as '[Notion title]'? (y/n)"*
  - **No match (<30%, no name):** No existing brief — create new.

### Zoom Summary Block Format
Prepend using `update_content`. **Idempotency check first:** if the page's first line begins with `## 🎥 Zoom Summary`, skip — already processed. Record `⚠️ Already fetched — skipped`.
```
## 🎥 Zoom Summary — [Meeting Topic]
**[Day, Date · HH:MM Pacific · X min]**
**Participants:** [Participant 1, Participant 2, ...]

**Summary**
• [AI summary bullet — 3–6 bullets max]
• ...

**Action Items from This Meeting**
• [Natural-language commit — e.g. "Post recommendation in RE channel"]
• [Natural-language delegated follow-up — e.g. "Check with [Name] on compliance list by EOW"]

---
```
- Header line (`Day, Date · HH:MM Pacific · X min`) is mandatory on every write, even if no AI summary is available.
- If participants unavailable: write `Participants: [not available]`.
- If no summary: write `Summary: ⚠️ Not yet available — retry later`.
- Always fetch the target page immediately before writing. Never write from a stale fetch.

### Action Item Extraction Rules
**User commits:** The user is the owner. Signals: user's name, role title, unambiguous inference. Title phrasing: active first-person — e.g., "Post recommendation in RE channel", "Review CTV deck before Thursday".

**User delegates:** User assigned work to a named person. Signals: "user asked [person] to", "[Person] will [action]", explicit delegation. Title phrasing: natural follow-up — e.g., "Check with [Name] on compliance list", "Follow up with [Name] on positioning by EOW". Assignee's name must appear in the title.

When in doubt about ownership: move to Flagged Items. Never attribute to the user based on weak inference.

### To Do Write Rules
- **Semantic dedup:** 2–3 keyword searches against To Do List DB before creating any item. ~70% match = update existing item, not create new.
- **Signal year gate:** Prior-year deadline reference → flag `⚠️ Stale deadline — confirm`. Never auto-assign prior-year due dates.
- **Source link:** Mandatory on every item. No link = Flagged Items, no exceptions.
- **Priority:** P0 if maps to a P0 in Strategic Priorities DB. P1 default. P2 for administrative.
- **Fields:** Title (natural language per extraction rules), Status = Open, Due Date per defaults (P0=48h, P1=7d, P2=30d) or explicit meeting deadline, Source Link = brief URL, Type = Action.

---

## Known Failure Modes
- Zoom AI summary not yet generated (~15–30 min lag) — brief gets placeholder, re-run resolves. Day-level flag will be set on first run; re-run won't overwrite existing content but also won't retry no-summary meetings (add a "force retry" override if needed in future).
- Daily page exists in archive with slight title variation — surface raw page list for user to confirm before proceeding.
- Fuzzy match false positive (e.g., "Steve" matching an unrelated Steve brief) — always include both titles in output; user can correct via ambiguous match flow.
- `update_content` failing on a very short or empty brief page — fall back to `replace_content` with Zoom block as full content.
- Two Zoom meetings matching the same Notion brief — take the closer start-time match, flag the other as unmatched.
- Monday holiday edge case (e.g., Monday is a public holiday, last meeting day was Thursday) — not auto-handled; user should specify the date manually.

---

## 🔴 Zoom Connector Failure Protocol
*This section addresses connector-level failures — distinct from API-level failures (summary not yet generated, no permission). A connector-level failure means the MCP tool itself is unreachable: `ERR_NETWORK_CHANGED`, persistent network errors, timeout on every call, or zero response on `search_meetings`.*

**How to distinguish connector-level vs. API-level failure:**
- **Connector down:** `search_meetings` itself fails or returns a network error. All calls fail regardless of meeting UUID. The tool is unreachable.
- **API-level:** `search_meetings` succeeds and returns meetings. `get_meeting_assets` returns `has_summary: null` (not yet generated) or `has_summary_permission: false` (no access). The connector is working; the content is unavailable.

**When connector-level failure is detected:**
1. **Do NOT write the day-level EOD idempotency flag** (`🔁 EOD Wrap fetched:`). Absence of the flag enables automatic retry on the next run without manual intervention.
2. **Write a connector-failure note to the daily page** in place of the EOD flag:
```
🔴 ZOOM CONNECTOR DOWN — [Day, Date · HH:MM Pacific]
Zoom for Claude MCP connector was unreachable during this run. Zoom summaries not fetched. Re-run when connector recovers to complete this day's processing.
```
3. **Flag the failure prominently in the weekly brief output** under a red callout block:
```
🔴 ZOOM CONNECTOR — PARTIAL DATA
Connector was unreachable during [Day, Date] processing. The following meetings have no Zoom summary and require a follow-up run:
- [Meeting 1 title] · [HH:MM Pacific]
- [Meeting 2 title] · [HH:MM Pacific]
Re-run /skill-eod-wrap targeting [date] to recover summaries when connector is restored.
```
4. **Do not attempt to infer or reconstruct meeting content** from pre-read docs, calendar invites, or prior context. Only actual Zoom AI summary content belongs in Zoom Summary Blocks.
5. **All action item extraction for affected meetings is deferred** — do not create To Do items from meetings that could not be fetched. Log them as `⚠️ Deferred — connector failure` in the Flagged Items section.
6. **Recovery:** On next run, the absent idempotency flag will trigger normal Phase 1–6 processing for the affected date. The connector-failure note on the daily page will be replaced by the normal EOD flag once processing completes. Individual brief pages that already have Zoom Summary Blocks will be skipped (idempotency check).

**Important distinction from API-level unavailability:**
- No AI summary generated (`has_summary: null`) → write placeholder block, set day-level flag normally, retry is manual.
- No permission (`has_summary_permission: false`) → check `my_notes.transcript.transcript_items`. If transcript available: use it to generate the summary and action items, note source in block header. If transcript also unavailable: log as unresolvable gap, document in brief, set day-level flag normally.
- Connector down (`ERR_NETWORK_CHANGED` or tool unreachable) → do NOT set flag, write connector-failure note, flag in brief, auto-retry on next run.
