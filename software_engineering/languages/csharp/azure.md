# Azure & Microsoft Fabric Integration for ASP.NET Core

**Focus**: Azure services integration, Microsoft Fabric API, Managed Identity, Application Insights, Azure-specific patterns.

## Table of Contents

- [1. Configuration & Azure Key Vault](#1-configuration--azure-key-vault)
- [2. Authentication (Azure AD)](#2-authentication-azure-ad)
- [3. Azure Services](#3-azure-services)
- [4. Microsoft Fabric Integration](#4-microsoft-fabric-integration)
- [5. Application Insights & Monitoring](#5-application-insights--monitoring)
- [6. Managed Identity](#6-managed-identity)

---

## 1. Configuration & Azure Key Vault

```csharp
// Program.cs - Configuration precedence (later overrides earlier)
var builder = WebApplication.CreateBuilder(args);

// Automatic: appsettings.json, appsettings.{Environment}.json, environment variables, command-line
// Add Azure Key Vault (production only)
if (!builder.Environment.IsDevelopment())
{
    var keyVaultUrl = builder.Configuration["Azure:KeyVault:VaultUrl"];
    if (!string.IsNullOrEmpty(keyVaultUrl))
    {
        builder.Configuration.AddAzureKeyVault(
            new Uri(keyVaultUrl), 
            new DefaultAzureCredential());
    }
}

// Access: builder.Configuration["ConnectionStrings:DefaultConnection"]
// Options pattern
public class AzureOptions
{
    public string KeyVaultUrl { get; set; }
    public string StorageAccount { get; set; }
}
builder.Services.Configure<AzureOptions>(builder.Configuration.GetSection("Azure"));
```

**Precedence**: Command-line > Environment > Azure Key Vault > appsettings.{Environment}.json > appsettings.json

**Azure Key Vault**: Use `Azure.Extensions.AspNetCore.Configuration.Secrets` - Add conditionally (not in dev)

**Azure App Configuration**:
```csharp
builder.Configuration.AddAzureAppConfiguration(options =>
    options.Connect(new Uri("https://myappconfig.azconfig.io"), new DefaultAzureCredential()));
```

---

## 2. Authentication (Azure AD)

```csharp
// Program.cs - Azure AD / Entra ID
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.Authority = $"https://login.microsoftonline.com/{tenantId}";
        options.Audience = clientId;
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
        };
    });

builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("AdminOnly", policy => policy.RequireRole("Admin"));
    options.AddPolicy("RequireScope", policy => policy.RequireClaim("scope", "api.read"));
});

// In controller/Minimal API
[Authorize] // or app.MapGet("/protected", [Authorize] () => ...)
[Authorize(Roles = "Admin")]
[Authorize(Policy = "RequireScope")]
public class UsersController : ControllerBase { }
```

**Azure AD**: Use `Microsoft.Identity.Web` for easier integration

**Managed Identity**: `DefaultAzureCredential` for service-to-service auth (Azure services)

**Claims**: `User.Claims`, `User.FindFirst(ClaimTypes.NameIdentifier)?.Value`, `User.IsInRole("Admin")`

**Microsoft.Identity.Web**:
```csharp
builder.Services.AddMicrosoftIdentityWebApiAuthentication(
    builder.Configuration, "AzureAd");
```

---

## 3. Azure Services

**Azure Key Vault** (secrets):
```csharp
using Azure.Security.KeyVault.Secrets;
using Azure.Identity;

var credential = new DefaultAzureCredential(); // Managed Identity in Azure
var client = new SecretClient(new Uri("https://myvault.vault.azure.net/"), credential);
var secret = await client.GetSecretAsync("my-secret");
```

**Azure Storage Blobs**:
```csharp
using Azure.Storage.Blobs;

var blobClient = new BlobServiceClient(connectionString);
var container = blobClient.GetBlobContainerClient("container");
var blob = container.GetBlobClient("file.txt");
await blob.UploadAsync(stream);
```

**Azure Service Bus** (messaging):
```csharp
using Azure.Messaging.ServiceBus;

var client = new ServiceBusClient(connectionString);
var sender = client.CreateSender("queue");
await sender.SendMessageAsync(new ServiceBusMessage(json));

var receiver = client.CreateReceiver("queue");
var message = await receiver.ReceiveMessageAsync();
await receiver.CompleteMessageAsync(message);
```

**Azure App Configuration** (settings):
```csharp
builder.Configuration.AddAzureAppConfiguration(options =>
    options.Connect(new Uri("https://myappconfig.azconfig.io"), new DefaultAzureCredential()));
```

**Service registration**: Register Azure clients as singletons:
```csharp
builder.Services.AddSingleton(sp => 
{
    var credential = new DefaultAzureCredential();
    return new SecretClient(new Uri(keyVaultUrl), credential);
});
```

**Azure SQL with EF Core**:
```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"),
        sqlOptions => sqlOptions.EnableRetryOnFailure(
            maxRetryCount: 5, 
            maxRetryDelay: TimeSpan.FromSeconds(30), 
            errorNumbersToAdd: null)));
```

---

## 4. Microsoft Fabric Integration

```csharp
// Fabric REST API client (enterprise pattern)
public class FabricApiClient
{
    private readonly HttpClient _httpClient;
    private readonly ILogger<FabricApiClient> _logger;
    
    public FabricApiClient(HttpClient httpClient, ILogger<FabricApiClient> logger) =>
        (_httpClient, _logger) = (httpClient, logger);
    
    public async Task<FabricWorkspace> GetWorkspaceAsync(string workspaceId)
    {
        var response = await _httpClient.GetAsync($"/v1/workspaces/{workspaceId}");
        response.EnsureSuccessStatusCode();
        return await response.Content.ReadFromJsonAsync<FabricWorkspace>();
    }
    
    public async Task<FabricItem> CreateItemAsync(string workspaceId, FabricItem item)
    {
        var response = await _httpClient.PostAsJsonAsync($"/v1/workspaces/{workspaceId}/items", item);
        response.EnsureSuccessStatusCode();
        return await response.Content.ReadFromJsonAsync<FabricItem>();
    }
}

// Program.cs - Register with Managed Identity
builder.Services.AddHttpClient<FabricApiClient>(async (sp, client) =>
{
    client.BaseAddress = new Uri("https://api.fabric.microsoft.com");
    var credential = new DefaultAzureCredential();
    var token = await credential.GetTokenAsync(new TokenRequestContext(new[] { "https://analysis.windows.net/powerbi/api/.default" }));
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token.Token);
});
```

**Authentication**: Use `DefaultAzureCredential` with Fabric API scope for Managed Identity

**REST API**: Fabric REST API endpoints for workspaces, items, datasets, reports

**Fabric API scopes**: `https://analysis.windows.net/powerbi/api/.default` (Power BI), `https://analysis.windows.net/fabric/api/.default` (Fabric)

---

## 5. Application Insights & Monitoring

```csharp
// Program.cs - Application Insights (Azure monitoring)
builder.Services.AddApplicationInsightsTelemetry();

// Health checks with Azure services
builder.Services.AddHealthChecks()
    .AddCheck<DatabaseHealthCheck>("database")
    .AddAzureKeyVault(options => ...)
    .AddAzureServiceBusQueue("connection", "queue");

app.MapHealthChecks("/health");
app.MapHealthChecks("/health/ready", new HealthCheckOptions { Predicate = check => check.Tags.Contains("ready") });
```

**Application Insights**: Automatic telemetry, custom events, dependency tracking, performance counters

**Custom telemetry**:
```csharp
public class UserService
{
    private readonly TelemetryClient _telemetryClient;
    
    public async Task<User> GetUserAsync(int id)
    {
        using var operation = _telemetryClient.StartOperation<RequestTelemetry>("GetUser");
        operation.Telemetry.Properties["UserId"] = id.ToString();
        
        try
        {
            var user = await _repository.GetByIdAsync(id);
            _telemetryClient.TrackEvent("UserRetrieved", new Dictionary<string, string> { ["UserId"] = id.ToString() });
            return user;
        }
        catch (Exception ex)
        {
            _telemetryClient.TrackException(ex);
            throw;
        }
    }
}
```

**Health checks**: Separate `/health` (liveness) and `/health/ready` (readiness) endpoints

**Azure Monitor**: Application Insights automatically sends data to Azure Monitor

---

## 6. Managed Identity

**DefaultAzureCredential** (automatic authentication):
```csharp
using Azure.Identity;

// Automatically uses (in order):
// 1. Environment variables (AZURE_CLIENT_ID, AZURE_CLIENT_SECRET, AZURE_TENANT_ID)
// 2. Managed Identity (when running in Azure)
// 3. Azure CLI (az login)
// 4. Visual Studio / Azure PowerShell

var credential = new DefaultAzureCredential();
var client = new SecretClient(new Uri("https://myvault.vault.azure.net/"), credential);
```

**Service-to-service authentication**:
```csharp
// No secrets needed when using Managed Identity in Azure
var credential = new DefaultAzureCredential();
var token = await credential.GetTokenAsync(
    new TokenRequestContext(new[] { "https://vault.azure.net/.default" }));
```

**Managed Identity in Azure**: Automatically configured for App Service, Function Apps, VM, AKS

**Client secret fallback** (development):
```csharp
var credential = new DefaultAzureCredential(new DefaultAzureCredentialOptions
{
    ExcludeManagedIdentityCredential = false,
    ExcludeEnvironmentCredential = false,
    ExcludeAzureCliCredential = true
});
```

---

> **Note**: Use `DefaultAzureCredential` for all Azure service authentication (no secrets needed in Azure). Managed Identity automatically configured for Azure-hosted services. Application Insights provides automatic telemetry and monitoring. Azure Key Vault for secrets management, Azure App Configuration for settings. Microsoft Fabric integration via REST API with Managed Identity authentication.

