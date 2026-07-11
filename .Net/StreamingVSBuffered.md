# Streaming PDFs and Zips in .NET Minimal APIs

## Question

In .NET minimal API which returns a pdf file or a zip. How does streaming work, versus returning the whole payload at once?

## Response: Streaming vs. Buffering

In Minimal APIs, the difference comes down to whether you buffer the whole file in memory/disk before writing to the response, or write directly to the response stream as data becomes available.

### Returning the Whole Payload at Once (Buffering)

`Results.File(byte[] contents, ...)` or `Results.Bytes(...)` load the entire file into a `byte[]` first, then write it to the response body. Simple, but memory scales with file size — a 500MB zip means 500MB in memory (plus whatever GC overhead) per concurrent request.

```csharp
app.MapGet("/report.pdf", () =>
{
    byte[] pdfBytes = GeneratePdf(); // whole thing built in memory
    return Results.File(pdfBytes, "application/pdf", "report.pdf");
});
```

### Streaming (Without Buffering)

`Results.File(Stream fileStream, ...)` or `Results.Stream(...)` write chunks to the HTTP response as they're read/produced, without holding the full content in memory at once.

```csharp
app.MapGet("/report.pdf", async () =>
{
    Stream pdfStream = await GeneratePdfStreamAsync();
    return Results.File(pdfStream, "application/pdf", "report.pdf");
});
```

For a zip built on the fly, `Results.Stream` combined with `System.IO.Compression.ZipArchive` writing directly to the output stream is the classic pattern:

```csharp
app.MapGet("/export.zip", () =>
{
    return Results.Stream(async (outputStream) =>
    {
        using var archive = new ZipArchive(outputStream, ZipArchiveMode.Create, leaveOpen: true);
        foreach (var file in filesToInclude)
        {
            var entry = archive.CreateEntry(file.Name);
            using var entryStream = entry.Open();
            using var source = file.OpenRead();
            await source.CopyToAsync(entryStream);
        }
    }, "application/zip", "export.zip");
});
```

### Key Practical Differences

**Memory footprint:**
- Buffered = full file size held in memory per request
- Streaming = only the current chunk/buffer (typically a few KB–MB depending on the underlying stream)

**Time to first byte:**
- Streaming can start sending bytes before the whole file is ready (e.g., zip entries written as each source file finishes reading)
- Buffered has to finish generating everything first

**Content-Length header:**
- Buffered responses know the exact size upfront, so `Content-Length` is set and the client gets an accurate progress indicator
- Streamed responses often don't know the final size in advance, so ASP.NET Core falls back to chunked transfer encoding (`Transfer-Encoding: chunked`) unless you explicitly pass a known length

**Error handling:**
- With buffering, if generation fails you can still return a clean error response (nothing's been sent yet)
- With streaming, if you've already flushed headers and some bytes, a failure mid-stream just kills the connection — the client sees a truncated file, not a clean HTTP error

**Backpressure:**
- Streaming respects the client's read rate via `PipeWriter`/`Stream` semantics — if the client is slow, your code awaits rather than piling everything into memory regardless of consumption speed

**Seek/Range support:**
- `Results.File` with a `FileStream` (physical file on disk) supports HTTP range requests (partial content, resumable downloads) for free
- A custom `Results.Stream` delegate generally doesn't unless you implement range logic yourself

### Rule of Thumb

If the file already exists on disk or blob storage, use `Results.File` with a `FileStream`/`Stream` — you get streaming *and* range support with minimal code. If you're generating the file dynamically (zipping multiple sources, rendering a PDF on the fly), use `Results.Stream` and write directly into the output stream rather than materializing a `byte[]` or `MemoryStream` first — that's the main lever for keeping memory flat under concurrent load.

---

## Official Microsoft References

### Primary Documentation

**Microsoft Learn: Create responses in Minimal API applications**

https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis/responses?view=aspnetcore-10.0

Key sections:
- **Stream results** — explains that `Results.Stream` overloads allow access to the underlying HTTP response stream without buffering
- **File result types** — contrasts `TypedResults.File` with byte arrays versus streams, and explains that the most common and recommended way to create a file result is `TypedResults.File`, which accepts a `byte[]` or `Stream` and returns a `FileContentHttpResult` or `FileStreamHttpResult`
- **Detailed examples**: Azure Blob image streaming, video streaming with range request support

Example from the docs:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

var proxyClient = new HttpClient();
app.MapGet("/pokemon", async () =>
{
    var stream = await proxyClient.GetStreamAsync("http://contoso/pokedex.json");
    // Proxy the response as JSON
    return Results.Stream(stream, "application/json");
});

app.Run();
```

### API Reference

**Results.Stream Method (Microsoft.AspNetCore.Http)**

https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.results.stream?view=aspnetcore-10.0

The three overloads are:

1. **`Stream(Func<Stream, Task>, ...)`** — allows writing directly to the response body
   - Parameters: callback, contentType, fileDownloadName, lastModified, entityTag
   - Usage: dynamic content generation without buffering

2. **`Stream(Stream, ...)`** — writes the specified Stream to the response and supports range requests
   - An alias for `File(Stream, ...)`
   - Supports HTTP range requests (206 Partial Content, 416 Range Not Satisfiable)

3. **`Stream(PipeReader, ...)`** — writes the contents of a PipeReader to the response and supports range requests
   - More efficient for high-throughput scenarios
   - Also supports range requests

### Design Discussion

**GitHub Issue #39383: Add Results.Stream overload that takes a Func<Stream, Task>**

https://github.com/dotnet/aspnetcore/issues/39383

Microsoft's design motivation: "There are scenarios where APIs want to work directly on the response stream without buffering. Today the Stream overloads assume you have a stream that you have produced that is then copied to the underlying response stream, instead, we want to provide a result type that gives you access to the underlying response stream."

### Release Notes

**ASP.NET Core .NET 7 Preview 3 — New `Results.Stream()` overloads**

Introduced as: "for scenarios where developers need access to the underlying HTTP response stream without buffering. These overloads also improve cases where your API wants to stream data to the HTTP response stream, like from Azure Blob Storage."

---

## Key Takeaways

✅ **Use `Results.Stream(Func<Stream, Task>, ...)`** when:
- Generating PDFs, zips, or other files dynamically
- Content size is unknown or very large
- You want to avoid buffering the entire file in memory

✅ **Use `Results.File(Stream, ...)`** when:
- File already exists on disk or in blob storage
- You need range request support (video/media streaming)
- File size is known

✅ **Avoid `Results.File(byte[], ...)`** for large files because:
- Entire file held in memory per concurrent request
- Linear memory scaling with file size
- No backpressure handling

The official Microsoft guidance: use streaming to access the underlying HTTP response stream without buffering, especially for large files and cloud storage scenarios like Azure Blob Storage.

## Demo
### Program.cs
```csharp
using System.Diagnostics;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// Create a sample text file for demo purposes if it doesn't exist
const string sampleFilePath = "sample-file.txt";
if (!File.Exists(sampleFilePath))
{
    var sampleContent = string.Join(
        Environment.NewLine,
        Enumerable.Range(1, 10000)
            .Select(i => $"Line {i}: This is a sample line with some content for demonstration purposes.")
    );
    await File.WriteAllTextAsync(sampleFilePath, sampleContent);
    Console.WriteLine($"✓ Created sample file: {sampleFilePath}");
}

// Endpoint 1: Buffered result (loads entire file into memory first)
app.MapGet("/download/buffered", () =>
{
    Console.WriteLine("[BUFFERED] Request received");
    var stopwatch = Stopwatch.StartNew();
    
    // Read entire file into memory
    byte[] fileBytes = File.ReadAllBytes(sampleFilePath);
    
    stopwatch.Stop();
    Console.WriteLine($"[BUFFERED] File loaded into memory: {fileBytes.Length} bytes in {stopwatch.ElapsedMilliseconds}ms");
    
    // Return the byte array
    return Results.File(
        fileBytes, 
        contentType: "text/plain",
        fileDownloadName: "buffered-download.txt"
    );
})
.WithName("DownloadBuffered")
.WithOpenApi()
.WithDescription("Downloads file by buffering entire content into memory first");

// Endpoint 2: Streamed result (writes directly to response stream without buffering)
app.MapGet("/download/streamed", () =>
{
    Console.WriteLine("[STREAMED] Request received");
    
    // Return a Result.Stream that writes directly to the response stream
    return Results.Stream(
        async (outputStream) =>
        {
            Console.WriteLine("[STREAMED] Starting to write to response stream");
            var stopwatch = Stopwatch.StartNew();
            
            // Open file and copy directly to response stream without buffering
            using (var fileStream = File.OpenRead(sampleFilePath))
            {
                await fileStream.CopyToAsync(outputStream);
            }
            
            stopwatch.Stop();
            Console.WriteLine($"[STREAMED] Completed writing to response stream in {stopwatch.ElapsedMilliseconds}ms");
        },
        contentType: "text/plain",
        fileDownloadName: "streamed-download.txt"
    );
})
.WithName("DownloadStreamed")
.WithOpenApi()
.WithDescription("Downloads file by streaming directly to response without buffering");

// Endpoint 3: Health check
app.MapGet("/", () => new
{
    message = "Streaming vs Buffering Demo API",
    endpoints = new
    {
        buffered = new
        {
            url = "/download/buffered",
            description = "Returns file by loading entire content into memory first",
            method = "GET"
        },
        streamed = new
        {
            url = "/download/streamed",
            description = "Returns file by streaming directly to response without buffering",
            method = "GET"
        }
    },
    notes = new[]
    {
        "Use /download/buffered to see memory allocation of entire file upfront",
        "Use /download/streamed to see streaming write to response stream",
        "Check console output to see timing and memory behavior differences",
        "Monitor memory usage with tools like Task Manager or 'dotnet trace' for real differences with large files"
    }
})
.WithName("Index")
.WithOpenApi();

// Add OpenAPI/Swagger documentation
app.UseSwagger();
app.UseSwaggerUI();

Console.WriteLine();
Console.WriteLine("╔════════════════════════════════════════════════════════════╗");
Console.WriteLine("║  Streaming vs Buffering Demo API                           ║");
Console.WriteLine("╠════════════════════════════════════════════════════════════╣");
Console.WriteLine("║  Sample file: sample-file.txt                              ║");
Console.WriteLine("║                                                            ║");
Console.WriteLine("║  Endpoints:                                                ║");
Console.WriteLine("║    GET http://localhost:5000/                 (info page)  ║");
Console.WriteLine("║    GET http://localhost:5000/download/buffered            ║");
Console.WriteLine("║        → Loads file into byte[] then sends                ║");
Console.WriteLine("║    GET http://localhost:5000/download/streamed            ║");
Console.WriteLine("║        → Streams file directly to response                ║");
Console.WriteLine("║                                                            ║");
Console.WriteLine("║  Swagger UI: http://localhost:5000/swagger/ui             ║");
Console.WriteLine("╚════════════════════════════════════════════════════════════╝");
Console.WriteLine();

app.Run();
```

## ReadMe.md
```
# Streaming vs Buffering Demo API

A minimal ASP.NET Core API demonstrating the difference between **streaming** and **buffering** file responses.

## Files

- **Program.cs** — Main application with both endpoints
- **StreamingDemo.csproj** — Project configuration for .NET 8
- **sample-file.txt** — Auto-generated on first run (10,000 lines of text)

## Prerequisites

- .NET 8 SDK or later
- A terminal/command prompt

## Running the Demo

### Option 1: Using dotnet CLI

```bash
# Restore dependencies
dotnet restore

# Run the application
dotnet run
```

### Option 2: Using Visual Studio / Visual Studio Code

1. Open the folder in VS Code or Visual Studio
2. Press `F5` or click "Run"

The app will start on `http://localhost:5000`

## Endpoints

### GET `/` — Health Check / Info Page

Returns information about available endpoints and notes on testing.

**Example:**
```
curl http://localhost:5000/
```

### GET `/download/buffered` — Buffered Download

**Behavior:**
- Reads the entire file into a `byte[]` array first
- Holds all file content in memory
- Then writes to the HTTP response

**Code:**
```csharp
byte[] fileBytes = File.ReadAllBytes(sampleFilePath);
return Results.File(fileBytes, "text/plain", "buffered-download.txt");
```

**When to use:**
- Small files (< 10 MB)
- When you need the full file size upfront for `Content-Length` header
- When you need error handling before any response is sent

**Test:**
```bash
curl -O http://localhost:5000/download/buffered
```

### GET `/download/streamed` — Streamed Download

**Behavior:**
- Opens the file stream directly
- Writes to HTTP response stream without buffering
- No intermediate memory allocation for the entire file

**Code:**
```csharp
return Results.Stream(
    async (outputStream) =>
    {
        using (var fileStream = File.OpenRead(sampleFilePath))
        {
            await fileStream.CopyToAsync(outputStream);
        }
    },
    contentType: "text/plain",
    fileDownloadName: "streamed-download.txt"
);
```

**When to use:**
- Large files (> 100 MB)
- When generating files dynamically (PDFs, ZIPs)
- When memory efficiency matters
- Cloud storage scenarios (Azure Blob, S3)
- Video/media streaming

**Test:**
```bash
curl -O http://localhost:5000/download/streamed
```

### GET `/swagger/ui` — Swagger Documentation

Interactive API documentation powered by Swashbuckle.

**Visit:** `http://localhost:5000/swagger/ui`

## Console Output

When you make requests, the console displays timing information:

```
[BUFFERED] Request received
[BUFFERED] File loaded into memory: 456789 bytes in 5ms

[STREAMED] Request received
[STREAMED] Starting to write to response stream
[STREAMED] Completed writing to response stream in 3ms
```

## Key Differences to Observe

### Memory Usage

| Aspect | Buffered | Streamed |
|--------|----------|----------|
| **Memory footprint** | Entire file size (+ GC overhead) | Few KB to MB (buffer chunks only) |
| **Scalability** | Linear with file size | Constant |
| **Concurrent requests** | 10 × 100MB file = 1GB RAM | 10 × 100MB file ≈ 10MB RAM |

### Time to First Byte

- **Buffered**: Must read entire file before first byte is sent
- **Streamed**: First byte sent as soon as file read begins

### Error Handling

- **Buffered**: Errors before response headers are sent return clean HTTP error
- **Streamed**: Errors mid-stream result in truncated responses

## Testing with Large Files

To test with a larger file and see real memory differences:

1. Modify `Program.cs` to generate a bigger sample file:
   ```csharp
   Enumerable.Range(1, 1000000)  // 1 million lines instead of 10,000
   ```

2. Run the app and monitor memory usage:
   - **Windows**: Task Manager → Processes
   - **macOS**: Activity Monitor
   - **Linux**: `top` or `htop`

3. Hit `/download/buffered` and watch memory spike
4. Hit `/download/streamed` and observe minimal memory increase

## HTTP Details

### Buffered Response Headers

```
HTTP/1.1 200 OK
Content-Type: text/plain
Content-Length: 456789              ← Known size upfront
Content-Disposition: attachment; filename="buffered-download.txt"
```

### Streamed Response Headers

```
HTTP/1.1 200 OK
Content-Type: text/plain
Transfer-Encoding: chunked          ← Size unknown, sent in chunks
Content-Disposition: attachment; filename="streamed-download.txt"
```

## Real-World Scenarios

### PDF Generation (Dynamic)
```csharp
app.MapGet("/report.pdf", () =>
{
    return Results.Stream(
        async (stream) =>
        {
            using var pdfDocument = new PdfDocument();
            // Generate PDF content...
            pdfDocument.Save(stream, false);
        },
        "application/pdf",
        "report.pdf"
    );
});
```

### ZIP Archive (Multiple Files)
```csharp
app.MapGet("/export.zip", () =>
{
    return Results.Stream(
        async (stream) =>
        {
            using var archive = new ZipArchive(stream, ZipArchiveMode.Create, true);
            foreach (var file in Directory.GetFiles("./data"))
            {
                var entry = archive.CreateEntry(Path.GetFileName(file));
                using var entryStream = entry.Open();
                using var source = File.OpenRead(file);
                await source.CopyToAsync(entryStream);
            }
        },
        "application/zip",
        "export.zip"
    );
});
```

### Azure Blob Storage
```csharp
app.MapGet("/blob/{name}", async (string name, BlobClient blob) =>
{
    var download = await blob.DownloadAsync();
    return Results.Stream(
        download.Value.Content,
        "application/octet-stream",
        name
    );
});
```

## Stopping the App

Press `Ctrl+C` in the terminal.

## Next Steps

1. Try both endpoints and compare the console output
2. Increase the sample file size and observe memory differences
3. Implement your own file generation logic (PDF, ZIP, etc.)
4. Test with real large files from your application
5. Monitor HTTP headers with browser DevTools or curl `-v`

## References

- [Microsoft Learn: Create responses in Minimal API applications](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis/responses)
- [Results.Stream API Documentation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.results.stream)
- [ASP.NET Core Minimal APIs](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis)

```
