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
