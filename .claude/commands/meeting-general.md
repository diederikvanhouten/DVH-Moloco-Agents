# 📅 /skill-meeting-general

> **Source:** Moloco Chief of Staff operating system — exported 2026-May-09
> **Scope:** Generic methodology — adapt tool names, org structure, and personas to your context.

**Purpose:** Generate a meeting brief for ad hoc or general meetings not covered by a specific brief type.
**Trigger:** "Prep for [meeting name]" or meeting appears on calendar without a specific brief type

---

## Preconditions
1. Fetch your operating rules / system index — hard gate. Halt if unreachable.
2. Check your meeting briefs store for an existing brief for this meeting date and title. If found, surface and ask. If not, proceed.

---

## Execution Steps
1. Read your strategic priorities database.
2. Resolve the meeting document using the Document Link Priority Chain.
   - If doc found: read it, apply the two-pass rule, check your action items store for open items, add to Documents to Review if not already there.
   - If no doc found: flag gap, build context-based agenda.

---

## Output Format — When Document Available
Headed `Meeting Brief — [Meeting Name] — [Day, Date]`:
> **Created:** [YYYY-Mon-DD HH:MM TZ] | **Last Updated:** [YYYY-Mon-DD HH:MM TZ]

- **Document summary** — 3-5 sentences on purpose, key decisions, open questions.
- **Strategic relevance** — map explicitly to your top priorities. Flag if no connection exists.
- **Strategic questions to ask** — up to 5, filtered by: (1) signal not symptom, (2) your role test (only topics where your intervention creates leverage).
- **Open items** for this attendee group from your action items store (Status = Open or In Progress only).
- **Context gap flag** if applicable.

## Output Format — When No Document Available
Headed `Meeting Brief — [Meeting Name] — [Day, Date]`:
> **Created:** [YYYY-Mon-DD HH:MM TZ] | **Last Updated:** [YYYY-Mon-DD HH:MM TZ]

- **Context summary** — what you found from Slack/email/other tools; note which priorities the meeting likely touches.
- **Proposed agenda** — 6 items ranked by your priority framework.
- **Open items** for this group from your action items store.
- **Context gap flag.**

---

## Write-Back
Create a dated page for the meeting day in your meeting briefs store (if it doesn't already exist). Then create a subpage titled `[Meeting Name]`. The brief body must begin with the date as the first line before any other content. Confirm with URL.

---

## Hard Guardrails
- Document Link Priority Chain is the only acceptable method for resolving pre-reads — never use a cached link or one from memory
- Never surface Complete items as open
- Two-pass rule is mandatory on any doc found — not optional

---

## Known Failure Modes
- Using a previously known document link instead of resolving via the priority chain
- Skipping Pass 2 of the two-pass rule on the meeting doc
- Creating a new brief without checking for an existing one first
