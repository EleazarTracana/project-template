# Scenario Integration Fixtures

How to build the shared host and database fixtures that scenario integration tests depend on.

## The host fixture

Use `WebApplicationFactory<TEntryPoint>` as the base. Customize it to replace only what is
genuinely external:

```csharp
public class ScenarioFixture : WebApplicationFactory<Program>
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.ConfigureServices(services =>
        {
            // Replace only true external boundaries
            services.RemoveAll<IPaymentGateway>();
            services.AddSingleton<IPaymentGateway, StubPaymentGateway>();

            // Keep the real database — scenario tests need real state
        });
    }
}
```

**Do not** replace the database with an in-memory provider unless the project explicitly
accepts the loss of migration and constraint testing. The in-memory provider is not the
database; it is a different database with different behavior.

## The database fixture

Use a shared database with respawn or transaction rollback between tests. The goal is that
each test starts with a known, empty state without paying the migration cost per test.

```csharp
public class DatabaseFixture : IAsyncLifetime
{
    public string ConnectionString { get; private set; }

    public async Task InitializeAsync()
    {
        // Start the container or connect to the test database
        // Run migrations once
    }

    public async Task ResetAsync()
    {
        // Respawn or rollback — the test's starting state is empty
    }

    public async Task DisposeAsync()
    {
        // Stop the container or clean up
    }
}
```

## Collection fixtures

Use `ICollectionFixture<T>` to share the host and database across a test class. Use
`IClassFixture<T>` when the fixture is cheap enough to build per class.

```csharp
[Collection("Scenario")]
public class CreateOrderTests : IClassFixture<ScenarioFixture>
{
    private readonly ScenarioFixture _fixture;

    public CreateOrderTests(ScenarioFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public async Task CreateOrder_WhenInventoryIsAvailable_ReturnsCreatedAndReservesStock()
    {
        // Arrange
        await _fixture.ResetDatabaseAsync();
        var product = await _fixture.SeedProductAsync(stock: 10);

        // Act
        var response = await _fixture.Client.PostAsJsonAsync("/orders", new
        {
            productId = product.Id,
            quantity = 1
        });

        // Assert
        Assert.Equal(HttpStatusCode.Created, response.StatusCode);

        var updated = await _fixture.GetProductAsync(product.Id);
        Assert.Equal(9, updated.Stock);
    }
}
```

## Seed helpers

Keep seed helpers small and named for the scenario, not the entity:

| Avoid | Prefer |
|---|---|
| `CreateOrder()` | `CreatePendingOrder()` |
| `CreateUser()` | `CreateUserWithRole(Role.Admin)` |
| `SeedDatabase()` | `SeedCatalogWithProducts(params Product[])` |

## What not to fixture

- **Do not** fixture the clock unless the project has a deterministic clock abstraction.
- **Do not** fixture the file system unless the scenario is specifically about file I/O.
- **Do not** fixture every service in the container — only the true external boundaries.
