# Caching strategies 

🔁 Caching Patterns

1. Cache-Aside (Lazy Loading)

✅ Common, flexible.

❌ Risk of cache-miss penalty.


> App loads from DB, then updates cache.

---

2. Read-Through Cache

Cache acts as a proxy to the underlying data store.

The application only talks to the cache.

Cache fetches data if not present.


> Typically requires a smart cache layer, like a Redis module or a service with built-in DB access logic.



In .NET, you’d implement this via a wrapper/service layer.


---

3. Write-Through Cache

Every write goes through the cache, and the cache is responsible for writing to the DB.

Keeps cache and DB always in sync.


> Useful for high consistency but adds latency on writes.

---

4. Write-Behind (Write-Back) Cache

Writes go to the cache first, and the cache asynchronously writes to the DB.

Very fast for the user, but risks data loss on cache failure.


> Rarely used unless performance is critical and some data loss is tolerable.


---

5. Cache Invalidation Strategies

These are not patterns themselves, but critical to every pattern:

Time-based (TTL)

Event-based (on writes/deletes)

Manual (explicit RemoveAsync)



---

🧱 

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379";
});
builder.Services.AddSingleton<ProductService>();
builder.Services.AddSingleton<RedisCacheService>();
builder.Services.AddLogging();

var app = builder.Build();

app.UseSwagger();
app.UseSwaggerUI();

app.MapGet("/products/{id}", async (int id, ProductService service) =>
{
    var product = await service.GetProductAsync(id);
    return product is not null ? Results.Ok(product) : Results.NotFound();
});

app.Run();

// Models/Product.cs
public record Product(int Id, string Name, decimal Price);

// Services/ProductService.cs
public class ProductService
{
    private readonly RedisCacheService _cache;
    private readonly ILogger<ProductService> _logger;
    private readonly Dictionary<int, Product> _db = new()
    {
        [1] = new Product(1, "Laptop", 1299.99m),
        [2] = new Product(2, "Smartphone", 699.49m),
        [3] = new Product(3, "Headphones", 199.95m)
    };

    public ProductService(RedisCacheService cache, ILogger<ProductService> logger)
    {
        _cache = cache;
        _logger = logger;
    }

    public async Task<Product?> GetProductAsync(int id)
    {
        string key = $"product:{id}";

        var cached = await _cache.GetAsync<Product>(key);
        if (cached is not null)
        {
            _logger.LogInformation("Cache hit for key: {CacheKey}", key);
            return cached;
        }

        _logger.LogInformation("Cache miss for key: {CacheKey}", key);
        _db.TryGetValue(id, out var product);

        if (product is not null)
            await _cache.SetAsync(key, product, TimeSpan.FromMinutes(10));

        return product;
    }
}

// Services/RedisCacheService.cs
using Microsoft.Extensions.Caching.Distributed;
using Microsoft.Extensions.Logging;
using System.Text.Json;

public class RedisCacheService
{
    private readonly IDistributedCache _cache;

    public RedisCacheService(IDistributedCache cache)
    {
        _cache = cache;
    }

    public async Task<T?> GetAsync<T>(string key)
    {
        var cachedData = await _cache.GetStringAsync(key);
        return cachedData is null ? default : JsonSerializer.Deserialize<T>(cachedData);
    }

    public async Task SetAsync<T>(string key, T data, TimeSpan expiration)
    {
        var jsonData = JsonSerializer.Serialize(data);
        await _cache.SetStringAsync(key, jsonData, new DistributedCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = expiration
        });
    }
}


A ProductService that attempts to load a product from Redis cache first.

If not found, it fetches from a simulated in-memory DB and then caches the result.

A RedisCacheService wrapper for JSON serialization/deserialization.

A minimal API endpoint /products/{id}.

```

## Sources

https://antondevtips.com/blog/how-to-implement-caching-strategies-in-dotnet
