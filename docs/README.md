# Documentation

Four committed types live here, and nothing else. Code and tests carry the contract; these
documents carry only what code and tests cannot express.

`adl` is the architecture decision log; each file in it is one ADR.

| Folder | Type | Answers | Mutable |
|---|---|---|---|
| `adl/` | ADR | Why is the boundary here? | No, once accepted |
| `concepts/` | Concept | What does this word mean, and how does the mechanism work? | Yes |
| `domain/{module}/` | Domain README | How does this bounded context behave today? | Yes |
| `runbooks/{audience}/` | Runbook | What do I run right now? | Yes |

Folder names under `domain/` mirror the source module names. Nothing nests more than four levels
deep from here.

## Before adding anything

Load the `documentation-writing` skill and run its gate. Writing no document is a valid and
frequently correct outcome — plans, roadmaps, status updates, and field tables all belong somewhere
else.

The skill is self-contained: the gate, the taxonomy, the voice, and the per-type checklists all
live inside it.

## About the content in this template

Every document under `docs/` is demonstrative. It describes a small fictional ordering and
inventory system, and exists to show what each type looks like when written correctly — the voice,
the length, where the trade-offs go, and what each type deliberately leaves out.

Replace it with your own. Keep the shape.
