# Project Template

A starting point that ships one thing fully formed: a documentation discipline that holds up when
both people and agents are writing in the repo.

## What is here

```
.cursor/
├── rules/
│   ├── docs-boundary.mdc        # HARD GATE — may I write this, and where does it go
│   └── docs-writing-style.mdc   # the voice, scoped to docs/**/*.md
└── skills/
    └── documentation-writing/
        ├── SKILL.md              # the gate, the taxonomy, the read order, the workflow
        └── references/
            ├── registers.md      # voice by doc type; the two registers, never mixed
            ├── voice-dna.md      # tone, signature phrases, length targets, worked rewrite
            ├── anti-patterns.md  # AI tells, invented taxonomy, transformation pairs
            ├── doc-taxonomy.md   # the four types, with per-type checklists
            └── adr-format.md     # ADR format, invariant blocks, content boundary

docs/
├── adl/                         # ADRs — immutable why
├── concepts/                    # cross-cutting vocabulary (sparse)
├── domain/{module}/             # current behavior of a bounded context
└── runbooks/{audience}/         # production operations

AGENTS.md                        # rule and skill registry; read first
```

## The idea

Documentation rots because repositories document what code already says. Two sources of truth
drift, and a drifted document is worse than no document — it misleads the next reader, human or
agent.

So the rule here is narrow: **code and tests are the contract**, and a committed document is
allowed only for the residue those cannot express — an accepted decision, cross-cutting meaning,
the current behavior of a bounded context, or an operational path. Four types, a closed taxonomy, a
gate that runs before anything is written.

The separation between the rule and the skill is the part worth copying. The rule is always loaded,
so it stays mechanical and cheap: a decision ladder and a reject list, nothing more. The skill
loads only when someone actually writes, so it carries the depth — why the gate exists, what each
type is for, the voice, the checklists. Mixing the two gives you an always-on rule nobody finishes
reading.

Inside the skill there is a second separation, and it is the one that makes the voice material
portable. `SKILL.md` and the rules own **taxonomy and gates** — where files live, what types exist,
what is forbidden. The references own **voice and intent** — how prose reads, which phrases earn
their keep, which patterns are AI tells. Taxonomy is specific to a repository; voice is not. Swap
the taxonomy when you adopt this into a project with a different folder tree, and keep the voice.

## Using it

1. Clone or copy `.cursor/` into your repo.
2. Replace the contents of `docs/` with your own, keeping the folder shape.
3. Rename `docs/domain/ordering/` to mirror a real module in your source tree.
4. Keep `AGENTS.md` and register additional rules and skills there as you add them.

The documentation in `docs/` is demonstrative. It describes a small fictional ordering and
inventory system and exists to show what each type looks like when written correctly — the voice,
the length, where trade-offs go, and what each type deliberately omits. ADR 0001 and the concept
doc are deliberately paired so you can see the same decision rendered once as a frozen record and
once as a living explanation.
