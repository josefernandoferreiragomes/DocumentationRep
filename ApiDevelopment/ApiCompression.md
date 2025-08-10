🧩 Enabling Gzip Compression in .NET APIs and Azure API Management (APIM)

This guide explains how to implement Gzip compression in an ASP.NET Core Web API and configure Azure API Management (APIM) to work with compressed payloads.

---

📘 ASP.NET Core – Gzip Compression

ASP.NET Core includes built-in middleware for response compression.

✅ 1. Add the Response Compression Middleware

In your Startup.cs or Program.cs:
```csharp
services.AddResponseCompression(options =>
{
    options.Providers.Add<GzipCompressionProvider>();
    options.MimeTypes = new[] { "application/json" };
});

services.Configure<GzipCompressionProviderOptions>(options =>
{
    options.Level = CompressionLevel.Fastest;
});
```
✅ 2. Use the Middleware in the Pipeline

```csharp
app.UseResponseCompression();
```
> This enables compression for clients that send Accept-Encoding: gzip and automatically adds Content-Encoding: gzip to the response.



🔗 Microsoft Docs – Response Compression Middleware

<https://learn.microsoft.com/en-us/aspnet/core/performance/response-compression?view=aspnetcore-9.0>

---

🛡️ Azure API Management (APIM) – Supporting Gzip

✅ 1. Set Content-Encoding Header in Outbound Policy

In the API’s policy editor:
```xml
<outbound>
  <base />
  <set-header name="Content-Encoding" exists-action="override">
    <value>gzip</value>
  </set-header>
</outbound>
```
> This ensures the client knows the response is Gzip-compressed.



✅ 2. Ensure Clients Support Gzip

Clients must include the header:
```bash
Accept-Encoding: gzip
```

---

🔗 Additional References

ASP.NET Core Response Compression Middleware (Microsoft Docs)

<https://learn.microsoft.com/en-us/aspnet/core/performance/response-compression?view=aspnetcore-9.0>

Azure APIM Policy Sample for Gzip

<https://learn.microsoft.com/en-us/answers/questions/502806/gzip-compression-decompression-with-api-management?utm_source=chatgpt.com>


---
