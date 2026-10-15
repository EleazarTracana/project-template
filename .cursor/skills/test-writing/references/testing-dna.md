# Testing DNA

How tests in this repository are written. Match this voice, not generic test prose.

## Tone

Direct and honest. A test is a promise: *if this passes, the behavior is pinned.* Do not write
tests that make promises they cannot keep. If a test cannot fail when the code is wrong, delete
it. If a test only exists to make a coverage number go up, delete it.

## Structure by type

### Unit test

1. **Arrange** — set up the smallest possible input. No builders that need their own tests.
2. **Act** — invoke the method once. One action, one responsibility.
3. **Assert** — assert the shape of the result. Multiple related values are fine when they
   describe one logical outcome.

Test names describe the behavior, not the method:
- `CalculateTotal_WhenCartIsEmpty_ReturnsZero`
- `ProcessPayment_WhenGatewayDeclines_ThrowsPaymentException`
- `ValidateInvoice_WhenAmountExceedsThreshold_FlagsForReview`

### Scenario integration test

1. **Arrange** — seed the database fixture with the minimum state the scenario needs.
2. **Act** — send a real HTTP request through the full host.
3. **Assert** — assert the response status, the response body, and the database state after.

Test names describe the scenario, not the endpoint:
- `CreateOrder_WhenInventoryIsAvailable_ReturnsCreatedAndReservesStock`
- `GetOrder_WhenOrderDoesNotExist_ReturnsNotFound`
- `CancelOrder_WhenAlreadyShipped_ReturnsConflict`

### Platform integration test

1. **Arrange** — spin up the container or connect to the real service.
2. **Act** — execute the operation against real infrastructure.
3. **Assert** — assert the round-trip: what was written, what was read back, what constraints
   held.

Test names describe the infrastructure behavior:
- `SaveOrder_WhenDatabaseConstraintViolated_ThrowsDbUpdateException`
- `PublishEvent_WhenBrokerIsUnavailable_RetriesAndEventuallySucceeds`

## Signature patterns

Use when they are true. They tell the reader what kind of test is coming, which is work; used
as decoration they become noise.

| Pattern | When |
|---|---|
| `[Theory]` with `[InlineData]` | Input varies and boundaries matter |
| `[Fact]` | Input space is genuinely singular |
| `Assert.Equal(expected, actual)` | The value is the contract |
| `Assert.Contains(collection, predicate)` | The shape matters, not the order |
| `Assert.Throws<T>` | The exception is the contract |
| `await _fixture.SendAsync(request)` | Scenario integration — real request |
| `await _fixture.Database.ExecuteSqlAsync(...)` | Platform integration — real state |

## Prose versus assertions

Prefer a single rich assertion over many narrow ones. When a test needs more than one
`Assert`, ask whether they describe one logical outcome or two. If two, split the test.

```csharp
// Weak — three assertions, three promises, one test
Assert.NotNull(result);
Assert.True(result.IsSuccess);
Assert.Equal(200, result.StatusCode);

// Strong — one assertion, one promise
Assert.Equal(new { IsSuccess = true, StatusCode = 200 }, result);
```

## Length

| Element | Target |
|---|---|
| Test method | 10–30 lines; hard stop 50 |
| Arrange block | 5 lines ideal; if more, the fixture is doing too much |
| Assert block | 1–5 lines; if more, split the test |
| Test class | One behavior per class; if a class has >20 tests, split by scenario |

Length spent on assertions is the only length that earns its place. If no assertion is worth
writing, that is a strong signal the behavior is not worth pinning — or that the code is
trivial passthrough that needs no test.

## Words

**Use:** behavior, contract, boundary, pin, prove, fail, rich, shallow, Theory, Fact,
integration, unit, scenario, platform.

**Avoid:** see [anti-patterns.md](anti-patterns.md).

---

## Worked example

The move from shallow to rich, on a real behavior.

### Before — avoid

Coincidence testing, assertion poverty, and a mock that proves nothing:

```csharp
[Fact]
public async Task ProcessOrder_ShouldWork()
{
    var mockRepo = new Mock<IOrderRepository>();
    mockRepo.Setup(x => x.Save(It.IsAny<Order>())).ReturnsAsync(true);
    var service = new OrderService(mockRepo.Object);

    var result = await service.Process(new Order { Id = 1 });

    Assert.NotNull(result);
    Assert.True(result);
}
```

The test passes. It also proves nothing. It does not assert what was saved, what the result
contains, or what happens when the repository fails. The mock is a mirror: it reflects the
code back at itself and calls it a test.

### After — target

```csharp
[Theory]
[InlineData(1, OrderStatus.Pending)]
[InlineData(999, OrderStatus.Pending)]
public async Task ProcessOrder_WhenOrderIsValid_SavesAndReturnsConfirmation(
    int orderId, OrderStatus expectedStatus)
{
    // Arrange
    var order = new Order { Id = orderId, Status = OrderStatus.Draft };
    _repository.Orders.Add(order);
    await _repository.SaveChangesAsync();

    // Act
    var result = await _service.ProcessAsync(orderId);

    // Assert
    Assert.NotNull(result);
    Assert.Equal(orderId, result.OrderId);
    Assert.Equal(expectedStatus, result.Status);

    var saved = await _repository.Orders.FindAsync(orderId);
    Assert.Equal(expectedStatus, saved.Status);
}
```

### What changed

**The assertion became rich.** Not `Assert.True(result)` but the actual values that matter:
the ID, the status, and the persisted state. The reader can now disagree on the merits, which
is what makes a test trustworthy.

**The test became a Theory.** One magic input became two boundary rows. The next developer
who adds a boundary — a maximum order ID, a special status — adds a row, not a new test.

**The mock became real.** The repository is an in-memory EF Core context, not a `Mock<>`.
The test now proves that the service and the database agree on what "processed" means.

**The name became a contract.** `ProcessOrder_ShouldWork` says nothing.
`ProcessOrder_WhenOrderIsValid_SavesAndReturnsConfirmation` says exactly what is pinned.
