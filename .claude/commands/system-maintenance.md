# 🔧 /skill-system-maintenance

> **Source:** Moloco Chief of Staff operating system — exported 2026-May-09
> **Scope:** Generic methodology — adapt tool names, org structure, and personas to your context.

**Purpose:** Govern how the AI operating system is extended, modified, and maintained. Prevents bloat, duplication, and structural drift over time.
**Trigger:** Any request to add a new capability, change an existing rule, fix a broken behavior, or restructure any part of the system.

---

## Core Principle

The system has three layers. Every change must go to the right layer — nothing else.

| Layer | What lives here | When to edit |
|-------|----------------|--------------|
| **Index** (your main operating rules page) | Universal rules, DB IDs, shared protocols | Only when a rule applies to every task without exception |
| **Skill page** (subpage of index) | Task-specific execution logic, output format, guardrails | When a rule applies to one task type only |
| **Neither** | Everything else | Don't add it — see Bloat Prevention below |

---

## Decision Tree — Where Does This Change Go?

**Step 1:** Does this rule apply to every single task the AI performs?
- Yes → **Index** (Universal Protocols section)
- No → Go to Step 2

**Step 2:** Does this rule apply to exactly one task type?
- Yes → **Relevant skill page**
- No → Go to Step 3

**Step 3:** Does this rule apply to 2–4 related task types?
- Yes → Add to each relevant skill page individually. Do not add to the index.
- No → Go to Step 4

**Step 4:** Is this a one-off contextual instruction for a single session?
- Yes → Handle in conversation. Do not write to your notes system.
- No → Rethink whether this needs to be a rule at all.

---

## When to Create a New Skill Page

Create a new skill page when:
- You regularly ask the AI to perform a task type not covered by any existing skill
- The task has a distinct trigger, execution sequence, output format, and write-back pattern
- The task is complex enough to have meaningful guardrails and known failure modes

Do NOT create a new skill page when:
- The task is a minor variation of an existing skill (extend the existing page instead)
- The task is a one-time request
- The rule could be expressed in 1–2 sentences added to an existing skill's guardrails

---

## How to Write a New Skill Page

Every skill page must have these sections in this order:
1. **Purpose** — one sentence: what this skill does
2. **Trigger** — exact phrases or conditions that invoke this skill
3. **Preconditions** — gates that must pass before execution starts (always includes: fetch operating rules index)
4. **Execution Steps** — numbered, ordered, specific. No vague steps like "gather context" — name the source and method.
5. **Output Format** — exact heading format, section names, content of each section
6. **Write-Back** — where output is saved, naming convention, confirmation step. Include: "No approval loop" if applicable.
7. **Hard Guardrails** — rules that cannot be violated under any circumstance
8. **Self-Audit Checks** — a numbered checklist run against the *output* before showing it. Each check is a yes/no question about the output itself. If any check fails, fix silently before showing output. Only surface if the failure cannot be resolved without input. This section is distinct from Known Failure Modes: Known Failure Modes govern execution; Self-Audit Checks govern output quality. This is a living list — add new checks whenever a quality error is identified in a real output.
9. **Known Failure Modes** — specific ways this skill has broken or is likely to break

After writing, add a row to the Skill Directory table in the index.

---

## How to Modify an Existing Skill Page
1. **Fetch the skill page fresh** before editing — never edit from a stale version in context
2. **Use targeted search-and-replace** — never replace the entire page content unless the entire page is being rewritten
3. **Never delete content** without first listing what would be deleted and getting explicit confirmation
4. **Verify Self-Audit Checks section exists.** Before completing any modification, confirm the skill page has a Self-Audit Checks section. If it doesn't, create it (even if empty) as part of the same edit. A modification is not complete without this section present.
5. **Test the change** — if the modification affects output behavior, run one real task after the change and verify the rule is honored
6. **Update Known Failure Modes** if the change was prompted by a real failure

---

## How to Modify the Index

The index is the highest-risk page to edit. Extra rules apply:
1. Fetch the index fresh immediately before editing
2. Use targeted edits to specific sections — never replace the entire page
3. After any index edit, fetch the page again and verify the change landed correctly
4. If a new universal protocol is being added, check first whether it belongs in the index or in individual skill pages (apply the decision tree above)

---

## Bloat Prevention Rules

**Compression over completeness.** If a rule can be expressed in one sentence, it should be one sentence. If it needs a paragraph, ask whether it's actually universal or task-specific.

**No duplication across layers.** A rule that lives in the index must not be repeated in skill pages. A rule that lives in a skill page must not be repeated in other skill pages — if the same rule appears in 3+ skills, it belongs in the index instead.

**No orphaned rules.** Every rule must have a clear trigger for when it applies. Rules without a trigger are noise.

**No versioning inside skill pages.** Skill pages don't need changelogs. Only the index carries a version + changelog. When a skill page is updated, just update it — no "Previous:" annotations.

**Retire stale rules explicitly.** When a rule is no longer valid (e.g., a tool was renamed, a person left, a workflow changed), delete it. Don't leave it and add a caveat — remove it.

---

## Hard Guardrails
- Index changes must use targeted edits — never replace the full page
- Content deletion requires explicit confirmation every time, no exceptions
- New capability = new skill page, not a longer index
- Duplication across pages is always a bug — fix it by choosing the right layer
- **Every skill page must have a Self-Audit Checks section.** A skill page without this section is incomplete. Creating or modifying a skill without adding or verifying this section is a hard failure.

---

## Known Failure Modes
- Replacing the full index page and losing child skill page references — use targeted edits only
- Allowing content deletion without explicit confirmation — always stop and confirm first
- Adding task-specific rules to the index because it's faster — apply the decision tree
- Creating a new skill page for a minor variation instead of extending an existing one — check existing skills first
- Forgetting to add a new skill to the Skill Directory in the index after creating it
