
# ✅ Best Practices: Dependency Injection Setup & Validation in .NET 8 APIs

## 🎯 Goals

- Maintain clean and modular `Program.cs`.
- Validate that all registered dependencies are resolvable at **build-time**, not just at runtime.
- Block misconfigured DI from reaching production using **unit tests** and **CI pipelines**.
- Follow official .NET guidance and community best practices.

---

## 1. ✅ Extracting Service Registrations from `Program.cs`

### 🔍 Why extract?

- Promotes **separation of concerns**
- Keeps `Program.cs` focused on high-level bootstrapping
- Encourages **layered architecture** or **feature folders**
- Facilitates **unit testing** of DI validation

### 🧱 How to extract

Organize service registrations into **extension methods** based on layers (e.g., Application, Infrastructure, Web) or features (e.g., Orders, Users).

#### 📁 Example: `Infrastructure/DependencyInjection.cs`

```csharp
namespace MyApp.Infrastructure;

public static class InfrastructureServiceRegistration
{
    public static IServiceCollection AddInfrastructure(this IServiceCollection services, IConfiguration configuration)
    {
        services.AddDbContext<AppDbContext>(options =>
            options.UseSqlServer(configuration.GetConnectionString("Default")));

        services.AddScoped<IUserRepository, UserRepository>();
        return services;
    }
}
```

#### ✂️ Clean `Program.cs`

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services
    .AddInfrastructure(builder.Configuration)
    .AddApplication()
    .AddPresentation();

var app = builder.Build();
app.Run();
```

> 📘 **Official guidance:** [Microsoft Learn – Dependency Injection in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection)

---

## 2. ✅ Validating DI Configuration at Build-Time

By default, DI issues surface **at runtime**. However, .NET allows validating the entire service graph during **app startup** (build-time validation).

### 🧪 Enable validation with `ValidateOnBuild`

```csharp
builder.Host.UseDefaultServiceProvider(options =>
{
    options.ValidateScopes = true;
    options.ValidateOnBuild = true;
});
```

- `ValidateOnBuild`: Checks if all services can be **instantiated**
- `ValidateScopes`: Checks for **scoping violations**, e.g., scoped service injected into singleton

> ✅ Use `builder.Environment.IsDevelopment()` to toggle in non-prod environments

```csharp
builder.Host.UseDefaultServiceProvider(options =>
{
    options.ValidateScopes = builder.Environment.IsDevelopment();
    options.ValidateOnBuild = builder.Environment.IsDevelopment();
});
```

> 📘 **Official source:** [ServiceProviderOptions.ValidateOnBuild Property](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validateonbuild)

---

## 3. ✅ Verifying DI in Unit Tests

You can write a **unit test** to ensure your DI configuration works before merging to `main`.

### ✅ Example: DependencyInjectionValidationTests.cs

```csharp
public class DependencyInjectionValidationTests
{
    [Fact]
    public void AllServices_Should_Be_Resolvable()
    {
        var hostBuilder = Host.CreateDefaultBuilder()
            .ConfigureServices((context, services) =>
            {
                var config = new ConfigurationBuilder().Build();
                services.AddInfrastructure(config);
                services.AddApplication();
            })
            .UseDefaultServiceProvider(options =>
            {
                options.ValidateScopes = true;
                options.ValidateOnBuild = true;
            });

        var exception = Record.Exception(() => hostBuilder.Build());
        Assert.Null(exception); // Fail the test if any DI misconfiguration exists
    }
}
```

Add this to your **CI/CD pipeline** to prevent broken DI from reaching production.

---

## 4. ✅ Reusing Service Registrations Across App & Tests

To avoid duplication between `Program.cs` and test projects:

### 🧩 Create a Shared Registration Method

```csharp
public static class ServiceRegistration
{
    public static void RegisterAll(IServiceCollection services, IConfiguration configuration)
    {
        services.AddInfrastructure(configuration);
        services.AddApplication();
        services.AddPresentation();
    }
}
```

Use this in both:

- `Program.cs`: `builder.Services.RegisterAll(builder.Configuration);`
- Unit test: `ServiceRegistration.RegisterAll(services, config);`

---

## 5. ✅ Optional: Organize by Feature or Layer

For large apps, use **feature folders** or **layered structure**:

```
/Application
  - DependencyInjection.cs
/Infrastructure
  - DependencyInjection.cs
/Presentation
  - DependencyInjection.cs
```

Each defines its own `AddXYZ(this IServiceCollection)` method for separation of concerns.

---

## ✅ Summary

| Practice | Benefit |
|---------|---------|
| ✅ Extract service registration | Cleaner, testable, reusable DI setup |
| ✅ Use `ValidateOnBuild` and `ValidateScopes` | Catch errors early at app startup |
| ✅ Write a DI validation test | Prevents broken DI from reaching CI |
| ✅ Reuse registration in tests | DRY and maintainable |
| ✅ Organize registrations by layer or feature | Scalable and readable |

---

## 📚 Official Resources

- [🔗 Microsoft Docs – Dependency Injection in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection)
- [🔗 ServiceProviderOptions.ValidateOnBuild](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validateonbuild)
- [🔗 Microsoft Docs – Use dependency injection in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection-usage)
