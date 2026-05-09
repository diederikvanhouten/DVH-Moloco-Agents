# Chief of Staff Skills — AI Operating System

A collection of reusable skill definitions for AI chief-of-staff agents. Each skill is a self-contained methodology document covering: trigger conditions, execution steps, output format, write-back rules, hard guardrails, and known failure modes.

**Source:** Exported from a production Chief of Staff AI operating system (Moloco, May 2026).
**Scope:** Generic methodology. Skill files include adaptation notes where org-specific examples or tool references appear.

---

## Skills

| File | Trigger | Purpose |
|------|---------|---------|
| [`skill-1pager-decision-doc.md`](skills/skill-1pager-decision-doc.md) | "Write a 1-pager / decision doc" | Author a decision document or strategy memo — no headers, no bullets, pure dense prose, one page by default |
| [`skill-deep-dive.md`](skills/skill-deep-dive.md) | "Deep dive on [topic]" | Two-phase research and analysis workflow — Phase 1 outline, Phase 2 full prose narrative with source attribution and live data proposals |
| [`skill-eod-wrap.md`](skills/skill-eod-wrap.md) | "EOD wrap" / "Wrap up today" | Process all completed Zoom meetings for a day — match to Notion briefs, prepend AI summaries, extract and log action items, idempotent |
| [`skill-meeting-general.md`](skills/skill-meeting-general.md) | "Prep for [meeting]" | Generate a meeting brief for any ad hoc meeting — doc summary, strategic relevance, questions to ask, open items |
| [`skill-notion-to-github.md`](skills/skill-notion-to-github.md) | "Push skills to GitHub" / "Sync Notion skills to the repo" | Export generic reusable skills from a Notion operating system to GitHub — two-gate sensitivity evaluation, per-skill audit report, cleanliness verification |
| [`skill-slack-memo.md`](skills/skill-slack-memo.md) | "Send a note to [person] on [decision]" | Draft a post-meeting Slack alignment memo — 4-element structure, ≤60 words, closes with a confirmation question |
| [`skill-system-maintenance.md`](skills/skill-system-maintenance.md) | "Add a capability / fix a rule" | Govern how the AI operating system is extended and maintained — three-layer model, decision tree, bloat prevention |

---

## How to Use These Skills

Each skill file is a standalone markdown document. To use one:

1. **Read the skill file** at the start of the relevant task — treat its contents as binding execution instructions.
2. **Adapt tool references** — skills reference Notion, Zoom, Slack, and Google Calendar by name. Replace with your own connectors.
3. **Replace org-specific values** — Notion page IDs, database IDs, alias maps, and example names are illustrative. Swap in your own workspace IDs and people.
4. **Respect the hard guardrails** — each skill has a section of rules that cannot be bypassed. These exist because they've prevented real failures in production use.

---

## Skill Structure

Every skill follows this standard structure:

- **Purpose** — one sentence on what the skill does
- **Trigger** — exact phrases or conditions that invoke it
- **Preconditions** — gates that must pass before execution (always includes fetching the operating rules index)
- **Execution Steps** — numbered, ordered, source-specific
- **Output Format** — exact heading format and section content
- **Write-Back** — where output is saved and how it's confirmed
- **Hard Guardrails** — rules that cannot be violated
- **Self-Audit Checks** — yes/no checklist run against every output before surfacing it
- **Known Failure Modes** — specific ways this skill has broken in practice

---

## Adapting for Your Context

These skills assume:
- A **Notion** workspace as the primary docs and task system
- **Zoom for Claude** as the meeting connector
- A **Slack** connector for messaging
- A **Google Calendar** connector for meeting context
- An **operating rules index page** in Notion that skills fetch as a hard gate before execution

If your stack differs, the execution steps and tool call names will need updating — the methodology (matching algorithm, output format, rubrics, guardrails) is fully portable.
