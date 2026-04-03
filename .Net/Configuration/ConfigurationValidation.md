# Configuration Validation in .NET 8 Minimal APIs

A comprehensive guide to implementing robust configuration validation using the Options Pattern and validating plain configuration properties from `appsettings.json`.

## Table of Contents

1. [Overview](#overview)
2. [Why Validate Configuration?](#why-validate-configuration)
3. [Validation Approaches](#validation-approaches)
4. [Method 1: Data Annotations](#method-1-data-annotations)
5. [Method 2: Custom Inline Validation](#method-2-custom-inline-validation)
6. [Method 3: IValidateOptions Interface](#method-3-ivalidateoptions-interface)
7. [Method 4: Validating Root-Level Settings](#method-4-validating-root-level-settings)
8. [Complete Working Example](#complete-working-example)
9. [Best Practices](#best-practices)
10. [Official References](#official-references)

---

## Overview

Configuration validation ensures your application fails fast at startup if settings are invalid, rather than discovering issues at runtime. .NET 8's Options Pattern provides multiple validation strategies for different complexity levels.

---

## Why Validate Configuration?

**Without Validation:**
- Runtime errors when invalid config is first accessed
- Difficult to diagnose configuration issues
- Application may start but fail unexpectedly

**With Validation:**
- ✅ Fail fast at startup with clear error messages
- ✅ Catch configuration errors before deployment
- ✅ Enforce business rules and constraints
- ✅ Better developer experience and debugging

---

## Validation Approaches

| Approach | Complexity | Use Case |
|----------|-----------|----------|
| Data Annotations | Simple | Basic validation rules (required, range, format) |
| Inline Validation | Simple-Medium | Quick custom checks without extra classes |
| IValidateOptions | Medium-Complex | Complex validation logic, cross-property rules |
| Manual Validation | Any | Root-level settings, direct IConfiguration usage |

---

## Method 1: Data Annotations

**Best for:** Simple validation rules like required fields, ranges, and format validation.

### Step 1: Create Options Class with Attributes

```csharp
using System.ComponentModel.DataAnnotations;

public class EmailSettings
{
    [Required(ErrorMessage = "SMTP Server is required")]
    public string SmtpServer { get; set; } = string.Empty;
    
    [Range(1, 65535, ErrorMessage = "Port must be between 1 and 65535")]
    public int Port { get; set; }
    
    [Required]
    [EmailAddress(ErrorMessage = "Invalid email address format")]
    public string FromAddress { get; set; } = string.Empty;
    
    [MinLength(3, ErrorMessage = "FromName must be at least 3 characters")]
    public string FromName { get; set; } = string.Empty;
}
```

### Step 2: Configure in appsettings.json

```json
{
  "EmailSettings": {
    "SmtpServer": "smtp.gmail.com",
    "Port": 587,
    "FromAddress": "noreply@example.com",
    "FromName": "My Application"
  }
}
```

### Step 3: Register with Validation

```csharp
// In Program.cs
builder.Services.AddOptions<EmailSettings>()
    .BindConfiguration("EmailSettings")
    .ValidateDataAnnotations()  // Enable data annotation validation
    .ValidateOnStart();          // Validate at startup, not on first access
```

### Step 4: Use in Your Application

```csharp
app.MapGet("/send-email", (IOptions<EmailSettings> emailOptions) =>
{
    var settings = emailOptions.Value; // Already validated!
    // Use settings.SmtpServer, settings.Port, etc.
    return Results.Ok($"Using SMTP: {settings.SmtpServer}");
});
```

### Common Data Annotation Attributes

```csharp
[Required]                          // Must have a value
[Range(1, 100)]                     // Numeric range
[MinLength(5)]                      // Minimum string length
[MaxLength(50)]                     // Maximum string length
[EmailAddress]                      // Valid email format
[Url]                              // Valid URL format
[RegularExpression(@"^\d{3}-\d{2}-\d{4}$")] // Custom regex pattern
[Phone]                            // Valid phone number
[CreditCard]                       // Valid credit card format
```

---

## Method 2: Custom Inline Validation

**Best for:** Quick custom checks without creating separate validator classes.

### Combining Data Annotations with Custom Logic

```csharp
public class DatabaseSettings
{
    [Required]
    [MinLength(20)]
    public string ConnectionString { get; set; } = string.Empty;
    
    [Range(1, 3600)]
    public int CommandTimeout { get; set; } = 30;
    
    [Range(0, 10)]
    public int MaxRetryCount { get; set; } = 3;
}

// In Program.cs
builder.Services.AddOptions<DatabaseSettings>()
    .BindConfiguration("DatabaseSettings")
    .ValidateDataAnnotations()
    .Validate(settings => 
    {
        // Custom validation: ensure connection string contains required keywords
        return settings.ConnectionString.Contains("Server", StringComparison.OrdinalIgnoreCase) &&
               settings.ConnectionString.Contains("Database", StringComparison.OrdinalIgnoreCase);
    }, "Connection string must contain 'Server' and 'Database' keywords")
    .ValidateOnStart();
```

### Multiple Custom Validations

```csharp
builder.Services.AddOptions<ApiSettings>()
    .BindConfiguration("ApiSettings")
    .ValidateDataAnnotations()
    .Validate(settings => 
        !string.IsNullOrEmpty(settings.ApiKey), 
        "API Key cannot be empty")
    .Validate(settings => 
        settings.TimeoutSeconds <= settings.MaxRetryCount * 10,
        "Total timeout (retries * timeout) seems excessive")
    .ValidateOnStart();
```

---

## Method 3: IValidateOptions Interface

**Best for:** Complex validation logic, cross-property validation, and business rules.

### Step 1: Create Options Class

```csharp
public class ApiSettings
{
    [Required]
    [Url]
    public string BaseUrl { get; set; } = string.Empty;
    
    [Required]
    [MinLength(10)]
    public string ApiKey { get; set; } = string.Empty;
    
    [Range(1, 300)]
    public int TimeoutSeconds { get; set; } = 30;
    
    [Range(0, 10)]
    public int MaxRetries { get; set; } = 3;
    
    [Range(1, 1000)]
    public int RateLimitPerMinute { get; set; } = 60;
}
```

### Step 2: Create Validator Class

```csharp
using Microsoft.Extensions.Options;

public class ApiSettingsValidator : IValidateOptions<ApiSettings>
{
    public ValidateOptionsResult Validate(string? name, ApiSettings options)
    {
        var failures = new List<string>();

        // Validate BaseUrl
        if (string.IsNullOrWhiteSpace(options.BaseUrl))
        {
            failures.Add("BaseUrl is required");
        }
        else if (!Uri.TryCreate(options.BaseUrl, UriKind.Absolute, out var uri) || 
                 (uri.Scheme != Uri.UriSchemeHttp && uri.Scheme != Uri.UriSchemeHttps))
        {
            failures.Add("BaseUrl must be a valid HTTP or HTTPS URL");
        }

        // Validate ApiKey
        if (string.IsNullOrWhiteSpace(options.ApiKey))
        {
            failures.Add("ApiKey is required");
        }
        else if (options.ApiKey.Length < 10)
        {
            failures.Add("ApiKey must be at least 10 characters long");
        }

        // Cross-property validation: business rule
        if (options.MaxRetries > 5 && options.TimeoutSeconds < 10)
        {
            failures.Add(
                "With more than 5 retries, timeout should be at least 10 seconds " +
                "to avoid excessive delays");
        }

        // Validate rate limiting makes sense
        if (options.RateLimitPerMinute < 1)
        {
            failures.Add("RateLimitPerMinute must be at least 1");
        }

        if (failures.Count > 0)
        {
            return ValidateOptionsResult.Fail(failures);
        }

        return ValidateOptionsResult.Success;
    }
}
```

### Step 3: Register Both Options and Validator

```csharp
// In Program.cs
builder.Services.AddSingleton<IValidateOptions<ApiSettings>, ApiSettingsValidator>();
builder.Services.AddOptions<ApiSettings>()
    .BindConfiguration("ApiSettings")
    .ValidateOnStart();
```

---

## Method 4: Validating Root-Level Settings

**Best for:** Settings stored directly at the root of `appsettings.json`, not in nested sections.

### Scenario: Root-Level Configuration

```json
{
  "ApplicationName": "MyMinimalAPI",
  "Environment": "Development",
  "Version": "1.0.0",
  "MaxRequestSizeInMB": 10,
  "EnableSwagger": true
}
```

### Approach A: Manual Validation Function

```csharp
static void ValidateRootConfiguration(IConfiguration configuration)
{
    var errors = new List<string>();
    
    // Validate ApplicationName
    var appName = configuration["ApplicationName"];
    if (string.IsNullOrWhiteSpace(appName))
        errors.Add("ApplicationName is required in root configuration");
    else if (appName.Length < 3)
        errors.Add("ApplicationName must be at least 3 characters");
    
    // Validate Environment
    var environment = configuration["Environment"];
    var validEnvironments = new[] { "Development", "Staging", "Production" };
    if (string.IsNullOrWhiteSpace(environment))
        errors.Add("Environment is required in root configuration");
    else if (!validEnvironments.Contains(environment))
        errors.Add($"Environment must be one of: {string.Join(", ", validEnvironments)}");
    
    // Validate Version
    var version = configuration["Version"];
    if (string.IsNullOrWhiteSpace(version))
        errors.Add("Version is required in root configuration");
    else if (!System.Text.RegularExpressions.Regex.IsMatch(version, @"^\d+\.\d+\.\d+$"))
        errors.Add("Version must be in format X.Y.Z (e.g., 1.0.0)");
    
    // Validate MaxRequestSizeInMB
    var maxSize = configuration.GetValue<int>("MaxRequestSizeInMB");
    if (maxSize < 1 || maxSize > 100)
        errors.Add("MaxRequestSizeInMB must be between 1 and 100");
    
    if (errors.Any())
    {
        var errorMessage = "Root configuration validation failed:\n" + 
                          string.Join("\n", errors.Select(e => $"  - {e}"));
        throw new InvalidOperationException(errorMessage);
    }
}

// In Program.cs
ValidateRootConfiguration(builder.Configuration);
```

### Approach B: Wrapper Options Class (Recommended)

```csharp
public class RootSettings
{
    [Required]
    [MinLength(3)]
    public string ApplicationName { get; set; } = string.Empty;
    
    [Required]
    [RegularExpression("^(Development|Staging|Production)$")]
    public string Environment { get; set; } = string.Empty;
    
    [Required]
    [RegularExpression(@"^\d+\.\d+\.\d+$")]
    public string Version { get; set; } = string.Empty;
    
    [Range(1, 100)]
    public int MaxRequestSizeInMB { get; set; }
    
    public bool EnableSwagger { get; set; }
}

// In Program.cs
builder.Services.AddOptions<RootSettings>()
    .Configure(options =>
    {
        options.ApplicationName = builder.Configuration["ApplicationName"] ?? string.Empty;
        options.Environment = builder.Configuration["Environment"] ?? string.Empty;
        options.Version = builder.Configuration["Version"] ?? string.Empty;
        options.MaxRequestSizeInMB = builder.Configuration.GetValue<int>("MaxRequestSizeInMB");
        options.EnableSwagger = builder.Configuration.GetValue<bool>("EnableSwagger");
    })
    .ValidateDataAnnotations()
    .ValidateOnStart();

// Usage in endpoints
app.MapGet("/config/root", (IOptions<RootSettings> options) =>
{
    var settings = options.Value; // Validated and strongly-typed!
    return Results.Ok(settings);
});
```

---

## Complete Working Example

### appsettings.json

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  },
  "EmailSettings": {
    "SmtpServer": "smtp.gmail.com",
    "Port": 587,
    "FromAddress": "noreply@example.com",
    "FromName": "My Application"
  },
  "DatabaseSettings": {
    "ConnectionString": "Server=localhost;Database=MyApp;",
    "CommandTimeout": 30,
    "MaxRetryCount": 3
  },
  "ApiSettings": {
    "BaseUrl": "https://api.example.com",
    "ApiKey": "your-api-key-here",
    "TimeoutSeconds": 30,
    "MaxRetries": 3,
    "RateLimitPerMinute": 60
  },
  "ApplicationName": "MyMinimalAPI",
  "Environment": "Development",
  "Version": "1.0.0"
}
```

### Program.cs

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.Options;

var builder = WebApplication.CreateBuilder(args);

// 1. Data Annotations validation
builder.Services.AddOptions<EmailSettings>()
    .BindConfiguration("EmailSettings")
    .ValidateDataAnnotations()
    .ValidateOnStart();

// 2. Data Annotations + Custom inline validation
builder.Services.AddOptions<DatabaseSettings>()
    .BindConfiguration("DatabaseSettings")
    .ValidateDataAnnotations()
    .Validate(settings => 
        settings.ConnectionString.Contains("Server") &&
        settings.ConnectionString.Contains("Database"),
        "Connection string must contain 'Server' and 'Database'")
    .ValidateOnStart();

// 3. IValidateOptions validator
builder.Services.AddSingleton<IValidateOptions<ApiSettings>, ApiSettingsValidator>();
builder.Services.AddOptions<ApiSettings>()
    .BindConfiguration("ApiSettings")
    .ValidateOnStart();

// 4. Root-level settings validation
builder.Services.AddOptions<RootSettings>()
    .Configure(options =>
    {
        options.ApplicationName = builder.Configuration["ApplicationName"] ?? string.Empty;
        options.Environment = builder.Configuration["Environment"] ?? string.Empty;
        options.Version = builder.Configuration["Version"] ?? string.Empty;
    })
    .ValidateDataAnnotations()
    .ValidateOnStart();

var app = builder.Build();

app.MapGet("/", () => "Configuration Validation Demo");

app.MapGet("/config/email", (IOptions<EmailSettings> options) =>
    Results.Ok(options.Value));

app.MapGet("/config/database", (IOptions<DatabaseSettings> options) =>
    Results.Ok(options.Value));

app.MapGet("/config/api", (IOptions<ApiSettings> options) =>
    Results.Ok(options.Value));

app.MapGet("/config/root", (IOptions<RootSettings> options) =>
    Results.Ok(options.Value));

app.Run();

// Option classes and validators...
```

---

## Best Practices

### ✅ DO

1. **Always use `ValidateOnStart()`** - Fail fast at startup, not on first access
2. **Choose the right validation approach** for your complexity level:
   - Simple rules → Data Annotations
   - Quick custom checks → Inline validation
   - Complex business logic → IValidateOptions
3. **Provide clear error messages** - Help developers identify what's wrong quickly
4. **Validate cross-property rules** when properties depend on each other
5. **Use strongly-typed options classes** instead of raw IConfiguration access
6. **Test invalid configurations** to ensure validation works correctly

### ❌ DON'T

1. **Don't skip `ValidateOnStart()`** - Without it, validation only runs on first access
2. **Don't over-validate** - Focus on essential business rules and constraints
3. **Don't mix validation logic** in multiple places for the same settings
4. **Don't use IConfiguration directly** when Options Pattern is better suited
5. **Don't ignore validation errors** - Let the application fail to start

---

## Testing Your Validation

### Test Invalid Configuration

Try these intentionally invalid settings to verify validation works:

```json
{
  "EmailSettings": {
    "SmtpServer": "",           // ❌ Should fail: Required
    "Port": 70000,              // ❌ Should fail: Out of range
    "FromAddress": "not-email", // ❌ Should fail: Invalid email
    "FromName": "AB"            // ❌ Should fail: Too short
  }
}
```

### Expected Behavior

When validation fails at startup, you'll see:
```
Unhandled exception. Microsoft.Extensions.Options.OptionsValidationException: 
DataAnnotation validation failed for 'EmailSettings' members: 
'SmtpServer' with the error: 'SMTP Server is required.'.
'Port' with the error: 'Port must be between 1 and 65535.'.
```

---

## .NET 8 Enhancement: AddOptionsWithValidateOnStart

.NET 8 introduced a convenience method that combines registration and validation:

```csharp
// Traditional approach
builder.Services.AddOptions<EmailSettings>()
    .BindConfiguration("EmailSettings")
    .ValidateDataAnnotations()
    .ValidateOnStart();

// .NET 8+ shorthand
builder.Services.AddOptionsWithValidateOnStart<EmailSettings>()
    .BindConfiguration("EmailSettings")
    .ValidateDataAnnotations();
```

---

## Official References

### Primary Documentation

1. **[Options Pattern in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options)**
   - Comprehensive guide covering all validation approaches
   - Examples of ValidateOnStart() and ValidateDataAnnotations()

2. **[Options Pattern - .NET Core Extensions](https://learn.microsoft.com/en-us/dotnet/core/extensions/options)**
   - General .NET options pattern documentation
   - Covers AddOptionsWithValidateOnStart (new in .NET 8)

3. **[Options Validation Source Generation](https://learn.microsoft.com/en-us/dotnet/core/extensions/options-validation-generator)**
   - Compile-time validation using source generators (.NET 8+)

### API Reference

4. **[ValidateOnStart Method](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.optionsbuilderextensions.validateonstart)**
   - Official API documentation

5. **[IValidateOptions Interface](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.options.ivalidateoptions-1)**
   - Interface for custom validators

---

## Troubleshooting

### Validation Not Running?

- ✅ Ensure you called `ValidateOnStart()`
- ✅ Check that your options class has the validation attributes
- ✅ Verify the configuration section name matches `BindConfiguration("SectionName")`

### Application Starts Despite Invalid Config?

- ✅ Missing `ValidateOnStart()` - validation only runs on first IOptions access
- ✅ Check if the configuration section exists in appsettings.json

### Can't Find Error Details?

- ✅ Look at the complete exception message - it lists all validation failures
- ✅ Use detailed error messages in your validation attributes
- ✅ Check the console/logs at application startup

---

## Summary

Configuration validation in .NET 8 provides multiple approaches for different scenarios:

| Scenario | Recommended Approach |
|----------|---------------------|
| Simple field validation | Data Annotations + ValidateOnStart() |
| Quick custom checks | Inline Validate() method |
| Complex business rules | IValidateOptions<T> implementation |
| Root-level settings | Wrapper options class with data annotations |
| Multiple validation steps | Combine all approaches as needed |

**Key Takeaway:** Always use `ValidateOnStart()` to ensure your application fails fast with clear error messages when configuration is invalid, preventing runtime surprises in production.

---

## Additional Resources

- [Configuration in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/)
- [Dependency Injection in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection)
- [Data Annotations Reference](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations)

---

*Last Updated: January 2026 | .NET 8 | Minimal APIs*
