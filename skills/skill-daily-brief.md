# 📋 /skill-daily-brief

> **Source:** Moloco Chief of Staff operating system — created 2026-May-13
> **Scope:** Org-specific — references DVH's Strategic Priorities DB, sources.md, Notion workspace, Slack, Google Calendar, and Zoom.

**Purpose:** Generate a daily chief-of-staff brief covering the status of all 11 strategic priorities, key metrics, takeaways from yesterday's meetings, upcoming meetings today, and action items per priority owner. Writes a Notion page and sends a Slack DM to self with a link.
**Trigger (automated):** Runs every weekday at 7:00 AM ET via scheduled task.
**Trigger (manual):** "Daily brief" / "Morning brief" / "Run daily brief" / "Brief me"

---

## Preconditions

1. Call `user_time_v0` — mandatory before any timestamp or calendar query. Establishes current local time and timezone.
2. Read `sources.md` from this repo — canonical source registry. Do not proceed without it.
3. Fetch Strategic Priorities DB (Notion DB ID: `f551ee8631a54c488c395dae5acf0875`, data source ID: `e18dfe1a-fccb-4c36-beb2-634dc7e7cdd2`) — read all 11 active entries. Extract for each: `Priority`, `Level`, `Status`, `Notes` (contains DRI and source list).
4. Probe MCPs: Notion, Slack, Google Calendar, Zoom. On any failure, note the gap in the output but do not halt — produce the brief with available sources only, noting which sources were unreachable.

---

## Execution Phases

Run Phases 1–4 in parallel where inputs do not depend on each other. Wall-clock target: 3–5 minutes.

---

### Phase 1 — Gather priority signals (run all 11 in parallel)

For each of the 11 strategic priorities, pull signals from its registered sources in `sources.md` Section 2. Run all priority fetches simultaneously.

**For each priority, gather:**

**1a. Slack — last 24 hours**
- Read each registered Slack channel for the priority (last 24h, limit 50 messages per channel).
- Read registered DM threads with the priority's DRI (last 24h).
- Extract: decisions made, blockers raised, metric callouts, escalations, open questions.

**1b. Google Docs / Sheets**
- Fetch the Q2 2026 Consolidated Moloco Ads OKRs doc (`https://docs.google.com/document/d/1oTqHfzu05_oVi0YA2U1B_xHWhW8RhaepvV9YXUi7ehA`) — read the section(s) relevant to each priority.
- Fetch the Weekly Product Metrics Review doc (`https://docs.google.com/document/d/1d8g2dsKQJXgbvlcit1DdiLyli6EPa0seEucijWw3kR0`) — extract any metric updates for the priority.
- For priorities with a dedicated doc (Android Performance Weekly, Signals & Identity Strategy Brief), fetch that doc and extract updates since yesterday.

**1c. Notion**
- Fetch the ML/RS/Bidder Quality Weekly Team Sync page (`https://www.notion.so/moloco/ML-RS-Bidder-Quality-Weekly-Team-Sync-Meeting-log-461303e369654bd4be748cb36a6c4313`) for priorities mapped to it (Bidding Control Tower, Person & Household Identity Graph, pLTV, Key Experiments, Modeling Progress).

**1d. Explab**
- For priorities mapped to Explab (pLTV Alpha, Key Experiments, Modeling Progress): fetch `https://explab-prod.adsmoloco.com` — surface any experiments that launched, concluded, or changed status in the last 24h.

**Synthesize per priority into a structured object:**
```
{
  priority: string,
  level: P0|P1|P2,
  dri: string,
  status_sentence: string,          // 1-sentence current state
  key_metrics: [string],            // up to 3 specific numbers with sources
  what_moved: [string],             // bullets from Slack/docs since yesterday
  dvh_action_items: [string],       // action items for DVH
  dri_action_items: [string]        // action items for the DRI
}
```

**Rules for action item extraction:**
- DVH action items: anything where DVH is tagged, owes a response, or needs to unblock.
- DRI action items: anything the DRI committed to, was asked to deliver, or is blocking a milestone.
- If no action items can be identified for a party, omit that field (do not write "None").
- Action items must be specific — "follow up with Doug" is not valid; "follow up with Doug on MDF structure proposal (due May 16)" is.

---

### Phase 2 — Yesterday's meetings (run in parallel with Phase 1)

**2a. Discover yesterday's meetings**
- Compute yesterday's date in the user's local timezone.
- Call `Zoom for Claude:search_meetings` with yesterday's date window.
- Filter: `meeting_status = HISTORY` only.

**2b. Fetch summaries (lighter version)**
- For each meeting, call `Zoom for Claude:get_meeting_assets`.
- Extract only: (a) what was discussed (2–3 bullets), (b) what was decided (1–2 bullets), (c) any action items assigned to DVH.
- If `has_summary_permission: false`: check `my_notes.transcript.transcript_items` as fallback. If still no content: note the meeting title and attendees only, with `⚠️ Summary not accessible`.
- Skip meetings with 0 content (no summary, no transcript, no notes).

**2c. Map meetings to strategic priorities**
- For each meeting with content, tag it with the relevant strategic priority or priorities (by attendee and topic match). A meeting may tag multiple priorities.
- Meetings not linked to any priority go into a "General" bucket.

---

### Phase 3 — Today's meetings

- Call `Google Calendar:get_events` for today (00:00 → 23:59 local time).
- Filter: accepted invites only. Skip focus-time blocks and all-hands (>30 attendees).
- For each meeting: extract title, time, attendees, and note which strategic priority it maps to (if any).
- Order chronologically.

---

### Phase 4 — Write the Notion page

**4a. Locate or create the Daily Briefs parent page**
- Search Notion for a page titled "Daily Briefs" (DVH workspace root). If not found, create it now.
- Record the parent page URL.

**4b. Idempotency check**
- Search child pages of Daily Briefs for a page titled `Daily Brief — YYYY-Mon-DD` (today's date).
- If it exists: fetch it. Check whether the first line contains `✅ Brief generated:`. If yes, halt and DM self the existing page URL: "Brief already generated today — [URL]". If no: overwrite the content (partial run from earlier).
- If it does not exist: create a new child page titled `Daily Brief — YYYY-Mon-DD`.

**4c. Page content (write in this order)**

```
✅ Brief generated: [ISO timestamp]

---

# Strategic Priorities

---

## [Priority Name] — [P0/P1/P2] | DRI: [Name]

**Status:** [status_sentence]

**Key Metrics**
- [metric 1 with source]
- [metric 2 with source]
- [metric 3 with source]

**What moved since yesterday**
- [bullet]
- [bullet]

**Action Items**
- DVH: [item]
- [DRI Name]: [item]

---

[repeat for all 11 priorities, ordered P0 → P1 → P2]

---

# Yesterday's Meetings

## [Meeting Title] — [HH:MM] | Tags: [priority]
**Discussed:** [2–3 bullets]
**Decided:** [1–2 bullets]
**DVH AIs from this meeting:** [bullets, or omit if none]

---

# Today's Meetings

| Time | Meeting | Attendees | Priority |
|------|---------|-----------|---------|
| HH:MM | [title] | [names] | [priority or —] |

---
```

**Ordering rules:**
- Strategic Priorities: P0 first, then P1, then P2. Within level, preserve order from Strategic Priorities DB.
- Yesterday's Meetings: chronological by meeting start time.
- Today's Meetings: chronological.

**Omission rules:**
- If a priority has no Slack/doc signal in the last 24h and no meeting yesterday, still include it — write `Status: No signal in last 24h.` and omit Key Metrics and What Moved. Keep Action Items if any exist from prior context.
- If a section (Yesterday's Meetings, Today's Meetings) has no entries, omit that section header entirely.

---

### Phase 5 — Send Slack DM to self

After Phase 4 completes and the Notion page URL is confirmed:

Send a DM to DVH's own Slack user ID (`U09114HRLMB`) with this format:

```
📋 Daily Brief — [Day, Mon DD]
[Notion page URL]

*P0 pulse:*
• tKPI: [one-word status emoji + 1-line]
• IAA: [one-word status emoji + 1-line]
• Remarketing: [one-word status emoji + 1-line]

*Your action items today:*
• [DVH AI #1 — priority tag]
• [DVH AI #2 — priority tag]
• [DVH AI #3 — priority tag]
[+ N more in the full brief if >3]

*Meetings today:* [count] | First up: [HH:MM] [title]
```

**Status emoji key:** 🟢 on track | 🟡 needs attention | 🔴 at risk | ⚪ no signal

**Rules:**
- P0 pulse: always show all 3 P0 priorities (tKPI, IAA, Remarketing). One line each.
- DVH action items: surface up to 3 in the Slack message; link to full brief for the rest.
- If 0 DVH action items: omit that section.
- Meetings: total count of accepted meetings today; name only the first one.

---

## Output Format

**Notion:** `Daily Brief — YYYY-Mon-DD` child page under the Daily Briefs parent. Full structured content per Phase 4c.

**Slack:** Compact DM to self per Phase 5 format. Always includes the Notion page URL on line 2.

---

## Write-Back Rules

- Always create the Notion page before sending the Slack DM — the DM must include a valid Notion URL.
- Never send the Slack DM if the Notion write failed.
- If the Notion write partially failed (some priorities missing): still send the Slack DM, note: `⚠️ Brief is partial — [N] priorities missing due to source errors.`
- Do not write to sources.md or Strategic Priorities DB — this skill is read-only with respect to those registries.

---

## Hard Guardrails

- **Never fabricate metrics.** If no metric is found for a priority from its registered sources, omit the Key Metrics section for that priority. Do not estimate or interpolate.
- **Never write action items without a source.** Every action item must trace to a specific Slack message, doc excerpt, or meeting decision. If the source is ambiguous, omit the item.
- **Never send the Slack DM without a Notion URL.** The link is the primary value of the Slack message.
- **DRI must be a named person, not a team.** Read DRI from the Notion entry Notes field. If the Notes field has a team name instead of a person, surface it as-is but flag: `⚠️ DRI is a team, not a named person — consider updating the Notion entry.`
- **Do not reprocess today if the idempotency flag is set** unless the user explicitly says "re-run" or "regenerate."

---

## Self-Audit Checks

Before writing the Notion page:
1. Do all 11 priorities appear in the output?
2. Does every action item have an owner (DVH or DRI name)?
3. Does every metric cite its source (doc name, Slack channel, or Explab)?
4. Are P0 priorities listed before P1 and P2?

Before sending the Slack DM:
1. Does the message include the Notion URL on line 2?
2. Are all 3 P0 priorities represented in the pulse section?
3. Is the message ≤30 lines? If not, trim DVH AIs to top 3 and truncate.

---

## Known Failure Modes

- **Slack rate limits on parallel channel reads:** if reading all 11 priorities' channels simultaneously hits rate limits, fall back to sequential reads by priority level (P0 first).
- **Google Doc too large to read in one call:** if the Q2 OKRs doc or Weekly Metrics Review is very long, use the Glean MCP (`mcp__6737bdfa-49bd-4331-9626-e89d308d1616__search`) to search for priority-specific excerpts rather than reading the full doc.
- **Zoom summaries not yet generated at 7 AM:** yesterday's meetings may still be processing. If `has_summary: false` for a meeting that ended less than 4 hours ago, note: `⚠️ Summary may still be processing — re-run /eod-wrap later to capture it.`
- **Explab unauthenticated:** if Explab requires browser login, note which priorities are missing experiment data and suggest checking manually.
- **DRI DM has no recent messages:** if a DRI's DM thread has been quiet for >48h, do not flag this as a gap — it's normal. Only surface DM content when a decision or commitment is explicit.

---

## Source Registry Reference

| Priority | Level | DRI (from Notion) | Slack Sources | Doc Sources |
|----------|-------|-------------------|---------------|-------------|
| tKPI GA Launch | P0 | — | #tkpi-pne, #dsp-xfn-tkpi, #tkpi-ml-model, #tkpi-alpha-sales, #tmp-tkpi-process-improvements | Q2 OKRs, Weekly Metrics Review |
| IAA GA Launch | P0 | — | #iaa-xfn, #iaa-core-lt, #iaa-network-eng-prod, #iaa-network-xfn, #iaa_global_activation, #tmp-iaa-loop-specific-template | Q2 OKRs, Weekly Metrics Review |
| Remarketing $160M GAS ARR | P0 | Doug Pereira / Guillem Sanchez | #re-working-group-internal, #ml-reengagement, #retargeting-discussion-group | Q2 OKRs |
| Bidding Control Tower | P1 | Guillem Sanchez | #android-performance-weekly-2026 | Android Performance Weekly doc, ML Sync |
| Campaign Excellence / Blueprint | P1 | Santiago Melo | #dsp-q2-okr-blueprint-campaign-excellence | Q2 OKRs |
| Simplified Campaign Experience | P1 | Ernest Yip | #ads-manager-nextgen | Q2 OKRs |
| Event SDK MVP | P1 | Ruoyu Zhu | #identity-core-and-ctv, #owner-dsp-identity | Signals & Identity Strategy Brief |
| Person & Household Identity Graph | P1 | Ruoyu Zhu | #identity_v5, #identity-core-and-ctv, #owner-dsp-identity | Signals & Identity Strategy Brief, ML Sync |
| pLTV Alpha + Signal Utilization Infra | P1 | — | #tmp-pltv-signal-2026-q2-okrs | Signals & Identity Strategy Brief, Explab, ML Sync |
| Key Experiments | P1 | Camille Wormser | #android-performance-weekly-2026 | Explab, ML Sync |
| Modeling Progress | P1 | Camille Wormser | #tkpi-ml-model | Explab, ML Sync |

*DRIs marked — should be read from the Notion Strategic Priorities DB entry at runtime.*
