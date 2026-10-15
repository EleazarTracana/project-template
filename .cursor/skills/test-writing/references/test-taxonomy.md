# Test Taxonomy

Three committed types, each with a different job and a different bar. This file carries the
detail the skill summarizes.

All three speak the narrative register of proving behavior. Framework testing and private
method testing are not types — they are anti-patterns.

## Why exactly three

The taxonomy is closed on purpose. Each type answers one question that the others cannot:

- **Unit** — *does this piece of logic compute the right answer?* Answered in milliseconds.
- **Scenario integration** — *does a real request through the real pipeline produce the right
  response?* Answered in seconds.
- **Platform integration** — *does this code work against the real infrastructure it will run
  against?* Answered in seconds to minutes.

A test that does not answer one of those three questions does not belong in the suite. The key
insight is that a closed taxonomy is what keeps the suite navigable — a developer who knows the
three types can find anything, and an agent that knows the three types cannot invent a fourth
folder.

---

## Unit

**Path** mirrors the module under test: `{Module}/` or `{Feature}/`.
**Speed** milliseconds.
**Isolation** no shared state between tests.

### Sections

- **Arrange** — the smallest possible input. No builders that need their own tests.
- **Act** — one invocation. One action, one responsibility.
- **Assert** — the shape of the result. Rich, not shallow.

### Checklist

- [ ] The test would fail if the behavior changed
- [ ] `[Theory]` with `[InlineData]` when input varies
- [ ] `[Fact]` only when the input space is singular
- [ ] No mocks of the database, file system, or framework
- [ ] No testing of private methods via reflection
- [ ] The name describes the behavior, not the method

---

## Scenario Integration

**Path** `Scenario/{Feature}/` or `Integration/{Feature}/`.
**Speed** seconds.
**Isolation** a shared host fixture; tests may share the host but not the database state.

### Sections

- **Arrange** — seed the database fixture with the minimum state the scenario needs.
- **Act** — send a real HTTP request through the full host.
- **Assert** — assert the response status, the response body, and the database state after.

### Checklist

- [ ] Uses `WebApplicationFactory<T>` or the project's equivalent
- [ ] Mocks only at the true system boundary (third-party APIs)
- [ ] Asserts the full response: status, body, headers
- [ ] Asserts side effects in the database
- [ ] No `Mock<DbContext>` — use the fixture's real context
- [ ] The name describes the scenario, not the endpoint

---

## Platform Integration

**Path** `Platform/{Feature}/` or `Infrastructure/{Feature}/`.
**Speed** seconds to minutes.
**Isolation** per-test or per-class container, depending on the fixture strategy.

### Sections

- **Arrange** — spin up the container or connect to the real service.
- **Act** — execute the operation against real infrastructure.
- **Assert** — assert the round-trip: what was written, what was read back, what constraints
  held.

### Checklist

- [ ] Uses Testcontainers or the project's real infrastructure
- [ ] Tests migrations, query translation, and constraint violations
- [ ] Tests round-trip fidelity: write, read, compare
- [ ] No mocking of the infrastructure under test
- [ ] The name describes the infrastructure behavior, not the method

---

## Where removed content goes

| Content | Destination |
|---|---|
| Framework wiring tests | Delete — the framework has tests |
| Private method tests | Refactor to public, or delete |
| Coverage-driven tests with no assertions | Delete |
| Tests that duplicate the scenario integration suite | Delete, or move to the faster register |
| Performance benchmarks | A separate benchmark project |
| Manual test scripts | The tracker or a runbook — not the test suite |
