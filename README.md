# Project Template

A starting point that ships one thing fully formed: a documentation discipline that holds up when
both people and agents are writing in the repository.

## What is here

```
.cursor/
├── skills/
│   └── documentation-writing/
│       ├── SKILL.md              # the gate, the taxonomy, the read order, the workflow
│       └── references/
│           ├── registers.md      # voice by doc type; the two registers, never mixed
│           ├── voice-dna.md      # tone, signature phrases, length targets, worked rewrite
│           ├── anti-patterns.md  # AI tells, invented taxonomy, transformation pairs
│           ├── doc-taxonomy.md   # the four types, with per-type checklists
│           └── adr-format.md     # ADR format, invariant blocks, content boundary
└── rules/
    └── documentation.mdc         # pointer only — "load the skill"; deletable

docs/
├── adl/                          # architecture decision log; each file is one ADR
├── concepts/                     # cross-cutting vocabulary (sparse)
├── domain/{module}/              # current behavior of a bounded context
└── runbooks/{audience}/          # production operations

AGENTS.md                         # skill registry; read first
```

## The idea

Documentation rots because repositories document what code already says. Two sources of truth
drift, and a drifted document is worse than no document — it misleads the next reader, human or
agent.

So the discipline is narrow: **code and tests are the contract**, and a committed document is
allowed only for the residue those cannot express — an accepted decision, cross-cutting meaning,
the current behavior of a bounded context, or an operational path. Four types, a closed taxonomy,
and a gate that runs before anything is written. The gate's most common correct answer is *write
nothing*.

## Why it lives in one skill

Everything is in the skill, and that is deliberate. A discipline split across an always-on rule and
an on-demand skill has to repeat itself at the seam, and the two halves drift — which is the exact
failure the discipline exists to prevent. One source of truth, loaded when the work starts.

The `.cursor/rules/documentation.mdc` pointer is the only concession: it reminds an agent editing
`docs/` that the skill exists. It carries no rules of its own, so there is nothing to drift. Delete
it and the discipline is unaffected.

Inside the skill there is one further split, and it is what makes the voice material portable.
`SKILL.md` owns **taxonomy and gates** — folders, types, limits — which belong to a repository. The
references own **voice and intent** — how prose reads, which phrases earn their keep, which patterns
are AI tells — which belong to nobody in particular. Swap the taxonomy when you adopt this
elsewhere, and keep the voice.

## Using it

1. Copy `.cursor/skills/documentation-writing/` into your repository. That alone is the discipline.
2. Adjust the paths in the skill's gate and classification table if your folder tree differs.
3. Replace the contents of `docs/` with your own, keeping the folder shape.
4. Rename `docs/domain/ordering/` to mirror a real module in your source tree.
5. Optionally copy `.cursor/rules/documentation.mdc` and keep `AGENTS.md` as the registry.

The documentation under `docs/` is demonstrative. It describes a small fictional ordering and
inventory system and exists to show what each type looks like when written correctly — the voice,
the length, where trade-offs go, and what each type deliberately omits. ADR 0001 and the concept
document deliberately cover the same decision, so you can see it rendered once as a frozen record
and once as a living explanation.
