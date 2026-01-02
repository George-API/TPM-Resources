# Spring Boot for Enterprise Applications

**Focus**: Core Spring Boot syntax and patterns - REST APIs, DI, security, JPA, testing, configuration.

## Table of Contents

- [1. REST Controllers](#1-rest-controllers)
- [2. SOAP Web Services](#2-soap-web-services)
- [3. GraphQL](#3-graphql)
- [4. Dependency Injection](#4-dependency-injection)
- [5. Configuration](#5-configuration)
- [6. Security](#6-security)
- [7. Data Access (JPA)](#7-data-access-jpa)
- [8. Exception Handling](#8-exception-handling)
- [9. Testing](#9-testing)
- [10. Actuator](#10-actuator)

---

## 1. REST Controllers

**REST Controller**:
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    private final UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        return userService.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    @PostMapping
    public ResponseEntity<User> createUser(@Valid @RequestBody CreateUserDto dto) {
        User user = userService.create(dto);
        return ResponseEntity.created(URI.create("/api/users/" + user.getId())).body(user);
    }
}
```

**Request mapping**: `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`

**Parameters**: `@PathVariable`, `@RequestParam`, `@RequestBody`, `@RequestHeader`, `@CookieValue`

**Response entity**: `ResponseEntity.ok()`, `ResponseEntity.created()`, `ResponseEntity.noContent()`, `ResponseEntity.notFound()`, `ResponseEntity.badRequest()`

**Validation**:
```java
public record CreateUserDto(
    @NotBlank @Size(min = 2, max = 100) String name,
    @Email @NotBlank String email,
    @Min(18) @Max(120) Integer age
) {}
```

**Validation annotations**: `@NotNull`, `@NotBlank`, `@NotEmpty`, `@Size`, `@Min`, `@Max`, `@Email`, `@Pattern`, `@Valid`

---

## 2. SOAP Web Services

**Dependency**: `spring-boot-starter-web-services`

**Endpoint**:
```java
@Endpoint
public class UserEndpoint {
    private final UserService userService;
    
    public UserEndpoint(UserService userService) {
        this.userService = userService;
    }
    
    @PayloadRoot(namespace = "http://example.com/users", localPart = "GetUserRequest")
    @ResponsePayload
    public GetUserResponse getUser(@RequestPayload GetUserRequest request) {
        User user = userService.findById(request.getId())
            .orElseThrow(() -> new UserNotFoundException("User not found"));
        
        GetUserResponse response = new GetUserResponse();
        response.setUser(mapToUserDto(user));
        return response;
    }
    
    @PayloadRoot(namespace = "http://example.com/users", localPart = "CreateUserRequest")
    @ResponsePayload
    public CreateUserResponse createUser(@RequestPayload CreateUserRequest request) {
        User user = userService.create(mapToUser(request));
        CreateUserResponse response = new CreateUserResponse();
        response.setUser(mapToUserDto(user));
        return response;
    }
}
```

**Configuration**:
```java
@Configuration
@EnableWs
public class WebServiceConfig extends WsConfigurerAdapter {
    @Bean
    public ServletRegistrationBean<MessageDispatcherServlet> messageDispatcherServlet(ApplicationContext context) {
        MessageDispatcherServlet servlet = new MessageDispatcherServlet();
        servlet.setApplicationContext(context);
        servlet.setTransformWsdlLocations(true);
        return new ServletRegistrationBean<>(servlet, "/ws/*");
    }
    
    @Bean(name = "users")
    public DefaultWsdl11Definition defaultWsdl11Definition(XsdSchema userSchema) {
        DefaultWsdl11Definition wsdl = new DefaultWsdl11Definition();
        wsdl.setPortTypeName("UsersPort");
        wsdl.setLocationUri("/ws");
        wsdl.setTargetNamespace("http://example.com/users");
        wsdl.setSchema(userSchema);
        return wsdl;
    }
    
    @Bean
    public XsdSchema userSchema() {
        return new SimpleXsdSchema(new ClassPathResource("users.xsd"));
    }
}
```

**WSDL generation**: Auto-generated from XSD schema, accessible at `/ws/users.wsdl`

---

## 3. GraphQL

**Dependency**: `graphql-spring-boot-starter`, `graphql-java-tools`

**Query resolver**:
```java
@Component
public class UserQuery implements GraphQLQueryResolver {
    private final UserService userService;
    
    public UserQuery(UserService userService) {
        this.userService = userService;
    }
    
    public User getUser(Long id) {
        return userService.findById(id).orElseThrow();
    }
    
    public List<User> getUsers() {
        return userService.findAll();
    }
}
```

**Mutation resolver**:
```java
@Component
public class UserMutation implements GraphQLMutationResolver {
    private final UserService userService;
    
    public UserMutation(UserService userService) {
        this.userService = userService;
    }
    
    public User createUser(String name, String email, Integer age) {
        return userService.create(new CreateUserDto(name, email, age));
    }
    
    public User updateUser(Long id, String name) {
        return userService.update(id, new UpdateUserDto(name));
    }
}
```

**Schema** (`schema.graphqls`):
```graphql
type Query {
    getUser(id: ID!): User
    getUsers: [User!]!
}

type Mutation {
    createUser(name: String!, email: String!, age: Int!): User!
    updateUser(id: ID!, name: String!): User!
}

type User {
    id: ID!
    name: String!
    email: String!
    age: Int!
}
```

**Configuration**:
```yaml
graphql:
  servlet:
    enabled: true
    mapping: /graphql
    cors-enabled: true
```

**Endpoint**: `/graphql` (POST), GraphQL Playground at `/graphql-playground` (if enabled)

---

## 4. Dependency Injection

**Service layer**:
```java
@Service
@Transactional
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    @Transactional(readOnly = true)
    public Optional<User> findById(Long id) {
        return userRepository.findById(id);
    }
}
```

**Repository**:
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
    
    @Query("SELECT u FROM User u WHERE u.age >= :minAge")
    List<User> findAdults(@Param("minAge") int minAge);
}
```

**Component annotations**: `@Service`, `@Repository`, `@Component`, `@Controller`, `@RestController`

**Constructor injection** (preferred): `public UserService(UserRepository repo) { this.repo = repo; }`

**Qualifier**:
```java
@Autowired
@Qualifier("primary")
private UserService userService;
```

**Configuration**:
```java
@Configuration
public class AppConfig {
    @Bean
    public RestTemplate restTemplate(RestTemplateBuilder builder) {
        return builder.setConnectTimeout(Duration.ofSeconds(10)).build();
    }
}
```

---

## 5. Configuration

**Application YAML** (`application.yml`):
```yaml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/mydb
    username: ${DB_USERNAME:user}
    password: ${DB_PASSWORD:password}
  jpa:
    hibernate:
      ddl-auto: validate
logging:
  level:
    com.example: INFO
```

**Profile-specific** (`application-prod.yml`):
```yaml
spring:
  profiles:
    active: prod
  datasource:
    url: ${DATABASE_URL}
```

**Configuration properties**:
```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    @NotBlank
    private String apiKey;
    
    private Duration timeout = Duration.ofSeconds(30);
}
```

**Property precedence**: Command line > Environment variables > `application-{profile}.yml` > `application.yml`

---

## 6. Security

**Security configuration**:
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/public/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults())
            .csrf(csrf -> csrf.disable());
        return http.build();
    }
}
```

**JWT filter**:
```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                   HttpServletResponse response,
                                   FilterChain filterChain) {
        String token = extractToken(request);
        if (token != null && tokenProvider.validateToken(token)) {
            // Set authentication
        }
        filterChain.doFilter(request, response);
    }
}
```

**Method-level security**: `@PreAuthorize("hasRole('ADMIN')")`, `@PreAuthorize("#id == authentication.principal.id")`

---

## 7. Data Access (JPA)

**Entity**:
```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String name;
    
    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<Order> orders = new ArrayList<>();
}
```

**JPA annotations**: `@Entity`, `@Table`, `@Id`, `@GeneratedValue`, `@Column`, `@OneToMany`, `@ManyToOne`, `@OneToOne`, `@ManyToMany`

**Repository**:
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
    
    @Query("SELECT u FROM User u WHERE u.age >= :minAge")
    List<User> findAdults(@Param("minAge") int minAge);
}
```

**Transaction management**:
```java
@Service
@Transactional
public class UserService {
    @Transactional(readOnly = true)
    public Optional<User> findById(Long id) { }
    
    @Transactional(rollbackFor = Exception.class)
    public User create(CreateUserDto dto) { }
}
```

**Pagination**:
```java
Pageable pageable = PageRequest.of(page, size, Sort.by(sortBy));
Page<User> users = userRepository.findAll(pageable);
```

---

## 8. Exception Handling

**Global exception handler**:
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(EntityNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(EntityNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse("NOT_FOUND", ex.getMessage()));
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> errors = ex.getBindingResult().getFieldErrors().stream()
            .collect(Collectors.toMap(FieldError::getField, FieldError::getDefaultMessage));
        return ResponseEntity.status(HttpStatus.BAD_REQUEST)
            .body(new ErrorResponse("VALIDATION_ERROR", "Validation failed", errors));
    }
}
```

**Error response**:
```java
public record ErrorResponse(String code, String message, Map<String, String> errors) {
    public ErrorResponse(String code, String message) {
        this(code, message, null);
    }
}
```

---

## 9. Testing

**Unit test**:
```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository userRepository;
    
    @InjectMocks
    private UserService userService;
    
    @Test
    void shouldCreateUser() {
        when(userRepository.save(any(User.class))).thenReturn(savedUser);
        User result = userService.create(dto);
        assertThat(result.getId()).isEqualTo(1L);
        verify(userRepository).save(any(User.class));
    }
}
```

**Integration test**:
```java
@SpringBootTest
@AutoConfigureMockMvc
@Transactional
class UserControllerIntegrationTest {
    @Autowired
    private MockMvc mockMvc;
    
    @Test
    void shouldCreateUser() throws Exception {
        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(dto)))
            .andExpect(status().isCreated());
    }
}
```

**Test slices**: `@WebMvcTest`, `@DataJpaTest`, `@JsonTest`, `@RestClientTest`

---

## 10. Actuator

**Configuration**:
```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      show-details: when-authorized
```

**Endpoints**: `/actuator/health`, `/actuator/info`, `/actuator/metrics`, `/actuator/prometheus`

**Custom health indicator**:
```java
@Component
public class DatabaseHealthIndicator implements HealthIndicator {
    @Override
    public Health health() {
        // Check database connection
        return Health.up().withDetail("database", "Available").build();
    }
}
```

---

> **Note**: Spring Boot provides convention-over-configuration for rapid enterprise development. Use constructor injection, `@Transactional` at service layer, `@RestControllerAdvice` for exception handling, and Actuator for monitoring. Spring Boot auto-configuration reduces boilerplate while maintaining flexibility.
