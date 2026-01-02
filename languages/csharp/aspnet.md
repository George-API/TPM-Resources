# ASP.NET Core

**Focus**: Core ASP.NET Core patterns and concepts - Minimal APIs, Controllers, DI, middleware, EF Core, authentication, logging, error handling.

## Table of Contents

- [1. Minimal APIs & Controllers](#1-minimal-apis--controllers)
- [2. Dependency Injection](#2-dependency-injection)
- [3. Configuration](#3-configuration)
- [4. Authentication & Authorization](#4-authentication--authorization)
- [5. Middleware Pipeline](#5-middleware-pipeline)
- [6. CORS](#6-cors)
- [7. API Versioning](#7-api-versioning)
- [8. Rate Limiting](#8-rate-limiting)
- [9. Validation](#9-validation)
- [10. Caching](#10-caching)
- [11. Swagger/OpenAPI](#11-swaggeropenapi)
- [12. Entity Framework Core](#12-entity-framework-core)
- [13. Logging](#13-logging)
- [14. Error Handling](#14-error-handling)

---

## 1. Minimal APIs & Controllers

**Minimal APIs** (modern default):
```csharp
var app = WebApplication.CreateBuilder(args).Build();

app.MapGet("/users/{id}", async (int id, IUserService service) => 
    await service.GetByIdAsync(id) is { } user ? Results.Ok(user) : Results.NotFound());
// Pattern matching: `is { }` checks for non-null (see csharp.md)

app.MapPost("/users", async (CreateUserDto dto, IUserService service) =>
    await service.CreateAsync(dto) is { } user ? Results.Created($"/users/{user.Id}", user) : Results.BadRequest());

var group = app.MapGroup("/api/users").WithTags("Users");
```

**Controllers** (complex scenarios):
```csharp
[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    [HttpGet("{id}")]
    public async Task<ActionResult<User>> GetUser(int id) =>
        await _service.GetByIdAsync(id) is { } user ? Ok(user) : NotFound();
}
```

**Request mapping**: `@HttpGet`, `@HttpPost`, `@HttpPut`, `@HttpDelete`, `@HttpPatch`

**Results**: `Results.Ok()`, `Results.Created()`, `Results.NoContent()`, `Results.NotFound()`, `Results.BadRequest()`, `Results.Problem()`

**Model binding**: `[FromBody]`, `[FromQuery]`, `[FromRoute]`, `[FromHeader]` - Automatic in Minimal APIs

**Validation**: `[Valid]` attribute triggers validation, `ModelState.IsValid` checks validation state

**SOAP (WCF)**: `[ServiceContract]`, `[OperationContract]`, `[ServiceBehavior]` - Legacy integration pattern

**GraphQL (Hot Chocolate)**: `AddGraphQLServer()`, `AddQueryType<T>()`, `AddMutationType<T>()`, `MapGraphQL()` - Endpoint: `/graphql`

---

## 2. Dependency Injection

**Service registration**:
```csharp
builder.Services.AddScoped<IUserService, UserService>();      // Per request
builder.Services.AddSingleton<ICacheService, CacheService>(); // One instance
builder.Services.AddTransient<IEmailService, EmailService>();  // New each time
```

**Lifetimes**: `AddSingleton` (shared), `AddScoped` (per HTTP request), `AddTransient` (new instance)

**Options pattern**:
```csharp
builder.Services.Configure<AppOptions>(builder.Configuration.GetSection("App"));
// Inject: IOptions<AppOptions> (singleton) or IOptionsSnapshot<AppOptions> (scoped, reloads)
```

**HttpClientFactory** (enterprise pattern):
```csharp
builder.Services.AddHttpClient<IExternalApiService, ExternalApiService>(client =>
{
    client.BaseAddress = new Uri("https://api.example.com");
    client.Timeout = TimeSpan.FromSeconds(30);
});
```

**Constructor injection** (preferred): Dependencies injected via constructor, framework manages lifecycle

**HttpClientFactory benefits**: Prevents socket exhaustion, supports Polly retry policies, manages connection pooling

---

## 3. Configuration

**Configuration sources** (automatic): `appsettings.json`, `appsettings.{Environment}.json`, environment variables, command-line

**Precedence**: Command-line > Environment > User secrets (dev) > appsettings.{Environment}.json > appsettings.json

**Access**: `builder.Configuration["ConnectionStrings:DefaultConnection"]`

**Options pattern**:
```csharp
public class AppOptions { public string ApiKey { get; set; } }
builder.Services.Configure<AppOptions>(builder.Configuration.GetSection("App"));
```

**Configuration files**: JSON-based (`appsettings.json`), supports nested objects, environment-specific overrides

---

## 4. Authentication & Authorization

**JWT Bearer authentication**:
```csharp
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.Authority = "https://auth.example.com";
        options.Audience = "api";
        options.TokenValidationParameters = new TokenValidationParameters { ... };
    });
```

**Authorization policies**:
```csharp
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("AdminOnly", policy => policy.RequireRole("Admin"));
    options.AddPolicy("RequireScope", policy => policy.RequireClaim("scope", "api.read"));
});
```

**Authorization attributes**: `[Authorize]`, `[Authorize(Roles = "Admin")]`, `[Authorize(Policy = "RequireScope")]`

**Claims access**: `User.Claims`, `User.FindFirst(ClaimTypes.NameIdentifier)?.Value`, `User.IsInRole("Admin")`

**Cookie authentication**: `AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme).AddCookie()`

**Authentication vs Authorization**: Authentication (who you are), Authorization (what you can do)

---

## 5. Middleware Pipeline

**Middleware concept**: Request pipeline components that process HTTP requests/responses in sequence

**Custom middleware**:
```csharp
public class CorrelationIdMiddleware
{
    private readonly RequestDelegate _next;
    public async Task InvokeAsync(HttpContext context)
    {
        context.Items["CorrelationId"] = Guid.NewGuid().ToString();
        await _next(context);
    }
}
```

**Middleware order** (critical): Exception handler → HTTPS → Compression → Static files → Routing → CORS → Auth → Authorization → Endpoints

**Built-in middleware**: `UseExceptionHandler()`, `UseHttpsRedirection()`, `UseResponseCompression()`, `UseCors()`, `UseAuthentication()`, `UseAuthorization()`

**Registration**: `app.UseMiddleware<CorrelationIdMiddleware>()` or extension method `app.UseCorrelationId()`

**Request pipeline**: Each middleware can short-circuit (not call `_next`) or modify request/response

---

## 6. CORS

**CORS configuration**:
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowSpecificOrigin", policy =>
    {
        policy.WithOrigins("https://example.com")
              .AllowAnyMethod()
              .AllowAnyHeader()
              .AllowCredentials();
    });
});

app.UseCors("AllowSpecificOrigin");
```

**CORS policy options**: `WithOrigins()`, `AllowAnyOrigin()`, `AllowAnyMethod()`, `AllowAnyHeader()`, `AllowCredentials()`

**CORS middleware order**: Must be after `UseRouting()`, before `UseAuthorization()`

**Use cases**: Allow frontend applications to call APIs from different origins, required for browser-based clients

---

## 7. API Versioning

**API versioning** (package: `Microsoft.AspNetCore.Mvc.Versioning`):
```csharp
builder.Services.AddApiVersioning(options =>
{
    options.DefaultApiVersion = new ApiVersion(1, 0);
    options.AssumeDefaultVersionWhenUnspecified = true;
    options.ReportApiVersions = true;
});

// URL-based versioning
[ApiVersion("1.0")]
[Route("api/v{version:apiVersion}/[controller]")]
public class UsersController : ControllerBase { }

// Header-based versioning
[ApiVersion("2.0")]
[Route("api/[controller]")]
public class UsersV2Controller : ControllerBase { }
```

**Versioning strategies**: URL path (`/api/v1/users`), Query string (`?api-version=1.0`), Header (`api-version: 1.0`)

**Version selection**: `[ApiVersion("1.0")]`, `[MapToApiVersion("2.0")]` - Multiple versions per controller

---

## 8. Rate Limiting

**Rate limiting** (.NET 7+):
```csharp
builder.Services.AddRateLimiter(options =>
{
    options.GlobalLimiter = PartitionedRateLimiter.Create<HttpContext, string>(context =>
        RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: context.User.Identity?.Name ?? context.Request.Headers.Host.ToString(),
            factory: partition => new FixedWindowRateLimiterOptions
            {
                AutoReplenishment = true,
                PermitLimit = 100,
                Window = TimeSpan.FromMinutes(1)
            }));
});

app.UseRateLimiter();
```

**Rate limiter types**: `FixedWindowRateLimiter`, `SlidingWindowRateLimiter`, `TokenBucketRateLimiter`, `ConcurrencyLimiter`

**Endpoint-specific limits**:
```csharp
app.MapGet("/api/users", [EnableRateLimiting("CustomPolicy")] () => { });
```

**Rate limiting policies**: Configure named policies, apply per endpoint or globally

---

## 9. Validation

**Data annotations** (records for DTOs - see csharp.md for record syntax):
```csharp
public record CreateUserDto(
    [Required] [StringLength(100)] string Name,
    [Required] [EmailAddress] string Email,
    [Range(18, 120)] int Age
) {}
```

**Validation attributes**: `[Required]`, `[StringLength]`, `[Range]`, `[EmailAddress]`, `[RegularExpression]`, `[Compare]`, `[Url]`, `[Phone]`

**FluentValidation** (advanced):
```csharp
public class CreateUserDtoValidator : AbstractValidator<CreateUserDto>
{
    public CreateUserDtoValidator()
    {
        RuleFor(x => x.Email).EmailAddress().NotEmpty();
        RuleFor(x => x.Age).InclusiveBetween(18, 120);
    }
}

builder.Services.AddScoped<IValidator<CreateUserDto>, CreateUserDtoValidator>();
```

**Validation in Minimal APIs**: `[Valid]` attribute, `Results.ValidationProblem(ModelState)` for errors

**Custom validation**: `IValidatableObject`, custom validation attributes (see csharp.md for attribute syntax), `ValidationAttribute`

---

## 10. Caching

**Response caching**:
```csharp
builder.Services.AddResponseCaching();
app.UseResponseCaching();

[ResponseCache(Duration = 60, VaryByQueryKeys = new[] { "page" })]
[HttpGet]
public IActionResult GetUsers(int page) { }
```

**Output caching** (.NET 7+):
```csharp
builder.Services.AddOutputCache();
app.UseOutputCache();

app.MapGet("/users", [OutputCache(Duration = 60)] () => { });
```

**Cache attributes**: `[ResponseCache]`, `[OutputCache]` - Control caching behavior per endpoint

**Cache invalidation**: Tag-based invalidation, time-based expiration, cache keys

**Memory cache**:
```csharp
builder.Services.AddMemoryCache();
// Inject IMemoryCache for programmatic caching
```

---

## 11. Swagger/OpenAPI

**Swagger/OpenAPI** (API documentation):
```csharp
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen(options =>
{
    options.SwaggerDoc("v1", new OpenApiInfo 
    { 
        Title = "My API", 
        Version = "v1" 
    });
    options.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
    {
        Type = SecuritySchemeType.Http,
        Scheme = "bearer"
    });
});

app.UseSwagger();
app.UseSwaggerUI();
```

**Swagger endpoints**: `/swagger` (JSON), `/swagger/ui` (UI) - Auto-generated from controllers/Minimal APIs

**OpenAPI annotations**: `[ProducesResponseType]`, `[Produces]`, `[Consumes]`, `[SwaggerOperation]` - Enhance documentation

**API documentation**: Auto-generated from code, includes request/response schemas, authentication requirements

---

## 12. Entity Framework Core

**DbContext registration**:
```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(connectionString, sqlOptions => 
        sqlOptions.EnableRetryOnFailure(maxRetryCount: 5)));
```

**DbContext pattern**:
```csharp
public class AppDbContext : DbContext
{
    public DbSet<User> Users { get; set; }
    protected override void OnModelCreating(ModelBuilder modelBuilder) { }
}
```

**Repository pattern**: Abstraction over DbContext, encapsulates data access logic

**Query methods**: `FirstOrDefaultAsync()`, `ToListAsync()`, `AnyAsync()`, `Include()` (eager loading), `AsNoTracking()` (read-only)

**Resilience**: `EnableRetryOnFailure()` for transient SQL failures

**Pooling**: `AddDbContextPool<>()` for high-throughput scenarios (reuses DbContext instances)

**Migrations**: `dotnet ef migrations add Name`, `dotnet ef database update`

**Change tracking**: EF Core tracks entity changes automatically, `SaveChangesAsync()` persists changes

---

## 13. Logging

**Structured logging** (ASP.NET Core ILogger<T>):
```csharp
_logger.LogInformation("Getting user {UserId}", id);
_logger.LogError(ex, "Error retrieving user {UserId}", id);
```

**Log levels**: `Trace`, `Debug`, `Information`, `Warning`, `Error`, `Critical`

**Structured logging**: Use `{PropertyName}` placeholders for queryable logs (not string interpolation) - Framework automatically formats

**Health checks**:
```csharp
builder.Services.AddHealthChecks().AddCheck<DatabaseHealthCheck>("database");
app.MapHealthChecks("/health");
app.MapHealthChecks("/health/ready", new HealthCheckOptions { ... });
```

**Health check endpoints**: `/health` (liveness), `/health/ready` (readiness) - Kubernetes/container orchestration

**Logging providers**: Console, Debug, EventSource, Application Insights (Azure), Serilog, NLog

---

## 14. Error Handling

**Global exception handler** (middleware pattern):
```csharp
public class GlobalExceptionMiddleware
{
    public async Task InvokeAsync(HttpContext context)
    {
        try { await _next(context); }
        catch (Exception ex) { await HandleExceptionAsync(context, ex); }
    }
}
// Exception handling syntax covered in csharp.md
```

**Problem Details** (RFC 7807):
```csharp
builder.Services.AddProblemDetails();
app.UseExceptionHandler(); // Uses Problem Details automatically
```

**Exception mapping**: Map exceptions to HTTP status codes (400 Bad Request, 401 Unauthorized, 404 Not Found, 500 Internal Server Error)

**Correlation IDs**: Include in error responses for traceability across distributed systems

**Error response format**: Standard Problem Details format with `status`, `title`, `detail`, `instance`

**Exception handling strategy**: Catch at middleware level, log with context, return standardized error format

---

> **Note**: Modern ASP.NET Core (.NET 6+) uses `Program.cs` with Minimal APIs by default. Core patterns: constructor injection for DI, middleware pipeline for cross-cutting concerns, Options pattern for configuration, structured logging, Problem Details for errors, EF Core for data access. Use HttpClientFactory for HTTP clients, health checks for monitoring, and repository pattern for data access abstraction.
