# Using NSwag with Visual Studio

NSwag is a powerful tool for generating client code and API documentation. Below is a step-by-step guide on how to use NSwag effectively in Visual Studio.

## 1. Install NSwag

### Install via NuGet
In Visual Studio, install NSwag using NuGet:
1. Open NuGet Package Manager (`Manage NuGet Packages`).
2. Search for `NSwag` or `NSwag.AspNetCore` (if using ASP.NET Core).
3. Install the package.

### Install NSwag CLI (Optional)
If you want to use NSwag via the terminal, install the CLI version:
1. **Without admin access**: Download `nswag.zip` from [GitHub Releases](https://github.com/RicoSuter/NSwag/releases).
2. Extract the files to your local directory.
3. Run the executable:
   ```cmd
   cd C:\Users\<YourUsername>\Tools\NSwag
   nswag.exe help
   ```
4. (Optional) Add it to PATH for easier access:
   ```cmd
   set PATH=%PATH%;C:\Users\<YourUsername>\Tools\NSwag
   ```

## 2. Configure Swagger in Your Project
Ensure that Swagger is correctly set up in your ASP.NET Core project.

```csharp
app.UseSwagger();
app.UseSwaggerUI(c =>
{
    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
});
```

## 3. Generate Client Code
Use NSwag to generate API client code.

### Via NSwag Studio
- Open NSwag Studio.
- Load your `swagger.json` API definition.
- Configure output settings (C# or TypeScript).
- Click "Generate" and save the file in your project.

### Via Command Line
Run NSwag CLI with specific parameters:

```cmd
nswag run /command:swagger2csclient /input:"https://api.example.com/swagger.json" /output:"GeneratedApiClient.cs" /language:CSharp /namespace:MyApp.ApiClients
```

**Explanation**:
- `swagger2csclient`: Generates a C# client.
- `/input`: API definition URL.
- `/output`: Output filename.
- `/language`: Specifies `CSharp` or `TypeScript`.
- `/namespace`: Defines the namespace.

## 4. Creating Individual CMD Files for REST Clients
For each REST API, create a separate `.cmd` file containing the generation command.

### Example: Generate Client for a Specific REST API 
Save the following in a file like `GenerateApiClient1.cmd`:

```cmd
nswag run /command:swagger2csclient /input:"https://api.example.com/swagger.json" /output:"GeneratedApiClient.cs" /language:CSharp /namespace:MyApp.ApiClients
```

## 5. Automate Multiple API Clients
You can create `.cmd` files to update multiple client proxies at once.

Example `GenerateApiClients.cmd` file:

```cmd
@echo off
call GenerateApiClient1.cmd
call GenerateApiClient2.cmd
call GenerateApiClient3.cmd
echo All proxies updated successfully!
pause
```

## 6. Customizations
### Authentication Headers
If your API requires authentication, add headers:

```cmd
nswag run /command:swagger2csclient /input:"https://api.example.com/swagger.json" /output:"GeneratedApiClient.cs" /language:CSharp /headers:"Authorization=Bearer YOUR_ACCESS_TOKEN"
```

### Generate TypeScript Clients
For frontend applications, generate TypeScript clients:

```cmd
nswag run /command:swagger2tsclient /input:"https://api.example.com/swagger.json" /output:"GeneratedApiClient.ts" /language:TypeScript
```

---

## 🔗 Official Documentation
[NSwag documentation](https://github.com/RicoSuter/NSwag) [Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/additional-tools/nswag-guide).

# Generating SOAP Proxy Clients Using `dotnet-svcutil`

## 1. Install `dotnet-svcutil`
Before generating SOAP clients, install the tool using the following command:

```cmd
dotnet tool install --global dotnet-svcutil
```

This makes `dotnet-svcutil` available globally for command-line use.

## 2. Creating Individual CMD Files for SOAP Clients
For each SOAP API, create a separate `.cmd` file containing the generation command.

### Example: Generate Client for a Specific SOAP Service
Save the following in a file like `GenerateSoapClient1.cmd`:

```cmd
dotnet-svcutil https://example.com/service.wsdl --outputDir "GeneratedClients" --namespace "MyApp.Services" --targetFramework net8.0
```

### Explanation:
- `https://example.com/service.wsdl`: Specifies the WSDL (Web Services Description Language) URL of the SOAP service.
- `--outputDir`: Sets the folder where generated client files are saved.
- `--namespace`: Defines the namespace for the generated proxy classes.
- `--targetFramework`: Specifies the target framework (e.g., `.NET 8.0`).

## 3. Creating a Batch Script to Run Multiple CMD Files
If you have multiple SOAP services and want to generate clients for all of them at once, create a `RunAllSoapClients.cmd` file:

```cmd
@echo off
call GenerateSoapClient1.cmd
call GenerateSoapClient2.cmd
call GenerateSoapClient3.cmd
echo All SOAP clients generated successfully!
pause
```

## 4. Additional Customizations
### Enable Async Methods
Add the `--enableAsyncOperations` flag if you want asynchronous service operations:

```cmd
dotnet-svcutil https://example.com/service.wsdl --outputDir "GeneratedClients" --namespace "MyApp.Services" --targetFramework net8.0 --enableAsyncOperations
```

### Generate Specific Service Contracts
Use `--serviceName` to target a specific service contract within the WSDL:

```cmd
dotnet-svcutil https://example.com/service.wsdl --outputDir "GeneratedClients" --namespace "MyApp.Services" --targetFramework net8.0 --serviceName "MyServiceContract"
```
## References
[Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/additional-tools/dotnet-svcutil-guide)

[GitHub repository](https://github.com/dotnet/docs/blob/main/docs/core/additional-tools/dotnet-svcutil-guide.md)
