# Java for Data Integration & Enterprise Backend

**Focus**: Enterprise backend patterns, data integration, I/O, JSON, HTTP, SOAP, concurrency, validation, logging, database access.

## Table of Contents

- [Built-In Syntax](#built-in-syntax)
- [Variables & Basic Types](#variables--basic-types)
- [Operators](#operators)
- [Control Flow](#control-flow)
- [Functions & Methods](#functions--methods)
- [Classes & OOP](#classes--oop)
- [Collections](#collections)
- [Exception Handling](#exception-handling)
- [File I/O](#file-io)
- [JSON Payloads](#json-payloads)
- [HTTP Client](#http-client)
- [SOAP Client](#soap-client)
- [Database Access](#database-access)
- [Concurrency Basics](#concurrency-basics)
- [Logging](#logging)
- [Validation](#validation)
- [Integration Patterns](#integration-patterns)

---

## Built-In Syntax

### Access modifiers
- `public` - accessible from anywhere
- `private` - accessible only within class
- `protected` - accessible within package and subclasses
- `package-private` - accessible within package (default)

### Class/interface keywords
- `class` - define class
- `interface` - define interface
- `abstract` - abstract class/method
- `final` - cannot be extended/overridden
- `static` - belongs to class, not instance
- `extends` - class inheritance
- `implements` - interface implementation

### Method/field modifiers
- `void` - no return value
- `return` - return value from method
- `this` - reference to current object
- `super` - reference to parent class
- `static` - class-level member
- `final` - cannot be reassigned/overridden

### Control flow
- `if` - conditional branch
- `else` - default branch
- `switch` - multi-way branch
- `case` - switch case label
- `default` - switch default case
- `for` - iteration loop
- `while` - loop while condition true
- `do` - do-while loop
- `break` - exit loop/switch
- `continue` - skip to next iteration

### Exception handling
- `try` - start exception handling
- `catch` - catch exception
- `finally` - always execute cleanup
- `throw` - throw exception
- `throws` - declare thrown exceptions

### Other keywords
- `import` - import package/class
- `package` - declare package
- `new` - create object instance
- `instanceof` - type check
- `null` - null reference
- `var` - local variable type inference (Java 10+)
- `record` - immutable data class (Java 14+)

---

## Variables & Basic Types

```java
// Primitive types
int count = 10;                    // 32-bit integer
long id = 1000L;                   // 64-bit integer
double price = 99.99;              // 64-bit floating point
float rate = 0.5f;                 // 32-bit floating point
boolean isValid = true;            // boolean
char letter = 'A';                 // 16-bit Unicode character
byte data = 127;                   // 8-bit signed integer
short value = 32767;               // 16-bit signed integer

// Object types (wrappers)
Integer countObj = 10;             // autoboxing
String name = "Alice";             // immutable string
String nullStr = null;             // null reference

// Type conversion
int x = (int) 3.14;                // explicit cast
double y = 5;                      // implicit widening
String num = String.valueOf(42);   // convert to string
int parsed = Integer.parseInt("42"); // parse from string
```

**Primitive vs Object Types**
- Primitives: `int`, `long`, `double`, `float`, `boolean`, `char`, `byte`, `short`
- Objects: `Integer`, `Long`, `Double`, `Float`, `Boolean`, `Character`, `Byte`, `Short`, `String`
- Autoboxing: automatic conversion between primitives and wrappers

---

## Operators

```java
// Arithmetic
+ - * / % ++ --                    // add, sub, mul, div, modulo, increment, decrement

// Comparison
== != < > <= >=                    // equality, inequality, relational
instanceof                         // type check

// Logical
&& || !                            // logical AND, OR, NOT

// Bitwise
& | ^ ~ << >> >>>                  // AND, OR, XOR, NOT, left shift, right shift, unsigned right shift

// Assignment
= += -= *= /= %=                   // assignment operators

// Ternary
condition ? valueIfTrue : valueIfFalse
```

---

## Control Flow

### If/Else

```java
if (condition) {
    // code
} else if (anotherCondition) {
    // code
} else {
    // code
}

// Ternary operator
int max = (a > b) ? a : b;
```

### Switch (Java 14+)

```java
// Traditional switch
switch (value) {
    case 1:
        // code
        break;
    case 2:
        // code
        break;
    default:
        // code
}

// Switch expression (Java 14+)
String result = switch (value) {
    case 1 -> "one";
    case 2 -> "two";
    default -> "other";
};
```

### For Loops

```java
// Traditional for loop
for (int i = 0; i < 10; i++) {
    System.out.println(i);
}

// Enhanced for loop (for-each)
List<String> items = List.of("a", "b", "c");
for (String item : items) {
    System.out.println(item);
}

// Stream iteration (functional style)
items.forEach(System.out::println);
```

### While Loops

```java
while (condition) {
    // code
}

do {
    // code
} while (condition);
```

---

## Functions & Methods

```java
// Method definition
public static int add(int a, int b) {
    return a + b;
}

// Method overloading
public int sum(int a, int b) { return a + b; }
public int sum(int a, int b, int c) { return a + b + c; }

// Variable arguments
public int sum(int... numbers) {
    int total = 0;
    for (int n : numbers) {
        total += n;
    }
    return total;
}

// Lambda expressions (Java 8+)
Function<Integer, Integer> square = x -> x * x;
Predicate<Integer> isEven = x -> x % 2 == 0;
Consumer<String> printer = s -> System.out.println(s);

// Method references
items.forEach(System.out::println);           // instance method
items.sort(String::compareToIgnoreCase);     // instance method
Function<String, Integer> parser = Integer::parseInt; // static method
```

---

## Classes & OOP

```java
// Class definition
public class User {
    // Fields
    private String name;
    private int age;
    
    // Constructor
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Getter
    public String getName() {
        return name;
    }
    
    // Setter
    public void setName(String name) {
        this.name = name;
    }
    
    // Instance method
    public void greet() {
        System.out.println("Hello, " + name);
    }
    
    // Static method
    public static User createDefault() {
        return new User("Guest", 0);
    }
}

// Records (immutable data classes, Java 14+)
public record UserEvent(String userId, String action, Instant timestamp) {
    // Compact constructor for validation
    public UserEvent {
        if (userId == null || userId.isBlank()) {
            throw new IllegalArgumentException("userId required");
        }
    }
}

// Inheritance
public class Admin extends User {
    private String role;
    
    public Admin(String name, int age, String role) {
        super(name, age);  // call parent constructor
        this.role = role;
    }
    
    @Override
    public void greet() {
        super.greet();  // call parent method
        System.out.println("Role: " + role);
    }
}

// Interface
public interface Service {
    void execute();
    
    // Default method (Java 8+)
    default void log(String message) {
        System.out.println("Log: " + message);
    }
    
    // Static method
    static Service create() {
        return new ServiceImpl();
    }
}

// Abstract class
public abstract class BaseHandler {
    public abstract void handle();
    
    protected void validate() {
        // common validation logic
    }
}
```

---

## Collections

```java
import java.util.*;

// List (ordered, allows duplicates)
List<String> list = new ArrayList<>();       // mutable, resizable
List<String> immutable = List.of("a", "b");  // immutable (Java 9+)
list.add("item");
list.get(0);                                 // access by index
list.size();                                 // length

// Set (unordered, unique elements)
Set<String> set = new HashSet<>();           // hash-based, O(1) lookup
Set<String> ordered = new LinkedHashSet<>(); // insertion order
Set<String> sorted = new TreeSet<>();        // sorted order
set.add("item");
set.contains("item");                        // membership test

// Map (key-value pairs)
Map<String, String> map = new HashMap<>();   // hash-based
Map<String, Integer> ordered = new LinkedHashMap<>(); // insertion order
Map<String, Integer> sorted = new TreeMap<>(); // sorted by key
map.put("key", "value");
map.get("key");                              // get value
map.containsKey("key");                      // check key exists
map.getOrDefault("key", "default");          // get with default

// Optional (explicit null handling)
Optional<String> opt = Optional.ofNullable(maybeNull);
String value = opt.orElse("fallback");       // default if empty
String value2 = opt.orElseGet(() -> computeDefault()); // lazy default
opt.ifPresent(v -> System.out.println(v));   // execute if present
```

**Common Collection Operations**
- `add()`, `remove()`, `contains()` - membership
- `size()`, `isEmpty()`, `clear()` - size/state
- `forEach()` - iteration
- `stream()` - functional operations (Java 8+)

---

## Exception Handling

```java
// Try-catch-finally
try {
    // code that may throw exception
    int result = 10 / 0;
} catch (ArithmeticException e) {
    // handle specific exception
    System.out.println("Division by zero: " + e.getMessage());
} catch (Exception e) {
    // handle general exception
    System.out.println("Error: " + e.getMessage());
} finally {
    // always executes (cleanup)
    System.out.println("Cleanup");
}

// Try-with-resources (auto-close)
try (FileReader fr = new FileReader("file.txt");
     BufferedReader br = new BufferedReader(fr)) {
    String line = br.readLine();
} catch (IOException e) {
    // handle exception
}

// Throw exception
if (value < 0) {
    throw new IllegalArgumentException("Value must be non-negative");
}

// Custom exception
class IntegrationException extends RuntimeException {
    public IntegrationException(String message, Throwable cause) {
        super(message, cause);
    }
}

// Declare thrown exceptions
public void riskyMethod() throws IOException, SQLException {
    // method that may throw checked exceptions
}
```

**Exception Types**
- **Checked**: must be declared (`IOException`, `SQLException`)
- **Unchecked**: `RuntimeException` and subclasses (`IllegalArgumentException`, `NullPointerException`)

---

## File I/O

```java
import java.io.*;
import java.nio.charset.StandardCharsets;
import java.nio.file.*;

// Read file (small files)
Path path = Paths.get("data/input.txt");
String content = Files.readString(path, StandardCharsets.UTF_8);

// Read file line by line (large files)
try (Stream<String> lines = Files.lines(path, StandardCharsets.UTF_8)) {
    lines.filter(line -> !line.isBlank())
         .forEach(System.out::println);
}

// Write file
String data = "Hello, World!";
Files.writeString(path, data, StandardCharsets.UTF_8);

// Write lines
List<String> lines = List.of("line1", "line2", "line3");
Files.write(path, lines, StandardCharsets.UTF_8);

// Traditional I/O (try-with-resources)
try (FileReader fr = new FileReader("file.txt");
     BufferedReader br = new BufferedReader(fr)) {
    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
}

try (FileWriter fw = new FileWriter("output.txt");
     BufferedWriter bw = new BufferedWriter(fw)) {
    bw.write("Hello");
    bw.newLine();
    bw.write("World");
}
```

---

## JSON Payloads

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;

// ObjectMapper setup
class JsonUtil {
    private static final ObjectMapper MAPPER = new ObjectMapper()
        .registerModule(new JavaTimeModule()); // for LocalDateTime, Instant, etc.
    
    // Parse JSON to object
    public static <T> T fromJson(String json, Class<T> cls) {
        try {
            return MAPPER.readValue(json, cls);
        } catch (Exception e) {
            throw new IntegrationException("Invalid JSON payload", e);
        }
    }
    
    // Convert object to JSON
    public static String toJson(Object obj) {
        try {
            return MAPPER.writeValueAsString(obj);
        } catch (Exception e) {
            throw new IntegrationException("JSON serialization failed", e);
        }
    }
}

// DTOs (Data Transfer Objects)
public record UserEvent(String userId, String action, Instant timestamp) {}

// Usage
String json = "{\"userId\":\"123\",\"action\":\"login\",\"timestamp\":\"2024-01-01T00:00:00Z\"}";
UserEvent event = JsonUtil.fromJson(json, UserEvent.class);
String jsonOutput = JsonUtil.toJson(event);
```

---

## HTTP Client

```java
import java.net.URI;
import java.net.http.*;
import java.time.Duration;

class HttpClient {
    private final HttpClient client = HttpClient.newBuilder()
        .connectTimeout(Duration.ofSeconds(10))
        .build();
    
    // GET request
    public String getJson(String url, Map<String, String> headers) {
        try {
            HttpRequest.Builder builder = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .timeout(Duration.ofSeconds(20))
                .GET();
            
            headers.forEach(builder::header);
            
            HttpResponse<String> response = client.send(
                builder.build(),
                HttpResponse.BodyHandlers.ofString()
            );
            
            if (response.statusCode() / 100 != 2) {
                throw new IntegrationException("HTTP " + response.statusCode(), null);
            }
            
            return response.body();
        } catch (Exception e) {
            throw (e instanceof IntegrationException ie) 
                ? ie 
                : new IntegrationException("HTTP call failed", e);
        }
    }
    
    // POST request
    public String postJson(String url, String jsonBody, Map<String, String> headers) {
        try {
            HttpRequest.Builder builder = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .timeout(Duration.ofSeconds(20))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(jsonBody));
            
            headers.forEach(builder::header);
            
            HttpResponse<String> response = client.send(
                builder.build(),
                HttpResponse.BodyHandlers.ofString()
            );
            
            if (response.statusCode() / 100 != 2) {
                throw new IntegrationException("HTTP " + response.statusCode(), null);
            }
            
            return response.body();
        } catch (Exception e) {
            throw new IntegrationException("HTTP POST failed", e);
        }
    }
}
```

---

## SOAP Client

**Note**: Most common approach: generate strongly-typed stubs from WSDL (JAX-WS), then call port methods like normal Java methods.

**Typical generation** (tooling varies by build):
```bash
wsimport -keep -s src/main/java -p com.example.soap https://host/service?wsdl
```

In modern projects you usually use a Maven/Gradle plugin to generate sources.

### Basic JAX-WS Call

```java
import javax.xml.namespace.QName;
import javax.xml.ws.*;
import javax.xml.ws.handler.Handler;
import javax.xml.ws.handler.MessageContext;
import javax.xml.ws.handler.soap.*;
import javax.xml.soap.SOAPMessage;

// Generated classes assumed: MyService, MyPort, Request, Response
class SoapClient {
    private final MyPort port;
    
    public SoapClient(String endpointUrl) {
        MyService service = new MyService();              // generated Service subclass
        this.port = service.getMyPort();                  // generated Port interface
        
        BindingProvider bp = (BindingProvider) port;
        bp.getRequestContext().put(
            BindingProvider.ENDPOINT_ADDRESS_PROPERTY, 
            endpointUrl
        ); // override endpoint
        
        // Timeouts (common JAX-WS RI keys; other stacks may differ)
        bp.getRequestContext().put("com.sun.xml.ws.connect.timeout", 10_000);
        bp.getRequestContext().put("com.sun.xml.ws.request.timeout", 20_000);
        
        // Optional: attach handler chain (for headers / correlation IDs / auth)
        Binding binding = bp.getBinding();
        List<Handler> chain = new ArrayList<>(binding.getHandlerChain());
        chain.add(new SoapHeaderHandler("X-Correlation-Id", UUID.randomUUID().toString()));
        binding.setHandlerChain(chain);
    }
    
    public MyResponse call(MyRequest req) {
        try {
            return port.someOperation(req);                 // strongly-typed SOAP call
        } catch (SOAPFaultException e) {
            // SOAP Fault returned by server (structured error)
            throw new IntegrationException("SOAP fault: " + e.getFault().getFaultString(), e);
        } catch (WebServiceException e) {
            // transport / timeout / TLS / connection failures
            throw new IntegrationException("SOAP transport failed", e);
        }
    }
}
```

### SOAP Header Injection

**Note**: Header name here is illustrative; real SOAP headers often use QNames + namespaces.

```java
class SoapHeaderHandler implements SOAPHandler<SOAPMessageContext> {
    private final String headerName;
    private final String headerValue;
    
    public SoapHeaderHandler(String headerName, String headerValue) {
        this.headerName = headerName;
        this.headerValue = headerValue;
    }
    
    @Override
    public boolean handleMessage(SOAPMessageContext ctx) {
        Boolean outbound = (Boolean) ctx.get(MessageContext.MESSAGE_OUTBOUND_PROPERTY);
        if (Boolean.TRUE.equals(outbound)) {
            try {
                SOAPMessage msg = ctx.getMessage();
                var envelope = msg.getSOAPPart().getEnvelope();
                var header = envelope.getHeader();
                if (header == null) header = envelope.addHeader();
                header.addHeaderElement(new QName(headerName)).addTextNode(headerValue);
                msg.saveChanges();
            } catch (Exception e) {
                throw new IntegrationException("Failed to add SOAP header", e);
            }
        }
        return true;
    }
    
    @Override
    public boolean handleFault(SOAPMessageContext ctx) { return true; }
    @Override
    public void close(MessageContext ctx) {}
    @Override
    public Set<QName> getHeaders() { return Collections.emptySet(); }
}
```

---

## Database Access

```java
import java.sql.*;

class DatabaseService {
    private final Connection connection;  // typically from DataSource/pool
    
    // Parameterized query (prevents SQL injection)
    public int executeUpdate(String sql, List<Object> params) {
        try (PreparedStatement ps = connection.prepareStatement(sql)) {
            for (int i = 0; i < params.size(); i++) {
                ps.setObject(i + 1, params.get(i));  // 1-based indexing
            }
            return ps.executeUpdate();
        } catch (SQLException e) {
            throw new IntegrationException("Database write failed", e);
        }
    }
    
    // Query with result set
    public List<Map<String, Object>> executeQuery(String sql, List<Object> params) {
        List<Map<String, Object>> results = new ArrayList<>();
        try (PreparedStatement ps = connection.prepareStatement(sql);
             ResultSet rs = ps.executeQuery()) {
            
            for (int i = 0; i < params.size(); i++) {
                ps.setObject(i + 1, params.get(i));
            }
            
            ResultSetMetaData meta = rs.getMetaData();
            int columnCount = meta.getColumnCount();
            
            while (rs.next()) {
                Map<String, Object> row = new HashMap<>();
                for (int i = 1; i <= columnCount; i++) {
                    row.put(meta.getColumnName(i), rs.getObject(i));
                }
                results.add(row);
            }
        } catch (SQLException e) {
            throw new IntegrationException("Database query failed", e);
        }
        return results;
    }
    
    // Transaction management
    public void executeInTransaction(Runnable operations) {
        try {
            connection.setAutoCommit(false);
            operations.run();
            connection.commit();
        } catch (SQLException e) {
            try {
                connection.rollback();
            } catch (SQLException rollbackEx) {
                throw new IntegrationException("Rollback failed", rollbackEx);
            }
            throw new IntegrationException("Transaction failed", e);
        } finally {
            try {
                connection.setAutoCommit(true);
            } catch (SQLException e) {
                throw new IntegrationException("Failed to restore auto-commit", e);
            }
        }
    }
}
```

---

## Concurrency Basics

```java
import java.util.concurrent.*;

class ConcurrencyService {
    // ExecutorService for parallel work
    private final ExecutorService executor = Executors.newFixedThreadPool(8);
    
    // Submit task with timeout
    public <T> T executeWithTimeout(Callable<T> task, long timeout, TimeUnit unit) {
        Future<T> future = executor.submit(task);
        try {
            return future.get(timeout, unit);  // timeout to avoid hung pipelines
        } catch (TimeoutException e) {
            future.cancel(true);
            throw new IntegrationException("Task timed out", e);
        } catch (Exception e) {
            throw new IntegrationException("Task execution failed", e);
        }
    }
    
    // Parallel processing
    public <T> List<T> processInParallel(List<Callable<T>> tasks) {
        try {
            List<Future<T>> futures = executor.invokeAll(tasks);
            List<T> results = new ArrayList<>();
            for (Future<T> future : futures) {
                results.add(future.get());
            }
            return results;
        } catch (Exception e) {
            throw new IntegrationException("Parallel processing failed", e);
        }
    }
    
    // Shutdown executor
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

---

## Logging

**Note**: In production: SLF4J + Logback/Log4j2. Always log correlation IDs (requestId/eventId).

```java
import java.util.logging.Logger;

class Service {
    private static final Logger LOG = Logger.getLogger(Service.class.getName());
    
    public void processRequest(String requestId, String data) {
        LOG.info(() -> "Processing request id=" + requestId);
        
        try {
            // business logic
            validate(data);
            process(data);
            
            LOG.info(() -> "Request completed id=" + requestId);
        } catch (IllegalArgumentException e) {
            LOG.warning(() -> "Invalid request id=" + requestId + " error=" + e.getMessage());
            throw e;
        } catch (Exception e) {
            LOG.severe(() -> "Request failed id=" + requestId + " error=" + e.getMessage());
            throw new IntegrationException("Processing failed", e);
        }
    }
    
    private void validate(String data) {
        if (data == null || data.isBlank()) {
            throw new IllegalArgumentException("Data required");
        }
    }
    
    private void process(String data) {
        // processing logic
    }
}
```

---

## Validation

```java
class ValidationUtil {
    // Fail fast at boundaries: API input, events, config
    public static void requireNonBlank(String value, String fieldName) {
        if (value == null || value.isBlank()) {
            throw new IllegalArgumentException(fieldName + " is required");
        }
    }
    
    public static void requireNonNull(Object value, String fieldName) {
        if (value == null) {
            throw new IllegalArgumentException(fieldName + " cannot be null");
        }
    }
    
    public static void requirePositive(int value, String fieldName) {
        if (value <= 0) {
            throw new IllegalArgumentException(fieldName + " must be positive");
        }
    }
    
    public static void requireInRange(int value, int min, int max, String fieldName) {
        if (value < min || value > max) {
            throw new IllegalArgumentException(
                fieldName + " must be between " + min + " and " + max
            );
        }
    }
    
    // Validate DTO
    public static void validateUserEvent(UserEvent event) {
        requireNonBlank(event.userId(), "userId");
        requireNonBlank(event.action(), "action");
        requireNonNull(event.timestamp(), "timestamp");
    }
}
```

---

## Integration Patterns

### Minimal Integration Handler Skeleton

```java
class IntegrationHandler {
    private final HttpClient httpClient = new HttpClient();
    private final SoapClient soapClient = new SoapClient("https://soap-host/service");
    private final DatabaseService dbService = new DatabaseService(connection);
    private static final Logger LOG = Logger.getLogger(IntegrationHandler.class.getName());
    
    // HTTP -> parse -> validate -> persist
    public void handleHttpRequest(String url) {
        String correlationId = UUID.randomUUID().toString();
        LOG.info(() -> "HTTP start url=" + url + " correlationId=" + correlationId);
        
        try {
            // 1. Fetch data
            String json = httpClient.getJson(url, Map.of(
                "Accept", "application/json",
                "X-Correlation-Id", correlationId
            ));
            
            // 2. Parse
            UserEvent event = JsonUtil.fromJson(json, UserEvent.class);
            
            // 3. Validate
            ValidationUtil.validateUserEvent(event);
            
            // 4. Persist
            dbService.executeUpdate(
                "INSERT INTO events (user_id, action, timestamp) VALUES (?, ?, ?)",
                List.of(event.userId(), event.action(), event.timestamp())
            );
            
            LOG.info(() -> "HTTP done userId=" + event.userId() + " correlationId=" + correlationId);
        } catch (Exception e) {
            LOG.severe(() -> "HTTP failed correlationId=" + correlationId + " error=" + e.getMessage());
            throw e;
        }
    }
    
    // SOAP -> validate -> persist
    public void handleSoapRequest(MyRequest req) {
        String correlationId = UUID.randomUUID().toString();
        LOG.info(() -> "SOAP start op=someOperation correlationId=" + correlationId);
        
        try {
            // 1. Call SOAP service
            MyResponse resp = soapClient.call(req);
            
            // 2. Validate response
            if (resp.getStatus() == null) {
                throw new IntegrationException("Invalid SOAP response");
            }
            
            // 3. Persist
            dbService.executeUpdate(
                "INSERT INTO soap_responses (status, timestamp) VALUES (?, ?)",
                List.of(resp.getStatus(), Instant.now())
            );
            
            LOG.info(() -> "SOAP done status=" + resp.getStatus() + " correlationId=" + correlationId);
        } catch (Exception e) {
            LOG.severe(() -> "SOAP failed correlationId=" + correlationId + " error=" + e.getMessage());
            throw e;
        }
    }
}
```
