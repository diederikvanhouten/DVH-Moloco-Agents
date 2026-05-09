# DVH-Moloco-Agents

Chief of Staff AI operating system — a collection of reusable skills for strategic work.

## What this repo is

Each file in `skills/` is a self-contained skill definition: trigger conditions, execution steps, output format, write-back rules, hard guardrails, and known failure modes. They were exported from a production Chief of Staff operating system at Moloco (May 2026) and are intended to be adapted to any context.

## Slash commands (Claude Code)

Skills are available as slash commands from `.claude/commands/`. Invoke them by name:

| Command | Trigger phrase | What it does |
|---|---|---|
| `/meeting-general` | "Prep for [meeting]" | Meeting brief — doc summary, strategic questions, open items |
| `/deep-dive` | "Deep dive on [topic]" | Two-phase research: Phase 1 outline, Phase 2 full prose narrative |
| `/1pager-decision-doc` | "Write a 1-pager / decision doc" | Dense prose decision document, no bullets, one page |
| `/eod-wrap` | "EOD wrap" / "Wrap up today" | Process completed Zoom meetings, match to Notion briefs, extract action items |
| `/slack-memo` | "Send a note to [person] on [decision]" | ≤60-word Slack alignment memo, closes with a confirmation question |
| `/notion-to-github` | "Push skills to GitHub" / "Sync Notion skills" | Export generic skills from Notion to this repo with sensitivity gating |
| `/system-maintenance` | "Add a capability / fix a rule" | Governs how this operating system is extended and maintained |

## Adapting skills to your stack

Skills reference these tools by name — replace with your own connectors if different:
- **Notion** — docs, tasks, meeting briefs, action items store
- **Zoom for Claude** — meeting transcripts
- **Slack** — messaging
- **Google Calendar** — meeting context

Each skill file has an adaptation note section. At minimum, replace:
1. Notion page/database IDs with your own workspace IDs
2. Alias maps and example names with your own people
3. Tool call names if your connectors differ

## Hard rule

Every skill has a **Hard Guardrails** section. These rules are non-negotiable — they exist because they've prevented real failures in production. Never skip or bypass them.

## Editing skills

Edit files directly in `skills/` — that is the source of truth. After editing, the corresponding file in `.claude/commands/` needs to be updated to match (or re-run the sync). When the skill is stable and generic, use `/notion-to-github` to push it back to the GitHub repo.
