# ASP.NET Core for Azure & Microsoft Fabric

**Focus**: Modern enterprise patterns for Azure/Fabric environments - Minimal APIs, DI, auth, Azure services, EF Core, monitoring.

## Table of Contents

- [1. Minimal APIs & Controllers](#1-minimal-apis--controllers)
- [2. Dependency Injection](#2-dependency-injection)
- [3. Configuration & Azure Key Vault](#3-configuration--azure-key-vault)
- [4. Authentication (Azure AD)](#4-authentication-azure-ad)
- [5. Middleware Pipeline](#5-middleware-pipeline)
- [6. Entity Framework Core](#6-entity-framework-core)
- [7. Azure Services](#7-azure-services)
- [8. Microsoft Fabric Integration](#8-microsoft-fabric-integration)
- [9. Logging & Monitoring](#9-logging--monitoring)
- [10. Error Handling](#10-error-handling)

---

## 1. Minimal APIs & Controllers

**REST - Minimal APIs** (modern default):
```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/users/{id}", async (int id, IUserService service) => 
    await service.GetByIdAsync(id) is { } user ? Results.Ok(user) : Results.NotFound());

app.MapPost("/users", async (CreateUserDto dto, IUserService service, IValidator<CreateUserDto> validator) =>
{
    if (!(await validator.ValidateAsync(dto)).IsValid) return Results.BadRequest();
    var user = await service.CreateAsync(dto);
    return Results.Created($"/users/{user.Id}", user);
});

var usersGroup = app.MapGroup("/api/users").WithTags("Users");
usersGroup.MapGet("/", async (IUserService service) => Results.Ok(await service.GetAllAsync()));
```

**REST - Controllers** (complex scenarios):
```csharp
[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    private readonly IUserService _service;
    public UsersController(IUserService service) => _service = service;

    [HttpGet("{id}")]
    public async Task<ActionResult<User>> GetUser(int id) =>
        await _service.GetByIdAsync(id) is { } user ? Ok(user) : NotFound();

    [HttpPost]
    public async Task<ActionResult<User>> CreateUser([FromBody] CreateUserDto dto)
    {
        var user = await _service.CreateAsync(dto);
        return CreatedAtAction(nameof(GetUser), new { id = user.Id }, user);
    }
}
```

**SOAP - WCF Service** (legacy integration):
```csharp
// Service contract
[ServiceContract]
public interface IUserService
{
    [OperationContract]
    Task<User> GetUserAsync(int id);
    
    [OperationContract]
    Task<User> CreateUserAsync(CreateUserRequest request);
}

// Service implementation
[ServiceBehavior(InstanceContextMode = InstanceContextMode.PerCall)]
public class UserService : IUserService
{
    private readonly IUserRepository _repository;
    public UserService(IUserRepository repository) => _repository = repository;
    
    public async Task<User> GetUserAsync(int id) => await _repository.GetByIdAsync(id);
    public async Task<User> CreateUserAsync(CreateUserRequest request) => 
        await _repository.CreateAsync(new User { Name = request.Name, Email = request.Email });
}

// Program.cs - Add SOAP endpoint
builder.Services.AddServiceModelServices();
builder.Services.AddServiceModelMetadata();
var app = builder.Build();
app.UseServiceModel(serviceBuilder =>
{
    serviceBuilder.AddService<UserService>();
    serviceBuilder.AddServiceEndpoint<UserService, IUserService>(
        new BasicHttpBinding(), "/UserService.svc");
});
```

**GraphQL - Hot Chocolate** (modern GraphQL):
```csharp
// Install: HotChocolate.AspNetCore

// Query type
public class Query
{
    public async Task<User?> GetUser(int id, [Service] IUserService service) =>
        await service.GetByIdAsync(id);
    
    public async Task<List<User>> GetUsers([Service] IUserService service) =>
        await service.GetAllAsync();
}

// Mutation type
public class Mutation
{
    public async Task<User> CreateUser(CreateUserInput input, [Service] IUserService service) =>
        await service.CreateAsync(new CreateUserDto { Name = input.Name, Email = input.Email });
}

// Program.cs - Add GraphQL
builder.Services
    .AddGraphQLServer()
    .AddQueryType<Query>()
    .AddMutationType<Mutation>()
    .AddType<User>()
    .AddAuthorization();

var app = builder.Build();
app.MapGraphQL();  // Endpoint: /graphql
```

**Results**: `Results.Ok()`, `Results.Created()`, `Results.NoContent()`, `Results.NotFound()`, `Results.BadRequest()`, `Results.Problem()`

**Model binding**: `[FromBody]`, `[FromQuery]`, `[FromRoute]`, `[FromHeader]` - Automatic in Minimal APIs

---

## 2. Dependency Injection

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Service lifetimes
builder.Services.AddScoped<IUserService, UserService>();      // Per request
builder.Services.AddSingleton<ICacheService, CacheService>(); // One instance
builder.Services.AddTransient<IEmailService, EmailService>();  // New each time

// Options pattern (Azure config)
builder.Services.Configure<AzureOptions>(builder.Configuration.GetSection("Azure"));
// Inject: IOptions<AzureOptions> (singleton) or IOptionsSnapshot<AzureOptions> (scoped, reloads)

// HttpClientFactory (enterprise pattern)
builder.Services.AddHttpClient<IExternalApiService, ExternalApiService>(client =>
{
    client.BaseAddress = new Uri("https://api.example.com");
    client.Timeout = TimeSpan.FromSeconds(30);
});

// Service implementation
public class UserService : IUserService
{
    private readonly ILogger<UserService> _logger;
    private readonly IUserRepository _repository;
    
    public UserService(ILogger<UserService> logger, IUserRepository repository) =>
        (_logger, _repository) = (logger, repository);
    
    public async Task<User> GetByIdAsync(int id)
    {
        _logger.LogInformation("Getting user {UserId}", id);
        return await _repository.GetByIdAsync(id);
    }
}
```

**Lifetimes**: `AddSingleton` (shared), `AddScoped` (per HTTP request), `AddTransient` (new instance)

**HttpClientFactory**: Prevents socket exhaustion, supports Polly retry policies

---

## 3. Configuration & Azure Key Vault

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

**Precedence**: Command-line > Environment > User secrets (dev) > appsettings.{Environment}.json > appsettings.json

**Azure Key Vault**: Use `Azure.Extensions.AspNetCore.Configuration.Secrets` - Add conditionally (not in dev)

---

## 4. Authentication (Azure AD)

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

---

## 5. Middleware Pipeline

```csharp
// Custom middleware (enterprise pattern)
public class CorrelationIdMiddleware
{
    private readonly RequestDelegate _next;
    public CorrelationIdMiddleware(RequestDelegate next) => _next = next;
    
    public async Task InvokeAsync(HttpContext context)
    {
        var correlationId = context.Request.Headers["X-Correlation-Id"].FirstOrDefault() 
            ?? Guid.NewGuid().ToString();
        context.Items["CorrelationId"] = correlationId;
        context.Response.Headers["X-Correlation-Id"] = correlationId;
        await _next(context);
    }
}

// Program.cs - Middleware order matters
var app = builder.Build();

app.UseExceptionHandler();      // First: catch all exceptions
app.UseHttpsRedirection();
app.UseResponseCompression();    // Compress responses
app.UseStaticFiles();
app.UseRouting();
app.UseCors("AllowSpecificOrigin");
app.UseAuthentication();         // Before authorization
app.UseAuthorization();
app.UseCorrelationId();         // Custom
app.MapControllers();           // or app.MapEndpoints() for Minimal APIs
```

**Order**: Exception handler → HTTPS → Compression → Static files → Routing → CORS → Auth → Authorization → Endpoints

**Built-in**: `UseExceptionHandler()`, `UseHttpsRedirection()`, `UseResponseCompression()`, `UseCors()`, `UseAuthentication()`, `UseAuthorization()`

---

## 6. Entity Framework Core

```csharp
// Program.cs - EF Core with connection resilience
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"),
        sqlOptions => sqlOptions.EnableRetryOnFailure(
            maxRetryCount: 5, 
            maxRetryDelay: TimeSpan.FromSeconds(30), 
            errorNumbersToAdd: null)));

// DbContext
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
    public DbSet<User> Users { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>().HasIndex(u => u.Email).IsUnique();
    }
}

// Repository pattern
public class UserRepository : IUserRepository
{
    private readonly AppDbContext _context;
    public UserRepository(AppDbContext context) => _context = context;
    
    public async Task<User> GetByIdAsync(int id) =>
        await _context.Users
            .AsNoTracking()  // Read-only optimization
            .FirstOrDefaultAsync(u => u.Id == id);
    
    public async Task<User> CreateAsync(User user)
    {
        _context.Users.Add(user);
        await _context.SaveChangesAsync();
        return user;
    }
}
```

**Methods**: `FirstOrDefaultAsync()`, `ToListAsync()`, `AnyAsync()`, `Include()`, `AsNoTracking()` (read-only), `Where()`, `Select()`

**Resilience**: `EnableRetryOnFailure()` for transient SQL failures (Azure SQL)

**Pooling**: `AddDbContextPool<>()` for high-throughput scenarios

**Migrations**: `dotnet ef migrations add Name`, `dotnet ef database update`

---

## 7. Azure Services

```csharp
// Azure Key Vault (secrets)
using Azure.Security.KeyVault.Secrets;
using Azure.Identity;

var credential = new DefaultAzureCredential(); // Managed Identity in Azure
var client = new SecretClient(new Uri("https://myvault.vault.azure.net/"), credential);
var secret = await client.GetSecretAsync("my-secret");

// Azure Storage Blobs
using Azure.Storage.Blobs;

var blobClient = new BlobServiceClient(connectionString);
var container = blobClient.GetBlobContainerClient("container");
var blob = container.GetBlobClient("file.txt");
await blob.UploadAsync(stream);

// Azure Service Bus (messaging)
using Azure.Messaging.ServiceBus;

var client = new ServiceBusClient(connectionString);
var sender = client.CreateSender("queue");
await sender.SendMessageAsync(new ServiceBusMessage(json));

var receiver = client.CreateReceiver("queue");
var message = await receiver.ReceiveMessageAsync();
await receiver.CompleteMessageAsync(message);

// Azure App Configuration (settings)
builder.Configuration.AddAzureAppConfiguration(options =>
    options.Connect(new Uri("https://myappconfig.azconfig.io"), new DefaultAzureCredential()));
```

**Managed Identity**: `DefaultAzureCredential` automatically uses Managed Identity in Azure (no secrets needed)

**Service registration**: Register Azure clients as singletons: `builder.Services.AddSingleton(sp => new SecretClient(...))`

---

## 8. Microsoft Fabric Integration

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

---

## 9. Logging & Monitoring

```csharp
// Structured logging (enterprise pattern)
public class UserService
{
    private readonly ILogger<UserService> _logger;
    public UserService(ILogger<UserService> logger) => _logger = logger;
    
    public async Task<User> GetUserAsync(int id)
    {
        _logger.LogInformation("Getting user {UserId}", id);
        try
        {
            var user = await _repository.GetByIdAsync(id);
            _logger.LogInformation("User {UserId} retrieved successfully", id);
            return user;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error retrieving user {UserId}", id);
            throw;
        }
    }
}

// Program.cs - Application Insights (Azure monitoring)
builder.Services.AddApplicationInsightsTelemetry();

// Health checks (enterprise pattern)
builder.Services.AddHealthChecks()
    .AddCheck<DatabaseHealthCheck>("database")
    .AddAzureKeyVault(options => ...)
    .AddAzureServiceBusQueue("connection", "queue");

app.MapHealthChecks("/health");
app.MapHealthChecks("/health/ready", new HealthCheckOptions { Predicate = check => check.Tags.Contains("ready") });
```

**Structured logging**: Use `{PropertyName}` placeholders for queryable logs

**Application Insights**: Automatic telemetry, custom events, dependency tracking, performance counters

**Health checks**: Separate `/health` (liveness) and `/health/ready` (readiness) endpoints

---

## 10. Error Handling

```csharp
// Global exception handler (enterprise pattern)
public class GlobalExceptionMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<GlobalExceptionMiddleware> _logger;
    
    public GlobalExceptionMiddleware(RequestDelegate next, ILogger<GlobalExceptionMiddleware> logger) =>
        (_next, _logger) = (next, logger);
    
    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Unhandled exception {CorrelationId}", 
                context.Items["CorrelationId"]?.ToString());
            await HandleExceptionAsync(context, ex);
        }
    }
    
    private static Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        var statusCode = exception switch
        {
            ArgumentException => 400,
            UnauthorizedAccessException => 401,
            NotFoundException => 404,
            _ => 500
        };
        
        context.Response.StatusCode = statusCode;
        context.Response.ContentType = "application/json";
        
        var response = new ProblemDetails
        {
            Status = statusCode,
            Title = exception.GetType().Name,
            Detail = exception.Message,
            Instance = context.Request.Path
        };
        
        return context.Response.WriteAsJsonAsync(response);
    }
}

// Program.cs - Problem Details (RFC 7807)
builder.Services.AddProblemDetails();
app.UseExceptionHandler(); // Uses Problem Details automatically
```

**Problem Details**: Standard RFC 7807 error format (automatic with `UseExceptionHandler()`)

**Correlation IDs**: Include in error responses for traceability

---

> **Note**: Modern ASP.NET Core (.NET 6+) uses `Program.cs` with Minimal APIs by default. Enterprise patterns: Managed Identity for Azure services, structured logging with Application Insights, health checks, Problem Details for errors, EF Core with connection resilience. Use `DefaultAzureCredential` for all Azure service authentication (no secrets needed in Azure).

