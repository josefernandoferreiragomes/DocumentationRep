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
