# Java Enterprise Patterns & Best Practices

**Focus**: Essential enterprise patterns for Java backend/integration development - supplements core syntax reference.

## Table of Contents

- [Error Handling Patterns](#error-handling-patterns)
- [Resource Management](#resource-management)
- [Concurrency Patterns](#concurrency-patterns)
- [API Design Patterns](#api-design-patterns)
- [Data Access Patterns](#data-access-patterns)
- [Integration Patterns](#integration-patterns)
- [Best Practices](#best-practices)

---

## Error Handling Patterns

### Exception Wrapping
```java
public class IntegrationException extends RuntimeException {
    public IntegrationException(String message, Throwable cause) {
        super(message, cause);
    }
}

// Wrap checked exceptions
try {
    // operation
} catch (IOException e) {
    throw new IntegrationException("Failed to process file", e);
}
```

### Retry Pattern
```java
public class RetryUtil {
    public static <T> T retry(Supplier<T> operation, int maxAttempts, Duration delay) {
        Exception lastException = null;
        for (int i = 0; i < maxAttempts; i++) {
            try {
                return operation.get();
            } catch (Exception e) {
                lastException = e;
                if (i < maxAttempts - 1) {
                    Thread.sleep(delay.toMillis());
                }
            }
        }
        throw new RuntimeException("Operation failed after " + maxAttempts + " attempts", lastException);
    }
}
```

### Circuit Breaker Pattern
```java
public class CircuitBreaker {
    private enum State { CLOSED, OPEN, HALF_OPEN }
    private State state = State.CLOSED;
    private int failureCount = 0;
    private final int failureThreshold = 5;
    private final Duration timeout = Duration.ofSeconds(60);
    private Instant lastFailureTime;
    
    public <T> T execute(Supplier<T> operation) {
        if (state == State.OPEN) {
            if (Duration.between(lastFailureTime, Instant.now()).compareTo(timeout) > 0) {
                state = State.HALF_OPEN;
            } else {
                throw new RuntimeException("Circuit breaker is OPEN");
            }
        }
        
        try {
            T result = operation.get();
            if (state == State.HALF_OPEN) {
                state = State.CLOSED;
                failureCount = 0;
            }
            return result;
        } catch (Exception e) {
            failureCount++;
            lastFailureTime = Instant.now();
            if (failureCount >= failureThreshold) {
                state = State.OPEN;
            }
            throw e;
        }
    }
}
```

---

## Resource Management

### Connection Pooling
```java
public class DatabaseService {
    private final DataSource dataSource;
    
    public void executeUpdate(String sql, List<Object> params) {
        try (Connection conn = dataSource.getConnection();
             PreparedStatement ps = conn.prepareStatement(sql)) {
            // Set parameters and execute
        } catch (SQLException e) {
            throw new RuntimeException("Database error", e);
        }
    }
}
```

### Resource Cleanup
```java
// Always use try-with-resources
try (FileReader fr = new FileReader("file.txt");
     BufferedReader br = new BufferedReader(fr)) {
    // Use resources
} // Auto-closed

// Custom resource
public class ManagedResource implements AutoCloseable {
    @Override
    public void close() {
        // Cleanup
    }
}
```

---

## Concurrency Patterns

### Thread Pool Pattern
```java
public class TaskExecutor {
    private final ExecutorService executor;
    
    public TaskExecutor(int threadPoolSize) {
        this.executor = Executors.newFixedThreadPool(threadPoolSize);
    }
    
    public <T> CompletableFuture<T> submit(Callable<T> task) {
        return CompletableFuture.supplyAsync(() -> {
            try {
                return task.call();
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }, executor);
    }
    
    public void shutdown() {
        executor.shutdown();
        try {
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                executor.shutdownNow();
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
            Thread.currentThread().interrupt();
        }
    }
}
```

### CompletableFuture Patterns
```java
// Chain operations
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> fetchData())
    .thenApply(data -> process(data))
    .exceptionally(ex -> handleError(ex));

// Combine futures
CompletableFuture<String> future1 = fetchData1();
CompletableFuture<String> future2 = fetchData2();
CompletableFuture<String> combined = future1
    .thenCombine(future2, (r1, r2) -> r1 + r2);

// Wait for all
CompletableFuture.allOf(future1, future2).join();
```

---

## API Design Patterns

### Service Layer Pattern
```java
public interface UserService {
    User createUser(CreateUserRequest request);
    User getUser(String userId);
}

public class UserServiceImpl implements UserService {
    private final UserRepository repository;
    private final ValidationService validator;
    
    @Override
    public User createUser(CreateUserRequest request) {
        validator.validate(request);
        User user = new User(request.name(), request.email());
        return repository.save(user);
    }
}
```

### Repository Pattern
```java
public interface UserRepository {
    User save(User user);
    Optional<User> findById(String id);
    List<User> findAll();
}

public class UserRepositoryImpl implements UserRepository {
    private final DataSource dataSource;
    // Implementation
}
```

### DTO Pattern
```java
// Use records for DTOs (Java 14+)
public record CreateUserRequest(String name, String email) {
    public CreateUserRequest {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("Name is required");
        }
    }
}

// Convert between entity and DTO
public class UserMapper {
    public static UserDto toDto(User user) {
        return new UserDto(user.getId(), user.getName(), user.getEmail());
    }
}
```

---

## Data Access Patterns

### Transaction Management
```java
public class TransactionalService {
    private final DataSource dataSource;
    
    public void executeInTransaction(List<Runnable> operations) {
        try (Connection conn = dataSource.getConnection()) {
            conn.setAutoCommit(false);
            try {
                operations.forEach(Runnable::run);
                conn.commit();
            } catch (Exception e) {
                conn.rollback();
                throw new RuntimeException("Transaction failed", e);
            } finally {
                conn.setAutoCommit(true);
            }
        } catch (SQLException e) {
            throw new RuntimeException("Database error", e);
        }
    }
}
```

### Batch Operations
```java
public void batchInsert(List<User> users) {
    String sql = "INSERT INTO users (id, name, email) VALUES (?, ?, ?)";
    try (Connection conn = dataSource.getConnection();
         PreparedStatement ps = conn.prepareStatement(sql)) {
        for (User user : users) {
            ps.setString(1, user.getId());
            ps.setString(2, user.getName());
            ps.setString(3, user.getEmail());
            ps.addBatch();
        }
        ps.executeBatch();
    }
}
```

### Pagination Pattern
```java
public record Page<T>(List<T> content, int page, int size, long total) {
    public int totalPages() {
        return (int) Math.ceil((double) total / size);
    }
}

public Page<User> getUsers(int page, int size) {
    int offset = page * size;
    String sql = "SELECT * FROM users LIMIT ? OFFSET ?";
    List<User> users = fetchUsers(sql, size, offset);
    long total = countUsers();
    return new Page<>(users, page, size, total);
}
```

---

## Integration Patterns

### HTTP Integration Flow
```java
public class HttpIntegrationService {
    private final HttpClient httpClient;
    private final ObjectMapper mapper;
    
    public UserEvent fetchAndProcess(String url) {
        String correlationId = UUID.randomUUID().toString();
        LOG.info("HTTP start correlationId={}", correlationId);
        
        try {
            // Fetch
            String json = httpClient.getJson(url, Map.of("X-Correlation-Id", correlationId));
            
            // Parse
            UserEvent event = mapper.readValue(json, UserEvent.class);
            
            // Validate
            ValidationUtil.validateUserEvent(event);
            
            // Persist
            dbService.executeUpdate("INSERT INTO events (...) VALUES (?, ?, ?)", 
                List.of(event.userId(), event.action(), event.timestamp()));
            
            LOG.info("HTTP done correlationId={}", correlationId);
            return event;
        } catch (Exception e) {
            LOG.error("HTTP failed correlationId={}", correlationId, e);
            throw new IntegrationException("HTTP integration failed", e);
        }
    }
}
```

### SOAP Integration Flow
```java
public class SoapIntegrationService {
    private final MyPort soapPort;
    
    public MyResponse callSoapService(MyRequest request) {
        String correlationId = UUID.randomUUID().toString();
        LOG.info("SOAP start correlationId={}", correlationId);
        
        try {
            // Call
            MyResponse resp = soapPort.someOperation(request);
            
            // Validate
            if (resp.getStatus() == null) {
                throw new IntegrationException("Invalid SOAP response");
            }
            
            // Persist
            dbService.executeUpdate("INSERT INTO soap_responses (...) VALUES (?, ?)", 
                List.of(correlationId, resp.getStatus()));
            
            LOG.info("SOAP done correlationId={}", correlationId);
            return resp;
        } catch (SOAPFaultException | WebServiceException e) {
            LOG.error("SOAP failed correlationId={}", correlationId, e);
            throw new IntegrationException("SOAP integration failed", e);
        }
    }
}
```

### Builder Pattern (for Complex Requests)
```java
public class RequestBuilder {
    private String url;
    private Map<String, String> headers = new HashMap<>();
    
    public RequestBuilder url(String url) {
        this.url = url;
        return this;
    }
    
    public RequestBuilder header(String key, String value) {
        this.headers.put(key, value);
        return this;
    }
    
    public HttpRequest build() {
        var builder = HttpRequest.newBuilder().uri(URI.create(url));
        headers.forEach(builder::header);
        return builder.build();
    }
}
```

---

## Best Practices

### Logging
```java
// Structured logging with correlation IDs
LOG.info("Processing request userId={} requestId={}", userId, requestId);

// Log exceptions with context
catch (Exception e) {
    LOG.error("Failed to process userId={} requestId={}", userId, requestId, e);
    throw new IntegrationException("Processing failed", e);
}
```

### Validation
```java
// Validate at boundaries (API, events, config)
public void processRequest(CreateUserRequest request) {
    requireNonBlank(request.name(), "name");
    requireNonNull(request.email(), "email");
    // Process
}

// Helper methods
private void requireNonBlank(String value, String fieldName) {
    if (value == null || value.isBlank()) {
        throw new IllegalArgumentException(fieldName + " is required");
    }
}
```

### Exception Handling
- **Catch specific exceptions** first, then general
- **Wrap checked exceptions** in runtime exceptions for cleaner code
- **Include context** in exception messages (correlation IDs, user IDs)
- **Log exceptions** at appropriate level with full context
- **Don't swallow exceptions** - either handle or rethrow

### Resource Management
- **Always use try-with-resources** for AutoCloseable
- **Use connection pooling** (DataSource) instead of creating connections
- **Set timeouts** on all network operations
- **Clean up resources** in finally blocks

### Concurrency
- **Use ExecutorService** instead of creating threads directly
- **Shutdown executors** properly (shutdown, awaitTermination, shutdownNow)
- **Use CompletableFuture** for async operations
- **Avoid shared mutable state** - use immutable objects
- **Use thread-safe collections** (ConcurrentHashMap, CopyOnWriteArrayList)

### Security
- **Never log sensitive data** (passwords, tokens, PII)
- **Use parameterized queries** (PreparedStatement) to prevent SQL injection
- **Validate all inputs** at API boundaries
- **Use HTTPS** for all external communications
- **Store secrets** in environment variables or secure vaults

### Performance
- **Use StringBuilder** for string concatenation in loops
- **Prefer streams** for collection operations (Java 8+)
- **Batch database operations** instead of individual calls
- **Use connection pooling** for database connections
- **Profile before optimizing** - measure, don't guess

### Code Organization
```java
// Package structure
com.example
  ├── model          // Entities, DTOs
  ├── repository     // Data access
  ├── service        // Business logic
  ├── integration   // External integrations
  ├── config         // Configuration
  └── util           // Utilities
```
