# Anti-patterns

Patterns agents produce that are not this project's voice. Flag and rewrite on sight. Use this file
while drafting **and** when correcting a draft — the transformation pairs show the move.

## Openers and hedging

| Avoid | Why |
|---|---|
| Architecturally, | Performs expertise; skip to the question |
| Simply / Obviously / Basically / Essentially | Condescending filler |
| It's worth noting that… | Throat-clearing |
| In today's fast-paced… | Generic opener |
| Delve / Leverage / Utilize / Seamlessly | Marketing register |

## Voice smells

| Avoid | Prefer |
|---|---|
| Metaphor-heavy abstraction ("reading port", "deliberate seam") | Concrete architecture terms |
| Stacked "X, not Y" or "isn't X — it's Y" contrasts | Plain causal explanation |
| Mirror naming ("the total calculator calculates the total") | Delete, or explain the contract |
| Parameter and type narration in prose | Point to the code, or describe the contract |
| Hiding trade-offs | Name the cost explicitly |
| "since" used causally | **because** — link effect to cause |

## Content smells

| Avoid | Where it belongs |
|---|---|
| Paths, type names, registration snippets in an **ADR body** | A rule, a living doc, or code |
| Sequence diagrams and service lists as **primary ADR content** | Implementation notes — not `docs/` |
| Formula bullet tables posing as **architecture rationale** | Reference contract, collocated with code |
| Implementation checklists in a committed doc | Tracker, chat, or tests |
| Per-component markdown | Code metadata plus an authoring rule |
| Past-tense migration story in a living doc | An ADR, and only an ADR |
| Session TODOs, "currently", "I think", "need to verify" | Transitional — never committed |
| Multi-page *Alternatives rejected* essays | Short *Why not X?* subsections |

## Invented taxonomy

Agents coin category labels that sound precise but are not team vocabulary. They read like internal
jargon and then leak into folder names, ADR titles, and rules — where they are expensive to remove.

| Avoid, unless the codebase already uses it | Prefer |
|---|---|
| **Catalog** as a catch-all for "list of things" | Name the concept, or use the module's existing term |
| **Bag** / **grab bag** / **misc** | Group by concept; `Shared/` with sub-groups if the grouping is honest |
| **Port** / **seam** as metaphor | Query interface, handler boundary, module API |
| **Orchestrator** when one service method would do | The domain verb; reserve it for real multi-step coordination |
| **Pipeline** for any sequence of calls | Only when the codebase has an actual pipeline abstraction |
| **Slice** in reader-facing prose without defining it | Vertical feature folder, handler module — or drop the label |

**The test:** would a new hire find this word in production type names or in existing docs? If not,
rewrite it in plain terms. Never coin taxonomy in an ADR title or a README opening to sound
architectural.

## Transformation pairs

Each pair shows the move, not a whole document. The full models are the documents under `docs/`.

### ADR opening — template to narrative

**Before**

```markdown
## Context
We need to decide X.

## Decision
We chose Y.

## Rationale
- Reason 1
- Reason 2
```

**After**

```markdown
The natural question: should it be X or Y?

We chose **Y**. The key insight: [one sentence]. [Two sentences on because, and on what the
rejected path would have cost.]

The trade-off: [cost]. That discipline is the price of [benefit].
```

### Rejected alternatives — essay to subsection

**Before:** three paragraphs steelmanning each rejected option, with its own sub-rationale.

**After:** a *Why not X?* subsection of two or three sentences — the virtue it had, and the one
reason it lost. A reader who wants the full argument can reopen the discussion; a reader scanning
the ADR needs to know the option was considered and why it failed.

### Domain README opening — metaphor to concrete

**Before:** "A reservation is the module's reading port — the deliberate seam where stock becomes
intention." Plus a source file reference.

**After:** open with the capability in plain terms: what it owns, who owns the contract, what the
trade-off is. No file references in a README introduction.

### ADR versus implementation map

**Before:** a sequence diagram plus a step / class / method table, as the body of an "ADR".

**After:** the question, the decision in prose with **because**, and the explicit cost. The diagram
and the table were never ADR content.

### Living doc — narration to purpose

**Before**

```markdown
The invoice processor class takes a repository and a logger. Its process method loops
through invoices and calls validate on each one...
```

**After**

```markdown
Validates and persists incoming vendor invoices, verifying amounts against purchase
orders before anything is written.

Key responsibilities:
- Cross-reference invoice amounts with purchase-order records
- Flag discrepancies above threshold for manual review
- Generate accrual entries at period-end cutoff
```

### Causal wording — since to because

**Before:** "Since availability accuracy matters, we reserve at checkout."

**After:** "We reserve at checkout **because** a cart is a browsing artifact — and holding stock on
cart entry locks up exactly the items under the most genuine demand."

## Critique protocol

When auditing a draft — your own or someone else's — respond with:

1. Which anti-pattern matched, quoting the sentence
2. Which document under `docs/` models the fix
3. A rewrite of **only** the failing sections — never expand scope while correcting voice

Do not claim the voice audit passed on the first pass. Fix, then re-audit.
