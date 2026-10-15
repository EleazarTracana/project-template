# Anti-patterns

Patterns agents produce that are not this project's voice. Flag and rewrite on sight. Use this
file while drafting **and** when correcting a draft — the transformation pairs show the move.

## Assertion poverty

| Avoid | Why |
|---|---|
| `Assert.NotNull(result)` alone | Tells the reader nothing about what was produced |
| `Assert.True(result)` | A boolean is not a contract; assert the values |
| `Assert.NotEmpty(collection)` alone | Assert the shape: count, contents, order where it matters |
| No assertion at all | The test is a decoration; delete it |

## Mock overuse

| Avoid | Why |
|---|---|
| `Mock<DbContext>` | You are testing EF Core, not your code |
| `Mock<ILogger>` verifying log calls | You are testing the logging framework |
| Mocking a class you own to test another class you own | Integration is the point; use a real instance |
| Setup so complex it needs comments | The mock has become the system under test |

## Framework testing

| Avoid | Where it belongs |
|---|---|
| Testing ASP.NET Core routing | Microsoft has tests |
| Testing JSON serialization defaults | Microsoft has tests |
| Testing DI container resolution | Microsoft has tests |
| Testing AutoMapper mappings | AutoMapper has tests |

## Structural smells

| Avoid | Prefer |
|---|---|
| `[Fact]` with one magic input | `[Theory]` with `[InlineData]` rows for boundaries |
| Test class with 30 tests | Split by scenario or behavior |
| Setup in constructor that every test overrides | Use a fixture or per-test setup |
| `try/catch` in a test to "verify" an exception | `Assert.Throws<T>` |
| `Thread.Sleep` or `Task.Delay` in a test | Use polling with timeout or a deterministic clock |

## Naming smells

| Avoid | Prefer |
|---|---|
| `MethodName_ShouldWork` | `MethodName_WhenCondition_Expectation` |
| `Test1`, `Test2` | Names that describe the behavior |
| Test names that repeat the method name | Test names that describe the scenario |

## Invented taxonomy

Agents coin category labels that sound precise but are not team vocabulary. They read like
internal jargon and then leak into folder names, class names, and conventions — where they are
expensive to remove.

| Avoid, unless the codebase already uses it | Prefer |
|---|---|
| **Fixture** for any class with setup | Only for xUnit `IClassFixture` / `ICollectionFixture` |
| **Helper** for any shared method | Name the behavior: `CreateValidOrder`, `SeedDatabase` |
| **Manager** for a test orchestrator | The domain verb: `OrderProcessor`, `PaymentHandler` |
| **Util** / **Common** for test utilities | Group by concept; `Builders/`, `Data/` with sub-groups |
| **Stub** / **Fake** interchangeably with **Mock** | Use the codebase's own term consistently |

**The test:** would a new hire find this word in production type names or in existing tests?
If not, rewrite it in plain terms. Never coin taxonomy in a test class name to sound
architectural.

## Transformation pairs

Each pair shows the move, not a whole test. The full models are the tests in the target repo.

### Shallow assertion to rich assertion

**Before**

```csharp
[Fact]
public async Task GetOrder_ShouldReturnOrder()
{
    var result = await _service.GetAsync(1);
    Assert.NotNull(result);
}
```

**After**

```csharp
[Theory]
[InlineData(1, "Order-001", OrderStatus.Pending)]
[InlineData(2, "Order-002", OrderStatus.Shipped)]
public async Task GetOrder_WhenOrderExists_ReturnsOrderWithDetails(
    int orderId, string expectedNumber, OrderStatus expectedStatus)
{
    // Arrange
    await SeedOrderAsync(orderId, expectedNumber, expectedStatus);

    // Act
    var result = await _service.GetAsync(orderId);

    // Assert
    Assert.Equal(orderId, result.Id);
    Assert.Equal(expectedNumber, result.Number);
    Assert.Equal(expectedStatus, result.Status);
}
```

### Mock mirror to real dependency

**Before**

```csharp
var mock = new Mock<IOrderService>();
mock.Setup(x => x.Process(It.IsAny<Order>())).ReturnsAsync(new Result { Ok = true });
var controller = new OrderController(mock.Object);

var result = await controller.Post(new OrderDto());

Assert.NotNull(result);
```

**After**

```csharp
// Arrange
var order = new Order { Id = 1, Status = OrderStatus.Draft };
_context.Orders.Add(order);
await _context.SaveChangesAsync();

// Act
var response = await _client.PostAsJsonAsync("/orders", new { id = 1 });

// Assert
Assert.Equal(HttpStatusCode.Created, response.StatusCode);
var saved = await _context.Orders.FindAsync(1);
Assert.Equal(OrderStatus.Pending, saved.Status);
```

### Fact to Theory

**Before**

```csharp
[Fact]
public void CalculateTax_WhenAmountIs100_Returns20()
{
    Assert.Equal(20, Calculator.CalculateTax(100));
}
```

**After**

```csharp
[Theory]
[InlineData(0, 0)]
[InlineData(50, 10)]
[InlineData(100, 20)]
[InlineData(1000, 200)]
public void CalculateTax_WhenAmountIsProvided_ReturnsTwentyPercent(
    decimal amount, decimal expectedTax)
{
    Assert.Equal(expectedTax, Calculator.CalculateTax(amount));
}
```

## Critique protocol

When auditing a draft — your own or someone else's — respond with:

1. Which anti-pattern matched, quoting the test
2. Which existing test in the repo models the fix
3. A rewrite of **only** the failing tests — never expand scope while correcting voice

Do not claim the voice audit passed on the first pass. Fix, then re-audit.
