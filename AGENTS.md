# Agent Instructions

This repository carries a documentation discipline. Read this file before writing any markdown.

## The invariant

**Code and tests are the contract.** A committed document is allowed only for what they cannot
express. Before adding or extending any document, run the gate in the `documentation-writing`
skill. Writing no document is a valid and frequently correct outcome — in fact the most common one.

## Skill

| Skill | Purpose | Entry point |
|---|---|---|
| `documentation-writing` | The whole discipline: the gate, the taxonomy, the register, the voice, the checklists | [SKILL.md](.cursor/skills/documentation-writing/SKILL.md) |

The skill is **self-contained**. It depends on no rule, convention file, or external vault, which is
what makes it portable: copy the folder into any project, adjust the paths in its gate, and the
discipline comes with it.

## Before writing prose

Follow the read order in [SKILL.md](.cursor/skills/documentation-writing/SKILL.md#read-order). Do
not draft from memory: run the gate, pick the register, read the matching document under `docs/` as
a live model, then audit the draft against
[anti-patterns](.cursor/skills/documentation-writing/references/anti-patterns.md) before claiming
the work is done.

## Internal division of labour

Worth preserving as the repository grows, because it is what keeps the skill portable:

- `SKILL.md` owns **taxonomy and gates** — where files live, what types exist, what is forbidden.
  This is specific to a repository, and it is the part you edit when adopting the skill elsewhere.
- `references/` own **voice and intent** — how prose reads, which phrases earn their keep, which
  patterns are AI tells. This is repository-agnostic; leave it alone.

When a reference starts naming folders, the boundary has slipped. When `SKILL.md` starts teaching
tone, it has slipped the other way.
