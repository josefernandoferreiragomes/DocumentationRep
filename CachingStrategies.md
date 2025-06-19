# Caching Strategies in .NET

A comprehensive tutorial on various caching strategies you can implement in .NET applications. For each strategy, you'll learn how it works, see real-world examples, and understand its benefits and disadvantages.

## Table of Contents

1. Types of cache, that operate at different levels
   
   1.1. [In-Memory Caching (MemoryCache)](#in-memory-caching)

   1.2. [Distributed Caching (Redis)](#distributed-caching)

   1.3. [Response Caching](#response-caching)

2. Caching strategies

   2.1. [Cache-Aside Pattern](#cache-aside-pattern)

   2.2. [Read-Through Cache](#read-through-cache)

   2.3. [Write-Through Cache](#write-through-cache)

   2.4. [Write-Behind (Write-Back) Cache](#write-behind-cache)

---

## 1.1. In-Memory Caching (MemoryCache)

**How it Works:**

* Data is stored in the process memory of your application.
* Uses `MemoryCache` or `IMemoryCache` in ASP.NET Core.
* Items expire based on absolute or sliding expiration.

```csharp
// Configure IMemoryCache in Startup.cs (ASP.NET Core)
services.AddMemoryCache();

// Usage:
public class ProductService
{
    private readonly IMemoryCache _cache;
    public ProductService(IMemoryCache cache)
    {
        _cache = cache;
    }

    public Product GetProduct(int id)
    {
        return _cache.GetOrCreate($"product_{id}", entry =>
        {
            entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(5);
            return GetProductFromDb(id);
        });
    }
}
```

**Real-World Example:**

* Caching configuration settings or user session data in a small-scale web app.

**Benefits:**

* Fast access (in-memory).
* Simple to implement.
* No external dependencies.

**Disadvantages:**

* Limited by application memory and scope (single instance).
* Cache invalidation on application restart.

### Resources

https://learn.microsoft.com/hi-in/dotnet/api/microsoft.extensions.caching.memory.imemorycache?view=net-9.0-pp&viewFallbackFrom=dotnet-plat-ext-5.0

---

## 1.2. Distributed Caching (Redis)

**How it Works:**

* Cache stored in an external distributed system (e.g., Redis).
* Shares cache across multiple application instances.
* Uses `IDistributedCache` interface in ASP.NET Core.

```csharp
// Configure Redis in Startup.cs
services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379";
    options.InstanceName = "MyApp_";
});

// Usage:
public class OrderService
{
    private readonly IDistributedCache _cache;
    public OrderService(IDistributedCache cache)
    {
        _cache = cache;
    }

    public async Task<Order> GetOrderAsync(int id)
    {
        var key = $"order_{id}";
        var cached = await _cache.GetStringAsync(key);
        if (cached != null)
            return JsonSerializer.Deserialize<Order>(cached);

        var order = await GetOrderFromDbAsync(id);
        await _cache.SetStringAsync(key, JsonSerializer.Serialize(order), new DistributedCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(10)
        });
        return order;
    }
}
```

**Real-World Example:**

* Caching product catalogs in a large-scale e-commerce platform.

**Benefits:**

* Scalable across multiple servers.
* Survivable on application restarts.
* Supports high availability and clustering.

**Disadvantages:**

* Network latency compared to in-memory.
* Operational overhead (Redis management).

### Resources

https://learn.microsoft.com/en-gb/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache?view=net-9.0-pp

---

## 1.3. Response Caching

**How it Works:**

* Caches entire HTTP responses at the server or proxy level.
* Uses `[ResponseCache]` attribute in ASP.NET Core MVC or middleware.

```csharp
[ResponseCache(Duration = 60, Location = ResponseCacheLocation.Client)]
public IActionResult Index()
{
    var data = GetDashboardData();
    return View(data);
}
```

**Real-World Example:**

* Caching static content pages (e.g., homepage) to improve load times.

**Benefits:**

* Reduces server processing for identical requests.
* Improves client and proxy-side performance.

**Disadvantages:**

* Less control over granular data caching.
* Stale content risks if not properly invalidated.

### Resources

https://learn.microsoft.com/en-us/aspnet/core/performance/caching/response?view=aspnetcore-9.0
https://learn.microsoft.com/en-us/aspnet/core/performance/caching/middleware?view=aspnetcore-9.0

---

# 2. Caching Strategies

## 2.1. Cache-Aside Pattern

**How it Works:**

* Application code is responsible for reading from and writing to the cache.
* On cache miss, fetch from data store and populate cache.
* On data update, invalidate or update the cache.

```csharp
public async Task<Product> GetProductAsync(int id)
{
    var key = $"prod_{id}";
    var cached = await _cache.GetAsync<Product>(key);
    if (cached != null)
        return cached;

    var product = await _db.GetProductAsync(id);
    await _cache.SetAsync(key, product, TimeSpan.FromMinutes(5));
    return product;
}
```

**Real-World Example:**

* Any scenario where data consistency is critical, like user profiles.

**Benefits:**

* Flexible and deterministic cache usage.
* Data freshness can be managed explicitly.

**Disadvantages:**

* More complex application logic.
* Potential cache stampede on high concurrency misses.

---

## 2.2. Read-Through Cache

**How it Works:**

* Cache abstraction automatically reads from the data store on misses.
* Application interacts only with the cache API.
* Often provided by specialized caching frameworks.

```csharp
// Pseudocode, using a fictional ReadThroughCache library
public class CustomerService
{
    private readonly IReadThroughCache _cache;

    public CustomerService(IReadThroughCache cache)
    {
        _cache = cache;
    }

    public Task<Customer> GetCustomerAsync(int id)
    {
        return _cache.GetAsync(id, () => _db.GetCustomerAsync(id));
    }
}
```

**Real-World Example:**

* API gateways using transparent caching layers.

**Benefits:**

* Simplifies application code.
* Automatic cache population.

**Disadvantages:**

* Requires third-party libraries or custom implementation.
* Less control over when data is fetched or refreshed.

---

## 2.3. Write-Through Cache

**How it Works:**

* Every write operation goes to both cache and database.
* Ensures cache always has the latest data.

```csharp
public async Task UpdateProductAsync(Product p)
{
    await _db.UpdateProductAsync(p);
    await _cache.SetAsync($"product_{p.Id}", p, TimeSpan.FromMinutes(5));
}
```

**Real-World Example:**

* User settings in a social media app, ensuring consistency.

**Benefits:**

* Strong consistency between cache and datastore.
* Simplifies read operations (always fresh).

**Disadvantages:**

* Write latency increases (two writes).
* Higher load on cache on writes.

---

## 2.4. Write-Behind (Write-Back) Cache

**How it Works:**

* Writes are first applied to cache; database update is deferred.
* Cache asynchronously flushes updates to the datastore.

```csharp
public async Task UpdateOrderAsync(Order o)
{
    await _cache.SetAsync($"order_{o.Id}", o, TimeSpan.FromMinutes(10));
    // Background worker flushes changes to DB
}
```

**Real-World Example:**

* Session state storage with eventual persistence.

**Benefits:**

* Fast write operations.
* Reduced database load bursts.

**Disadvantages:**

* Risk of data loss if cache fails.
* Complexity in background synchronization.

---

**Official Resources:**

* **Cache-Aside Pattern**

  * Microsoft documentation: Architecture patterns - Cache-aside pattern
    [https://learn.microsoft.com/azure/architecture/patterns/cache-aside](https://learn.microsoft.com/azure/architecture/patterns/cache-aside)
  * AWS Developer Guide: Caching strategies - Cache-aside
    [https://docs.aws.amazon.com/whitepapers/latest/aws-overview/caching.html#cache-aside-pattern](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/caching.html#cache-aside-pattern)

* **Read-Through Cache**

  * Microsoft article on distributed caching with read-through support:
    [https://learn.microsoft.com/aspnet/core/performance/caching/distributed#read-through-caching](https://learn.microsoft.com/aspnet/core/performance/caching/distributed#read-through-caching)
  * Redis official documentation: Client-Side Caching (Read-Through Cache)
    [https://redis.io/docs/manual/client-side-caching/](https://redis.io/docs/manual/client-side-caching/)

* **Write-Through Cache**

  * Microsoft guide: Write-through caching with Azure Cache for Redis
    [https://learn.microsoft.com/azure/azure-cache-for-redis/cache-design-patterns#write-through](https://learn.microsoft.com/azure/azure-cache-for-redis/cache-design-patterns#write-through)
  * Redis Labs blog: Write-Through and Write-Back Caching Patterns
    [https://redis.com/blog/write-through-write-back-caching/](https://redis.com/blog/write-through-write-back-caching/)

* **Write-Behind (Write-Back) Cache**

  * Microsoft documentation: Architecture patterns - Write-behind cache
    [https://learn.microsoft.com/azure/architecture/patterns/write-behind-cache](https://learn.microsoft.com/azure/architecture/patterns/write-behind-cache)
  * NCache documentation: Write-behind caching
    [https://www.alachisoft.com/resources/docs/ncache/design/write-behind-caching.html](https://www.alachisoft.com/resources/docs/ncache/design/write-behind-caching.html)


## Conclusion

Choosing the right caching strategy depends on your application’s scale, consistency requirements, and infrastructure. Combine strategies (e.g., in-memory + distributed) for optimal performance. Always monitor cache performance and hit rates, and plan an invalidation strategy to avoid stale data.
