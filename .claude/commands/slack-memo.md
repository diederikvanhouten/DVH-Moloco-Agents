# 💬 /skill-slack-memo

> **Source:** Moloco Chief of Staff operating system — exported 2026-May-09
> **Scope:** Generic methodology — adapt tool names, org structure, and personas to your context.
> **Note:** The Gold Standard Example uses real names from a real internal meeting (May 6, 2026). Treat as illustrative — replace with your own examples as you calibrate this skill.

## Purpose
Draft a concise Slack alignment memo after a meeting, summarizing agreed decisions and inviting confirmation. Output should be immediately sendable — no editing required.

---

## Trigger
"Send a note to [person]", "put together a Slack message summarizing what we landed on", "draft a quick note for [person] on [decision]", or equivalent after a meeting or decision conversation.

---

## Preconditions
Before drafting, retrieve the meeting context. In order of preference:
1. Meeting transcript, summary, or notes from your meeting tool
2. Explicit context provided in the conversation
3. If neither is available: ask for the key decisions before drafting

Do not draft from the AI summary alone if meeting notes are available — notes contain the decision-level language expected.

---

## Execution Steps
1. Pull meeting assets (see Preconditions above)
2. Identify: (a) the 1–3 core decisions or agreements, (b) any critical role/ownership clarifications, (c) any open question requiring recipient confirmation
3. Draft using the Output Format below
4. Run the Rubric (see below) before showing output — fix any failures silently

---

## Output Format
Slack message structure — 4 elements, in order:

**1. Greeting line** (1 sentence)
Informal. Name the recipients and anchor to the meeting. e.g. *"Hey [Name], [Name] — quick note from the conversation [attendees] and I just had."*

**2. Decision bullets** (2–3 max)
Bold label + concise statement. Each bullet = one decision or model. No sub-bullets. No examples. No elaboration beyond what is needed to act.
Format: `• **[Label]:** [decision statement]`

**3. Closing alignment question** (1 sentence)
Ends with a direct question inviting confirmation. e.g. *"Does this work?"* or *"Thoughts?"* — or both on separate lines.

**4. Nothing else.**
No action item lists. No examples. No escalation path explanations. No "happy to jump on a call." No sign-off.

---

## Rubric — Run Before Every Output
Score each criterion pass/fail. Fix all failures before showing output.

| # | Criterion | Pass condition | Failure mode to fix |
|---|-----------|---------------|---------------------|
| 1 | **Length** | ≤ 60 words total (excl. greeting and closing) | Cut to 2 bullets; remove examples and elaboration |
| 2 | **Bullet count** | 2–3 bullets max | Merge or drop bullets beyond 3 |
| 3 | **No action items** | Zero per-person action assignments in the body | Move to a separate follow-up or drop |
| 4 | **No elaboration** | No examples, no framework explanation, no escalation path walkthrough | Cut everything after the core decision statement |
| 5 | **Closes with a question** | Final line is a question inviting alignment ("Does this work?", "Thoughts?") | Add confirmation question if missing |
| 6 | **No fluff closer** | No "happy to jump on a call", no sign-off, no "let me know if anything is unclear" | Delete |
| 7 | **Bold labels** | Each bullet opens with a **bold label** followed by a colon | Add label if missing |
| 8 | **Greeting anchors the meeting** | Greeting names attendees and references the meeting | Rewrite greeting |

**Scoring:** 8/8 = send. Any failure = revise before output. Do not show a draft that fails the rubric.

---

## Known Failure Modes
- **Too long:** Meeting contained many decisions → resist the urge to include all of them. Recipients only need what's actionable for them.
- **Action items leaked in:** Meeting next-steps often contain per-person actions → these belong in a separate task system, not in the Slack memo.
- **Missing confirmation question:** Draft reads like an announcement → always close with a question.
- **Over-explaining the framework:** If the decision involves a model or process, state the outcome only — not the reasoning that led to it.
