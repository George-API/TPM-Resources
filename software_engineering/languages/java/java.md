# Java for Data Integration & Enterprise Backend

**Focus**: Enterprise backend patterns, data integration, I/O, JSON, HTTP, SOAP, concurrency, validation, logging, database access.

## Table of Contents

- [Type System & Memory](#type-system--memory)
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

## Type System & Memory

**Primitives** (value types, stack-allocated): `int`, `long`, `double`, `float`, `boolean`, `char`, `byte`, `short` - Stored on stack, copied by value, no null (unless wrapper class)

**Objects** (reference types, heap-allocated): `String`, `Integer`, `Object`, `class`, `array`, `interface` - Stored on heap, copied by reference, can be `null`

**Memory management**: Automatic garbage collection (GC) - No manual memory management like C/C++. GC automatically reclaims unused heap memory when objects are no longer referenced

**Stack vs Heap**: Primitives and object references go on stack; actual objects go on heap. References point to heap objects

**Autoboxing/Unboxing**: Automatic conversion between primitives and wrapper classes - `int x = 5; Integer obj = x;` (autoboxing), `int y = obj;` (unboxing) - Performance cost, avoid in hot paths

**Pass by value**: Java always passes by value - For primitives, the value is copied. For objects, the reference is copied (not the object itself)

**String immutability**: `String` is immutable - `str += "text"` creates new string object. Use `StringBuilder` for multiple concatenations

**Null safety**: Objects can be `null` - Always check for null before accessing: `if (obj != null) { obj.method(); }` or use `Optional` (Java 8+)

**Compilation**: Java compiles to bytecode (.class files), runs on JVM (Java Virtual Machine) - JIT compilation to native code at runtime

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
- `instanceof` - type check / pattern matching (Java 16+)
- `null` - null reference
- `var` - local variable type inference (Java 10+)
- `record` - immutable data class (Java 14+)
- `sealed` - restrict which classes can extend/implement (Java 17+)

---

## Variables & Basic Types

- **Primitives**: `int` (32-bit), `long` (64-bit, suffix `L`), `double` (64-bit), `float` (32-bit, suffix `f`), `boolean`, `char` (16-bit Unicode), `byte` (8-bit), `short` (16-bit)
- **Objects**: `Integer`, `Long`, `Double`, `Float`, `Boolean`, `Character`, `Byte`, `Short`, `String` (immutable, UTF-16)
- **Autoboxing**: Automatic conversion between primitives and wrappers - `int x = 5; Integer obj = x;` (automatic)
- **Type conversion**: `(int) 3.14` (explicit cast), `double y = 5` (implicit widening), `String.valueOf(42)`, `Integer.parseInt("42")` (throws on failure), `Integer.parseInt("42", 10)` (with radix)
- **Type checking**: `obj instanceof String`, `obj instanceof String s` (pattern matching, Java 16+), `obj.getClass() == String.class`

---

## Operators

- **Arithmetic**: `+ - * / % ++ --`
- **Comparison**: `== != < > <= >=`, `instanceof` (type check)
- **Logical**: `&& || !`
- **Bitwise**: `& | ^ ~ << >> >>>` (unsigned right shift)
- **Assignment**: `= += -= *= /= %=`
- **Ternary**: `condition ? valueIfTrue : valueIfFalse`

---

## Control Flow

- **If/Else**: `if (cond) { } else if (cond) { } else { }`
- **Ternary**: `int max = (a > b) ? a : b;`
- **Switch**: `switch (val) { case 1: break; default: break; }` (traditional), `String result = switch (val) { case 1 -> "one"; default -> "other"; };` (expression, Java 14+)
- **Pattern matching in switch** (Java 17+): `switch (obj) { case String s -> s.length(); case Integer i -> i * 2; default -> 0; }`
- **For**: `for (int i = 0; i < 10; i++) { }` (traditional), `for (String item : items) { }` (enhanced/for-each), `items.forEach(System.out::println);` (stream, Java 8+)
- **While**: `while (cond) { }`, `do { } while (cond);` (executes at least once)

---

## Functions & Methods

- **Definition**: `public static int add(int a, int b) { return a + b; }`
- **Overloading**: Same method name, different parameters (different signature)
- **Varargs**: `public int sum(int... numbers) { }` (variable arguments, treated as array)
- **Lambdas** (Java 8+): `Function<Integer, Integer> square = x -> x * x;`, `Predicate<Integer> isEven = x -> x % 2 == 0;`, `Consumer<String> printer = s -> System.out.println(s);` - Anonymous functions, used with functional interfaces
- **Method references**: `items.forEach(System.out::println);` (instance method), `items.sort(String::compareToIgnoreCase);` (instance method), `Integer::parseInt` (static method), `String::new` (constructor)
- **Functional interfaces**: `Function<T, R>`, `Predicate<T>`, `Consumer<T>`, `Supplier<T>` - Single abstract method interfaces for lambdas

---

## Classes & OOP

- **Class** (reference type): `public class User { private String name; public User(String name) { this.name = name; } public String getName() { return name; } }` - Heap-allocated, can be null
- **Records** (Java 14+): `public record UserEvent(String userId, String action, Instant timestamp) { }` (immutable data class, auto-generates constructor, getters, equals, hashCode, toString), `public record Point(int x, int y) { public Point { if (x < 0) throw new IllegalArgumentException(); } }` (compact constructor for validation)
- **Inheritance**: `public class Admin extends User { public Admin(...) { super(...); } @Override public void greet() { super.greet(); } }` - Single inheritance only (unlike C++)
- **Interface**: `public interface Service { void execute(); default void log(String msg) { } static Service create() { } }` (default/static methods, Java 8+), `public interface Service { void execute(); }` (can have multiple implementations)
- **Abstract class**: `public abstract class BaseHandler { public abstract void handle(); protected void validate() { } }` - Cannot instantiate, can have implementation, use when sharing code
- **Sealed classes** (Java 17+): `public sealed class Shape permits Circle, Rectangle { }` - Restrict which classes can extend

---

## Collections (`java.util.*`)

- **List** (ordered, duplicates): `List<String> list = new ArrayList<>();` (mutable, dynamic array), `List.of("a", "b")` (immutable, Java 9+), `new LinkedList<>()` (doubly-linked list); `add()`, `get(i)`, `size()`, `remove()`
- **Set** (unique): `Set<String> set = new HashSet<>();` (hash, O(1) average), `new LinkedHashSet<>()` (insertion order), `new TreeSet<>()` (sorted, O(log n)); `add()`, `contains()`, `remove()`
- **Map** (key-value): `Map<String, String> map = new HashMap<>();` (hash, O(1) average), `new LinkedHashMap<>()` (insertion order), `new TreeMap<>()` (sorted, O(log n)); `put()`, `get()`, `containsKey()`, `getOrDefault()`, `keySet()`, `values()`, `entrySet()`
- **Optional** (Java 8+): `Optional.ofNullable(val)`, `opt.orElse("default")`, `opt.orElseGet(() -> compute())`, `opt.ifPresent(v -> ...)`, `opt.map(x -> x.toUpperCase())` - Avoid null, use for return values that may be absent
- **Streams** (Java 8+): `list.stream().filter(x -> x > 0).map(x -> x * 2).collect(Collectors.toList())` - Functional operations on collections, lazy evaluation
- **Operations**: `add()`, `remove()`, `contains()`, `size()`, `isEmpty()`, `clear()`, `forEach()`, `stream()` (Java 8+)

---

## Exception Handling

- **Try-catch-finally**: `try { } catch (SpecificException e) { } catch (Exception e) { } finally { }` (finally always executes, even if return/throw)
- **Try-with-resources**: `try (FileReader fr = new FileReader("file.txt"); BufferedReader br = new BufferedReader(fr)) { }` (auto-close, implements `AutoCloseable`) - Prevents resource leaks
- **Throw**: `throw new IllegalArgumentException("message");`
- **Custom**: `class IntegrationException extends RuntimeException { public IntegrationException(String msg, Throwable cause) { super(msg, cause); } }` - Extend `RuntimeException` for unchecked, `Exception` for checked
- **Declare**: `public void method() throws IOException, SQLException { }` - Must declare checked exceptions
- **Types**: **Checked** (must declare/catch: `IOException`, `SQLException`), **Unchecked** (`RuntimeException` subclasses: `IllegalArgumentException`, `NullPointerException`, `IllegalStateException`) - Unchecked don't need declaration
- **Exception chaining**: `throw new IntegrationException("Failed", e);` - Preserve original exception as cause

---

## File I/O (`java.nio.file.*`, `java.io.*`)

- **Read** (small): `String content = Files.readString(Paths.get("file.txt"), StandardCharsets.UTF_8);`
- **Read** (large, line-by-line): `try (Stream<String> lines = Files.lines(path, StandardCharsets.UTF_8)) { lines.forEach(...); }`
- **Write**: `Files.writeString(path, data, StandardCharsets.UTF_8);`, `Files.write(path, lines, StandardCharsets.UTF_8);`
- **Traditional**: `try (FileReader fr = new FileReader("file.txt"); BufferedReader br = new BufferedReader(fr)) { while ((line = br.readLine()) != null) { } }`
- **Write** (traditional): `try (FileWriter fw = new FileWriter("out.txt"); BufferedWriter bw = new BufferedWriter(fw)) { bw.write("text"); bw.newLine(); }`

---

## JSON Payloads (Jackson)

- **Setup**: `ObjectMapper MAPPER = new ObjectMapper().registerModule(new JavaTimeModule());` (for `LocalDateTime`, `Instant`)
- **Parse**: `public static <T> T fromJson(String json, Class<T> cls) { return MAPPER.readValue(json, cls); }`
- **Serialize**: `public static String toJson(Object obj) { return MAPPER.writeValueAsString(obj); }`
- **DTO**: `public record UserEvent(String userId, String action, Instant timestamp) {}`
- **Usage**: `UserEvent event = JsonUtil.fromJson(json, UserEvent.class); String json = JsonUtil.toJson(event);`

---

## HTTP Client (`java.net.http.*`)

- **Setup**: `HttpClient client = HttpClient.newBuilder().connectTimeout(Duration.ofSeconds(10)).build();`
- **GET**: `HttpRequest req = HttpRequest.newBuilder().uri(URI.create(url)).timeout(Duration.ofSeconds(20)).GET().build(); HttpResponse<String> resp = client.send(req, HttpResponse.BodyHandlers.ofString());` (check `statusCode() / 100 != 2` for errors)
- **POST**: `HttpRequest req = HttpRequest.newBuilder().uri(URI.create(url)).header("Content-Type", "application/json").POST(HttpRequest.BodyPublishers.ofString(jsonBody)).build();`
- **Headers**: `headers.forEach(builder::header);`
- **Error handling**: Check status code, throw `IntegrationException` on failure

---

## SOAP Client (JAX-WS)

**Note**: Generate strongly-typed stubs from WSDL (`wsimport` or Maven/Gradle plugin), then call port methods.

- **Setup**: `MyService service = new MyService(); MyPort port = service.getMyPort(); BindingProvider bp = (BindingProvider) port;`
- **Endpoint**: `bp.getRequestContext().put(BindingProvider.ENDPOINT_ADDRESS_PROPERTY, endpointUrl);`
- **Timeouts**: `bp.getRequestContext().put("com.sun.xml.ws.connect.timeout", 10_000); bp.getRequestContext().put("com.sun.xml.ws.request.timeout", 20_000);`
- **Headers**: Attach handler chain: `binding.setHandlerChain(chain);` with `SOAPHandler<SOAPMessageContext>` implementation
- **Call**: `MyResponse resp = port.someOperation(req);` (catch `SOAPFaultException`, `WebServiceException`)
- **Header handler**: Implement `handleMessage()` to add headers: `header.addHeaderElement(new QName(name)).addTextNode(value);`

---

## Database Access (`java.sql.*`)

- **Update** (parameterized): `try (PreparedStatement ps = connection.prepareStatement(sql)) { for (int i = 0; i < params.size(); i++) { ps.setObject(i + 1, params.get(i)); } return ps.executeUpdate(); }` (1-based indexing)
- **Query**: `try (PreparedStatement ps = connection.prepareStatement(sql); ResultSet rs = ps.executeQuery()) { while (rs.next()) { Map<String, Object> row = new HashMap<>(); for (int i = 1; i <= meta.getColumnCount(); i++) { row.put(meta.getColumnName(i), rs.getObject(i)); } results.add(row); } }`
- **Transaction**: `connection.setAutoCommit(false); operations.run(); connection.commit();` (catch: `rollback()`, finally: `setAutoCommit(true)`)
- **Note**: Use `PreparedStatement` to prevent SQL injection; connection typically from DataSource/pool

---

## Concurrency Basics (`java.util.concurrent.*`)

- **Executor**: `ExecutorService executor = Executors.newFixedThreadPool(8);` - Thread pool for managing concurrent tasks
- **Virtual threads** (Java 21+): `try (var executor = Executors.newVirtualThreadPerTaskExecutor()) { executor.submit(() -> task()); }` - Lightweight threads, good for I/O-bound tasks
- **Submit with timeout**: `Future<T> future = executor.submit(task); return future.get(timeout, unit);` (catch `TimeoutException`, cancel future with `future.cancel(true)`)
- **Parallel**: `List<Future<T>> futures = executor.invokeAll(tasks); for (Future<T> f : futures) { results.add(f.get()); }`
- **CompletableFuture** (Java 8+): `CompletableFuture.supplyAsync(() -> compute()).thenApply(x -> x * 2).thenAccept(System.out::println);` - Async composition
- **Shutdown**: `executor.shutdown(); if (!executor.awaitTermination(60, SECONDS)) { executor.shutdownNow(); }` - Always shutdown executors to prevent resource leaks

---

## Logging

**Note**: Production: SLF4J + Logback/Log4j2. Always log correlation IDs (requestId/eventId).

- **Setup**: `private static final Logger LOG = Logger.getLogger(Service.class.getName());`
- **Logging**: `LOG.info(() -> "message id=" + requestId);`, `LOG.warning(() -> "error id=" + id + " error=" + e.getMessage());`, `LOG.severe(() -> "failed id=" + id);`
- **Pattern**: Log at start/end of operations, include correlation ID, log exceptions with context

---

## Validation

**Fail fast at boundaries**: API input, events, config.

- **Helpers**: `requireNonBlank(String value, String fieldName)`, `requireNonNull(Object value, String fieldName)`, `requirePositive(int value, String fieldName)`, `requireInRange(int value, int min, int max, String fieldName)`
- **Pattern**: `if (value == null || value.isBlank()) { throw new IllegalArgumentException(fieldName + " is required"); }`
- **DTO validation**: `validateUserEvent(UserEvent event) { requireNonBlank(event.userId(), "userId"); requireNonNull(event.timestamp(), "timestamp"); }`

---

## Integration Patterns

**Minimal Integration Handler Skeleton**

- **HTTP flow**: Fetch → Parse → Validate → Persist
  - `String correlationId = UUID.randomUUID().toString(); LOG.info(() -> "HTTP start correlationId=" + correlationId);`
  - `String json = httpClient.getJson(url, Map.of("X-Correlation-Id", correlationId));`
  - `UserEvent event = JsonUtil.fromJson(json, UserEvent.class); ValidationUtil.validateUserEvent(event);`
  - `dbService.executeUpdate("INSERT INTO events (...) VALUES (?, ?, ?)", List.of(...));`
  - `LOG.info(() -> "HTTP done correlationId=" + correlationId);` (catch: log error, rethrow)
- **SOAP flow**: Call → Validate → Persist
  - `MyResponse resp = soapClient.call(req); if (resp.getStatus() == null) { throw new IntegrationException(...); }`
  - `dbService.executeUpdate("INSERT INTO soap_responses (...) VALUES (?, ?)", List.of(...));`
  - Log with correlation ID at start/end/error

---

> **Note**: Java is a managed language (automatic GC, no manual memory management) with strong typing and platform independence (write once, run anywhere). Primitives are stack-allocated and copied by value. Objects are heap-allocated and accessed via references. Java compiles to bytecode (.class files) and runs on JVM (Java Virtual Machine) with JIT compilation. Use `PreparedStatement` for database operations (prevents SQL injection), prefer `Optional` over null, use streams for collection operations (Java 8+), and leverage records for immutable data (Java 14+). For enterprise backend/integration: always use connection pooling, set timeouts on network operations, log correlation IDs, validate at boundaries, and handle exceptions appropriately.
