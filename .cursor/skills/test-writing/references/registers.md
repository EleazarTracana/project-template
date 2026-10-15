# Registers

A register is the voice a test speaks in. Pick one in intake, before drafting, and do not mix
two registers inside one test class. Mixing them is the most common reason a test reads wrong
even when every assertion in it passes.

## The three registers

**Unit register.** Fast, deterministic, isolated. The test proves that a piece of logic computes
the right answer. It mocks only what crosses a genuine process boundary — an HTTP client, a
message bus, a clock. It does not mock the database, the file system, or the framework.

**Scenario integration register.** The full pipeline. A real HTTP request enters the host,
flows through real middleware, hits a real handler, and produces a real response. The test
asserts the shape of the world after the request: the status code, the body, the headers, and
the side effects in the database. Mocks appear only at the true system boundary — an external
payment gateway, a third-party API.

**Platform integration register.** The real infrastructure. The test proves that your code works
against the actual database, the actual message broker, the actual file system. It catches
migration errors, query translation failures, and constraint violations that mocks hide.

## Voice by type

| Type | Voice | Mock boundary |
|---|---|---|
| Unit handler/rule | Arrange small, Act once, Assert rich | Only genuine external services |
| Unit pure function | `[Theory]` with `[InlineData]` rows | None |
| Scenario integration | Full host, real request, assert response + state | Third-party APIs only |
| Platform integration | Testcontainers or real service, assert round-trip | None |
| Framework/library test | **Not this register** — delete | — |
| Private method test | **Not this register** — refactor | — |

## Choosing

```
Is it a pure function of inputs to outputs?         → Unit register, [Theory] bias
Does it cross a process or network boundary?        → Scenario integration register
Does it need a real database, filesystem, or broker? → Platform integration register
Does it test the framework itself?                  → Not a test — delete
Does it test a private method?                      → Not a test — refactor
```

If two registers both seem to fit, the test is doing two jobs. Split it.
