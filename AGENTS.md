# Agent Instructions

This repository carries a documentation discipline enforced by a Cursor rule and executed by a
skill. Read this file before writing any markdown.

## The invariant

**Code and tests are the contract.** A committed document is allowed only for what they cannot
express. Before adding or extending any document, run the gate in
`.cursor/rules/docs-boundary.mdc`. Writing no document is a valid and frequently correct outcome.

## Rules

| Rule | Scope | Purpose |
|---|---|---|
| [docs-boundary](.cursor/rules/docs-boundary.mdc) | Always on | HARD GATE — whether a doc is allowed, and where it goes |
| [docs-writing-style](.cursor/rules/docs-writing-style.mdc) | `docs/**/*.md` | The voice: conceptual, narrative, trade-offs named |

Rules decide and constrain. They stay short on purpose — a rule that grows into a mini-spec stops
being read.

## Skills

| Skill | Purpose | Entry point |
|---|---|---|
| `documentation-writing` | Classify, write, revise, and review committed documentation | [SKILL.md](.cursor/skills/documentation-writing/SKILL.md) |

Skills teach and carry depth. They load on demand, which is why the conceptual model, the voice,
and the per-type checklists live there rather than in the always-on rule.

## Division of labour

This split is deliberate and worth preserving as the repo grows:

- A **rule** answers *may I, and where* — always loaded, so it must stay cheap
- A **skill** answers *how, and what good looks like* — loaded only when the work starts, so it can
  afford depth

When a rule starts explaining concepts, move the concepts into the skill. When a skill starts
gating behavior, move the gate into the rule.

Inside the skill, the same discipline applies one level down. `SKILL.md` and the rules own taxonomy
and gates; the references own voice and intent. Taxonomy belongs to this repository, voice does
not — which is what makes the reference files portable to a project with a different folder tree.

## Before writing prose

Follow the read order in [SKILL.md](.cursor/skills/documentation-writing/SKILL.md#read-order). Do
not draft from memory: pick the register first, then read the matching document under `docs/` as a
live model, then audit the draft against
[anti-patterns](.cursor/skills/documentation-writing/references/anti-patterns.md) before claiming
the work is done.
