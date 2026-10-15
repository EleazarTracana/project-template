---
name: test-writing
description: >
  Write or revise unit and integration tests in Eleazar's testing philosophy. Repo-agnostic.
  Self-contained — depends on no external rule or convention file.
  Trigger: When writing tests, fixing shallow agent tests, scenario integration fixtures,
  or when the user says "testing voice", "rich assertions", "not how I test", or "second brain
  testing". Does not define repo paths, test project layout, or dotnet filter commands.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "1.0"
---

## When to Use

- Writing new unit or integration tests from scratch
- Revising tests that are too shallow, too brittle, or too framework-heavy
- Building scenario integration fixtures that exercise real boundaries
- Auditing a test suite for assertion poverty, mock overuse, or false confidence
- Deciding what kind of test a behavior actually needs

**Not for:** deciding whether a feature should exist, designing the production code under test,
or configuring CI gates and test runners.

This skill is the whole discipline. It carries its own gate, so it needs no companion rule or
convention file to function.

---

## The One Idea

**Tests are the contract.** They are the executable proof that the code does what the ADR says it
should. A test that cannot fail when the code is wrong is not a test — it is a decoration.

This is why the gate below is hard rather than advisory. A suite full of tests that assert
nothing meaningful accumulates a false sense of safety that is worse than no tests: it actively
misleads the next reader, human or agent, into thinking the behavior is pinned.

**The so-what test.** Every assertion must answer: *what would break in production if this line
were deleted?* If the answer is nothing, delete the assertion. Run it on your own draft before
anyone else has to.

**Bias toward the Theory.** A single test with one magic input is a coincidence. A Theory with
a data row for every boundary is a proof. When the behavior varies with input, reach for
`[Theory]` first and `[Fact]` only when the input space is truly singular.

**Rich assertions over narrow ones.** Assert the shape of the result, not just its existence.
`Assert.NotNull(result)` tells the reader nothing about what was produced. Assert the values,
the state transitions, and the side effects that matter.

**When a test and the code disagree, the test is wrong** — unless the code is the bug.
Tests are descriptive of intent, never a second implementation.

---

## The Gate (HARD)

Run this before writing anything. Answer in order, stop at the first match.

```
Does the behavior already have a test that would fail if it broke?  → Write no test
Is it a pure function of inputs to outputs?                        → Unit test, [Theory] bias
Does it cross a process or network boundary?                      → Scenario integration test
Does it need a real database, filesystem, or broker?                → Platform integration test
Is it testing the framework itself (DI, serialization, routing)?    → Delete; the framework has tests
Is it testing a private method?                                     → Delete; test the public contract
```

**Writing no test is a valid and frequently correct outcome.** It is the most common correct
outcome for trivial passthrough code. Treat the first line of that ladder as the default and
work downward only when it fails.

**Never test the framework.** ASP.NET Core routing, EF Core change tracking, and JSON
serialization already have tests. If your test would fail because Microsoft broke something,
delete it. Your tests exist to prove *your* code, not theirs.

**Never test private methods.** If a private method needs a test, the class is doing too much.
Extract the behavior or test through the public API.

---

## Read Order

Before drafting, read in this order. Do not work from memory.

1. This file — the gate, the taxonomy, the workflow
2. [references/registers.md](references/registers.md) — pick the test type and its register
3. [references/testing-dna.md](references/testing-dna.md) — tone, structure, signature patterns, length
4. [references/anti-patterns.md](references/anti-patterns.md) — the failure modes to avoid
5. The nearest existing test of the same type in the target repo — the live model

---

## Classify Before You Write

| Behavior | Test Type | Path hint | Speed |
|---|---|---|---|
| Pure logic, no I/O | Unit | `{Module}/` or `{Feature}/` | ms |
| Handler or rule with mocked dependencies | Unit | `{Module}/` or `{Feature}/` | ms |
| Full request → response through real pipeline | Scenario integration | `Scenario/{Feature}/` | s |
| Real database or message broker | Platform integration | `Platform/{Feature}/` | s–min |
| Framework wiring, DI, serialization | **none** | Framework tests exist | — |
| Private method | **none** | Refactor to public | — |

Three committed types. Pick one *before* drafting — the type selects the register, and rewriting
register afterward costs more than choosing correctly up front.

**Unit tests** prove that a piece of logic computes the right answer for the right inputs.
They are fast, deterministic, and mock only what is genuinely external.

**Scenario integration tests** prove that a real request flowing through the real pipeline
produces the expected response. They use the full host, real middleware, and real serialization.
They mock only at the true system boundary.

**Platform integration tests** prove that your code works against the real infrastructure it
will run against: the actual database, the actual message broker, the actual file system.
They are the slowest and most valuable.

---

## Per-Type Checklists

The short version below; the section-by-section detail is in
[references/test-taxonomy.md](references/test-taxonomy.md).

**Unit** — Arrange, Act, Assert. One logical assertion per test, but that assertion may be
rich: multiple related values, a collection's shape, a state transition. `[Theory]` over
`[Fact]` when input varies. Mock only what crosses a process boundary.

**Scenario integration** — Spin up the full host. Send a real HTTP request. Assert the full
response: status, body, headers, and side effects. Use the database fixture for state.
Mock only what is genuinely outside the system under test.

**Platform integration** — Use Testcontainers or the real service. Test migrations, query
translation, constraint violations, and round-trip fidelity. Do not mock the database.

---

## Workflow

**Phase 0 — Intake.** Establish intent (new / revise / review / delete), test type and register,
the behavior being pinned, and explicit non-goals. If the intent is "test everything I just
wrote", stop: that is almost always a coverage report, not a test suite.

**Phase 1 — Gate.** Run the ladder above. Stop here when it returns *write no test*, and say
so rather than writing something weaker.

**Phase 2 — Load voice.** Follow the read order above, with the register from Phase 0 selecting
which existing test to read as the model.

**Phase 3 — Outline.** Test names only, plus the one-sentence justification for the type and
the explicit non-goals. Confirm the approach with the requester before writing bodies.

**Phase 4 — Draft.** Follow the DNA for the register. Apply the so-what test per assertion
while drafting, not after. No scope creep beyond intake.

**Phase 5 — Voice audit.** Check the draft against [references/anti-patterns.md](references/anti-patterns.md).
Fix and re-audit. Do not claim the audit passed on the first pass.

---

## Review Heuristics

The failures that actually recur, in review and in self-review:

| Smell | What it means | Fix |
|---|---|---|
| `Assert.NotNull(result)` with nothing else | Assertion poverty | Assert the values that matter |
| `[Fact]` with one magic input | Coincidence testing | Convert to `[Theory]` with boundary rows |
| Mocking the database | Testing EF, not your code | Use a real database or delete |
| Testing private methods via reflection | Class is doing too much | Extract or test the public API |
| `Mock<ILogger>` verifying log calls | Testing the logging framework | Delete, or use a test output helper |
| Setup so complex it needs comments | Over-mocking or wrong test type | Re-read the gate; maybe scenario integration |
| Test name says "should work" | No behavior pinned | Name the actual behavior and expectation |

---

## Adopting This Into a Project

Copy the whole skill folder. Then adjust two things and nothing else:

1. **The paths** in the classification table, if the project's folder tree differs.
2. **The live models** in the target repo — replace them with the project's own tests as they
   accumulate.

The references carry voice and intent, which is repository-agnostic; leave them alone. This file
carries taxonomy and gates, which are repository-specific; that is the part you edit.

---

## Resources

- **Registers — voice by test type**: [references/registers.md](references/registers.md)
- **Testing DNA, with worked example**: [references/testing-dna.md](references/testing-dna.md)
- **Anti-patterns and transformation pairs**: [references/anti-patterns.md](references/anti-patterns.md)
- **Taxonomy and per-type checklists**: [references/test-taxonomy.md](references/test-taxonomy.md)
- **Scenario integration fixtures**: [references/scenario-fixtures.md](references/scenario-fixtures.md)
- **Live models**: every test in the target repo is a reference implementation of its type
