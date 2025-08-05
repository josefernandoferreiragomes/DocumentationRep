# Parallel.ForEachAsync Concurrency Issues Demo - Step by Step Guide

This guide demonstrates common concurrency problems in `Parallel.ForEachAsync` and how to solve them using locks and other synchronization primitives.

## Step 1: Create the Project Structure

### 1.1 Create New .NET 8 Minimal API Project
```bash
dotnet new webapi -n ConcurrencyDemo -minimal
cd ConcurrencyDemo
```

### 1.2 Add Required NuGet Packages
```bash
dotnet add package System.Threading.Tasks.Extensions
```

## Step 2: Create the Problematic Code (Race Condition Demo)

### 2.1 Create Models and Services

First, create a `Models` folder and add these classes:

**Models/ApiResponse.cs**
```csharp
namespace ConcurrencyDemo.Models;

public record ApiResponse(int Id, string Data, DateTime ProcessedAt);

public record ProcessingResult
{
    public List<ApiResponse> Responses { get; set; } = new();
    public int TotalProcessed { get; set; }
    public Dictionary<string, int> StatusCounts { get; set; } = new();
    public List<string> Errors { get; set; } = new();
}
```

**Services/DataProcessor.cs**
```csharp
namespace ConcurrencyDemo.Services;

using ConcurrencyDemo.Models;

public class DataProcessor
{
    private readonly HttpClient _httpClient;
    private readonly ILogger<DataProcessor> _logger;
    
    // These will demonstrate race conditions
    private int _totalProcessed = 0;
    private readonly Dictionary<string, int> _statusCounts = new();
    private readonly List<string> _errors = new();
    private readonly List<ApiResponse> _responses = new();

    public DataProcessor(HttpClient httpClient, ILogger<DataProcessor> logger)
    {
        _httpClient = httpClient;
        _logger = logger;
    }

    // PROBLEMATIC METHOD - Contains race conditions
    public async Task<ProcessingResult> ProcessDataUnsafeAsync(IEnumerable<int> ids)
    {
        _logger.LogInformation("Starting UNSAFE parallel processing of {Count} items", ids.Count());
        
        var startTime = DateTime.UtcNow;
        
        await Parallel.ForEachAsync(ids, new ParallelOptions
        {
            MaxDegreeOfParallelism = 10
        }, async (id, ct) =>
        {
            try
            {
                // Simulate API call
                await SimulateApiCallAsync(id);
                
                // RACE CONDITION 1: Incrementing shared counter
                _totalProcessed++;
                
                // RACE CONDITION 2: Adding to shared collection
                _responses.Add(new ApiResponse(id, $"Data for {id}", DateTime.UtcNow));
                
                // RACE CONDITION 3: Updating shared dictionary
                var status = id % 3 == 0 ? "success" : "partial";
                if (_statusCounts.ContainsKey(status))
                    _statusCounts[status]++;
                else
                    _statusCounts[status] = 1;
                
                _logger.LogDebug("Processed item {Id}", id);
            }
            catch (Exception ex)
            {
                // RACE CONDITION 4: Adding to shared error collection
                _errors.Add($"Error processing {id}: {ex.Message}");
                _logger.LogError(ex, "Error processing item {Id}", id);
            }
        });

        var result = new ProcessingResult
        {
            Responses = _responses.ToList(),
            TotalProcessed = _totalProcessed,
            StatusCounts = _statusCounts.ToDictionary(kvp => kvp.Key, kvp => kvp.Value),
            Errors = _errors.ToList()
        };

        _logger.LogInformation("UNSAFE processing completed in {Duration}ms. Expected: {Expected}, Actual: {Actual}", 
            (DateTime.UtcNow - startTime).TotalMilliseconds, ids.Count(), _totalProcessed);

        return result;
    }

    private async Task SimulateApiCallAsync(int id)
    {
        // Simulate network delay
        await Task.Delay(Random.Shared.Next(50, 200));
        
        // Simulate occasional failures
        if (id % 50 == 0)
            throw new InvalidOperationException($"Simulated failure for ID {id}");
    }
}
```

## Step 3: Create Thread-Safe Version

### 3.1 Add Thread-Safe DataProcessor Methods

Add these methods to the `DataProcessor` class:

```csharp
// Thread-safe fields using concurrent collections
private readonly object _lockObject = new();
private int _safeProcessedCount = 0;
private readonly ConcurrentBag<ApiResponse> _safeResponses = new();
private readonly ConcurrentDictionary<string, int> _safeStatusCounts = new();
private readonly ConcurrentBag<string> _safeErrors = new();

// SAFE METHOD - Using locks and concurrent collections
public async Task<ProcessingResult> ProcessDataSafeAsync(IEnumerable<int> ids)
{
    _logger.LogInformation("Starting SAFE parallel processing of {Count} items", ids.Count());
    
    var startTime = DateTime.UtcNow;
    
    await Parallel.ForEachAsync(ids, new ParallelOptions
    {
        MaxDegreeOfParallelism = 10
    }, async (id, ct) =>
    {
        try
        {
            await SimulateApiCallAsync(id);
            
            // SOLUTION 1: Thread-safe increment using Interlocked
            Interlocked.Increment(ref _safeProcessedCount);
            
            // SOLUTION 2: Thread-safe collection (ConcurrentBag)
            _safeResponses.Add(new ApiResponse(id, $"Data for {id}", DateTime.UtcNow));
            
            // SOLUTION 3: Thread-safe dictionary (ConcurrentDictionary)
            var status = id % 3 == 0 ? "success" : "partial";
            _safeStatusCounts.AddOrUpdate(status, 1, (key, oldValue) => oldValue + 1);
            
            _logger.LogDebug("Safely processed item {Id}", id);
        }
        catch (Exception ex)
        {
            // SOLUTION 4: Thread-safe error collection
            _safeErrors.Add($"Error processing {id}: {ex.Message}");
            _logger.LogError(ex, "Error processing item {Id}", id);
        }
    });

    var result = new ProcessingResult
    {
        Responses = _safeResponses.ToList(),
        TotalProcessed = _safeProcessedCount,
        StatusCounts = _safeStatusCounts.ToDictionary(kvp => kvp.Key, kvp => kvp.Value),
        Errors = _safeErrors.ToList()
    };

    _logger.LogInformation("SAFE processing completed in {Duration}ms. Expected: {Expected}, Actual: {Actual}", 
        (DateTime.UtcNow - startTime).TotalMilliseconds, ids.Count(), _safeProcessedCount);

    return result;
}

// ALTERNATIVE SAFE METHOD - Using traditional locks
public async Task<ProcessingResult> ProcessDataWithLocksAsync(IEnumerable<int> ids)
{
    _logger.LogInformation("Starting LOCK-BASED parallel processing of {Count} items", ids.Count());
    
    var startTime = DateTime.UtcNow;
    var lockProcessedCount = 0;
    var lockResponses = new List<ApiResponse>();
    var lockStatusCounts = new Dictionary<string, int>();
    var lockErrors = new List<string>();

    await Parallel.ForEachAsync(ids, new ParallelOptions
    {
        MaxDegreeOfParallelism = 10
    }, async (id, ct) =>
    {
        try
        {
            await SimulateApiCallAsync(id);
            
            // SOLUTION: Using lock for all shared state modifications
            lock (_lockObject)
            {
                lockProcessedCount++;
                lockResponses.Add(new ApiResponse(id, $"Data for {id}", DateTime.UtcNow));
                
                var status = id % 3 == 0 ? "success" : "partial";
                if (lockStatusCounts.ContainsKey(status))
                    lockStatusCounts[status]++;
                else
                    lockStatusCounts[status] = 1;
            }
            
            _logger.LogDebug("Lock-processed item {Id}", id);
        }
        catch (Exception ex)
        {
            lock (_lockObject)
            {
                lockErrors.Add($"Error processing {id}: {ex.Message}");
            }
            _logger.LogError(ex, "Error processing item {Id}", id);
        }
    });

    var result = new ProcessingResult
    {
        Responses = lockResponses,
        TotalProcessed = lockProcessedCount,
        StatusCounts = lockStatusCounts,
        Errors = lockErrors
    };

    _logger.LogInformation("LOCK-BASED processing completed in {Duration}ms. Expected: {Expected}, Actual: {Actual}", 
        (DateTime.UtcNow - startTime).TotalMilliseconds, ids.Count(), lockProcessedCount);

    return result;
}

// Method to reset state between tests
public void ResetState()
{
    _totalProcessed = 0;
    _statusCounts.Clear();
    _errors.Clear();
    _responses.Clear();
    
    _safeProcessedCount = 0;
    // Note: ConcurrentBag doesn't have Clear(), so we create new instances in practice
}
```

## Step 4: Create the Minimal API Endpoints

### 4.1 Update Program.cs

Replace the contents of `Program.cs`:

```csharp
using ConcurrencyDemo.Services;

var builder = WebApplication.CreateBuilder(args);

// Add services
builder.Services.AddHttpClient<DataProcessor>();
builder.Services.AddScoped<DataProcessor>();

var app = builder.Build();

// Endpoint to demonstrate race conditions
app.MapGet("/unsafe/{count:int}", async (int count, DataProcessor processor) =>
{
    processor.ResetState();
    var ids = Enumerable.Range(1, count);
    var result = await processor.ProcessDataUnsafeAsync(ids);
    
    return Results.Ok(new
    {
        Method = "Unsafe",
        ExpectedCount = count,
        ActualCount = result.TotalProcessed,
        DataLoss = count - result.TotalProcessed,
        ResponseCount = result.Responses.Count,
        StatusCounts = result.StatusCounts,
        ErrorCount = result.Errors.Count,
        Message = result.TotalProcessed == count ? "âœ… No race condition detected this time" : "âŒ Race condition detected - data loss occurred"
    });
})
.WithName("ProcessUnsafe")
.WithOpenApi();

// Endpoint using concurrent collections
app.MapGet("/safe/{count:int}", async (int count, DataProcessor processor) =>
{
    var ids = Enumerable.Range(1, count);
    var result = await processor.ProcessDataSafeAsync(ids);
    
    return Results.Ok(new
    {
        Method = "Safe (Concurrent Collections)",
        ExpectedCount = count,
        ActualCount = result.TotalProcessed,
        DataLoss = count - result.TotalProcessed,
        ResponseCount = result.Responses.Count,
        StatusCounts = result.StatusCounts,
        ErrorCount = result.Errors.Count,
        Message = result.TotalProcessed == count ? "âœ… Thread-safe processing successful" : "âŒ Unexpected issue"
    });
})
.WithName("ProcessSafe")
.WithOpenApi();

// Endpoint using traditional locks
app.MapGet("/locks/{count:int}", async (int count, DataProcessor processor) =>
{
    var ids = Enumerable.Range(1, count);
    var result = await processor.ProcessDataWithLocksAsync(ids);
    
    return Results.Ok(new
    {
        Method = "Safe (Locks)",
        ExpectedCount = count,
        ActualCount = result.TotalProcessed,
        DataLoss = count - result.TotalProcessed,
        ResponseCount = result.Responses.Count,
        StatusCounts = result.StatusCounts,
        ErrorCount = result.Errors.Count,
        Message = result.TotalProcessed == count ? "âœ… Lock-based processing successful" : "âŒ Unexpected issue"
    });
})
.WithName("ProcessWithLocks")
.WithOpenApi();

// Comparison endpoint
app.MapGet("/compare/{count:int}", async (int count, DataProcessor processor) =>
{
    var ids = Enumerable.Range(1, count).ToList();
    
    // Test unsafe version multiple times to increase chance of race condition
    var unsafeResults = new List<int>();
    for (int i = 0; i < 5; i++)
    {
        processor.ResetState();
        var result = await processor.ProcessDataUnsafeAsync(ids);
        unsafeResults.Add(result.TotalProcessed);
    }
    
    // Test safe version
    var safeResult = await processor.ProcessDataSafeAsync(ids);
    var lockResult = await processor.ProcessDataWithLocksAsync(ids);
    
    return Results.Ok(new
    {
        ExpectedCount = count,
        UnsafeResults = unsafeResults,
        UnsafeConsistent = unsafeResults.All(r => r == count),
        SafeResult = safeResult.TotalProcessed,
        LockResult = lockResult.TotalProcessed,
        Summary = new
        {
            UnsafeHasRaceCondition = unsafeResults.Any(r => r != count),
            SafeIsCorrect = safeResult.TotalProcessed == count,
            LocksAreCorrect = lockResult.TotalProcessed == count
        }
    });
})
.WithName("Compare")
.WithOpenApi();

app.Run();
```

## Step 5: Testing the Demo

### 5.1 Run the Application
```bash
dotnet run
```

### 5.2 Test the Endpoints

**Test Race Conditions (run multiple times):**
```bash
curl http://localhost:5000/unsafe/1000
```

**Test Thread-Safe Version:**
```bash
curl http://localhost:5000/safe/1000
```

**Test Lock-Based Version:**
```bash
curl http://localhost:5000/locks/1000
```

**Compare All Methods:**
```bash
curl http://localhost:5000/compare/1000
```

## Step 6: Understanding the Results

### 6.1 What You'll Observe

**Unsafe Version:**
- `ActualCount` often less than `ExpectedCount`
- `ResponseCount` may not match `ActualCount`
- Inconsistent `StatusCounts`
- Race conditions become more apparent with higher counts

**Safe Versions:**
- Always correct counts
- Consistent results
- Proper data integrity

### 6.2 Key Concurrency Issues Demonstrated

1. **Lost Updates**: Multiple threads increment counters simultaneously
2. **Collection Corruption**: Non-thread-safe collections lose data
3. **Dictionary Race Conditions**: Concurrent dictionary updates cause issues
4. **Inconsistent State**: Related data becomes inconsistent

### 6.3 Solutions Explained

1. **Interlocked Operations**: Atomic operations for simple counters
2. **Concurrent Collections**: Built-in thread-safe collections
3. **Traditional Locks**: Mutual exclusion for complex operations
4. **Immutable Patterns**: Using records and copying instead of mutation

## Step 7: Best Practices

### 7.1 Performance Considerations

- **Concurrent Collections** are generally faster than locks
- **Locks** can create bottlenecks but are sometimes necessary
- **Lock Granularity** matters - avoid locking too much or too little

### 7.2 When to Use Each Approach

- **Interlocked**: Simple atomic operations (counters, flags)
- **Concurrent Collections**: When you need thread-safe collections
- **Locks**: When you need to atomically update multiple related pieces of state
- **Immutable Patterns**: When you can avoid shared mutable state

### 7.3 Common Pitfalls

- Forgetting that regular collections are not thread-safe
- Over-locking (reducing parallelism)
- Under-locking (race conditions)
- Deadlocks from multiple locks

## Conclusion

This demo shows how `Parallel.ForEachAsync` can create subtle race conditions when accessing shared state. The key takeaways:

1. **Always** assume multiple threads will access shared state simultaneously
2. Use appropriate synchronization primitives for your use case
3. Test with high concurrency to reveal race conditions
4. Consider thread-safe alternatives before adding locks
5. Monitor performance impact of synchronization choices

The combination of proper understanding and the right tools makes parallel processing both safe and efficient.

## References

Parallel Programming in .NET - Comprehensive guide covering Parallel.ForEach, PLINQ, and Task Parallel Library:

https://docs.microsoft.com/en-us/dotnet/standard/parallel-programming/

Thread Safety in .NET - Covers locks, concurrent collections, and synchronization primitives

https://learn.microsoft.com/en-us/dotnet/standard/threading/managed-threading-best-practices

Minimal APIs in ASP.NET Core - Shows how to structure minimal APIs effectively

https://docs.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis
