# Spring Boot Syllabus for Backend Developers
## Core Framework & REST APIs (2026 Industry Standards)

---

## Overview

This syllabus covers **Core Spring Boot** concepts essential for backend developers building REST APIs and web services. It focuses on what you actually use in production, day-to-day.

**Excluded (separate tracks):**
- Microservices (Spring Cloud, service-to-service communication)
- Database/JPA/Hibernate (covered separately)
- System design (covered separately)
- Reactive programming (WebFlux) — covered separately

**Included (this track):**
- Spring Framework fundamentals (IoC, DI, Beans)
- Spring Boot auto-configuration and starters
- REST API development (Controllers, request/response handling)
- Spring Security and JWT authentication
- Validation and error handling
- Configuration management
- Logging and monitoring basics
- Testing (Unit and Integration)
- Performance optimization

**Target:** Junior/Associate Java Developer roles in India (TCS, Infosys, Cognizant, Wipro, startups, MNCs)

**Time Allocation:** 40–60 hours focused study + 30 hours hands-on coding

**Outcome:** You'll build production-grade REST APIs, secure them, handle errors properly, test thoroughly, and deploy confidently.

---

## Interview Frequency Statistics

**Asked in 95%+ of backend Java interviews:**
- Spring Boot basics and auto-configuration
- Dependency injection and Beans
- REST API development (@RestController, @RequestMapping)
- Spring Security basics
- Exception handling and validation

**Asked in 80%+ of interviews:**
- Request/response handling (@RequestBody, @ResponseEntity)
- Spring configuration (application.properties/yml)
- Controllers, Services, Repositories pattern
- Testing (unit and integration)

**Asked in 60%+ of interviews:**
- Custom configurations and properties
- Actuator and monitoring
- Interceptors and filters
- AOP (Aspect-Oriented Programming)

**Asked in 40%+ of interviews:**
- Advanced security (OAuth2, custom filters)
- Caching with Spring
- Event-driven architecture (Spring Events)
- Custom starters and conventions

---

## Priority Breakdown

### TIER 1: CRITICAL ⭐⭐⭐ (95%+ interview frequency)
**Time allocation: 40% of study time**
- Spring Boot fundamentals
- REST API development
- Spring Security (JWT authentication)
- Dependency Injection & Beans
- Exception Handling & Validation

### TIER 2: IMPORTANT ⭐⭐ (60–80% interview frequency)
**Time allocation: 45% of study time**
- Configuration management
- Testing (unit and integration)
- Request/Response handling
- Interceptors and Filters
- Logging

### TIER 3: NICE-TO-KNOW ⭐ (<60% interview frequency)
**Time allocation: 15% of study time**
- AOP (Aspect-Oriented Programming)
- Custom configurations
- Actuator and monitoring
- Caching basics
- Spring Events

---

# TIER 1: CRITICAL TOPICS ⭐⭐⭐

## Module 1: Spring Framework & Dependency Injection Fundamentals (8 hours)

### 1.1 Inversion of Control (IoC) & Dependency Injection (DI)

**Core Concepts:**
- **IoC (Inversion of Control):** Framework manages object creation and lifecycle, not the application
- **DI (Dependency Injection):** Framework injects dependencies into objects instead of objects creating them
- **Container:** Spring ApplicationContext manages beans and dependencies
- **Bean:** Object managed by Spring container

**Why It Matters:**
- Loose coupling: Classes don't create their dependencies
- Testability: Easy to inject mock objects
- Flexibility: Can swap implementations without changing code

**Key Points:**
- Spring handles object creation and assembly
- Dependencies declared, not instantiated
- Reduces boilerplate and increases flexibility
- Makes code testable and maintainable

**Hands-On:**
```java
// Without DI (tightly coupled)
class OrderService {
  private PaymentService paymentService = new PaymentService();
  // Hard to test; can't use mock PaymentService
}

// With DI (loosely coupled)
class OrderService {
  private PaymentService paymentService;
  
  @Autowired
  public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
  }
}
// Easy to test; inject mock in tests
```

**Interview Questions:**
1. What is IoC and why is it useful?
2. What is dependency injection?
3. What is the difference between IoC and DI?
4. Why does Spring use IoC?
5. What are the benefits of DI?

---

### 1.2 Spring Beans & Bean Lifecycle

**Core Concepts:**
- **Bean:** A Java object managed by Spring container
- **Bean Definition:** Metadata describing how to create and configure a bean
- **Bean Scope:** How long bean lives (singleton, prototype, request, session)
- **Bean Lifecycle:** Creation → initialization → in-use → destruction

**Bean Scopes:**
- **Singleton:** One instance per application (default, most common)
- **Prototype:** New instance each time requested
- **Request:** New instance per HTTP request (web apps)
- **Session:** New instance per HTTP session (web apps)

**Bean Lifecycle:**
1. Instantiation: Spring creates bean instance
2. Set properties: Dependency injection happens
3. @PostConstruct: Custom initialization method
4. Bean in use: Application uses bean
5. @PreDestroy: Custom cleanup method
6. Destruction: Bean destroyed when application stops

**Hands-On:**
```java
@Component
public class MyBean {
  
  @PostConstruct
  public void init() {
    System.out.println("Bean created and initialized");
    // Initialize resources (DB connection, cache, etc.)
  }
  
  @PreDestroy
  public void cleanup() {
    System.out.println("Bean being destroyed");
    // Close resources
  }
}
```

**Interview Questions:**
1. What is a Spring Bean?
2. What are bean scopes? Explain each one.
3. What is the default bean scope?
4. What is the bean lifecycle?
5. When are @PostConstruct and @PreDestroy called?
6. Difference between singleton and prototype scope?
7. How do you create a bean?
8. What is @Component, @Service, @Repository, @Controller?

---

### 1.3 Spring Container & ApplicationContext

**Core Concepts:**
- **BeanFactory:** Low-level container; creates and manages beans
- **ApplicationContext:** Higher-level container; extends BeanFactory
- **Auto-configuration:** Spring boots configures itself based on classpath
- **Component Scanning:** Automatically detects beans in packages

**ApplicationContext Features:**
- Bean lifecycle management
- Dependency injection
- Event handling
- Resource loading
- Internationalization (i18n)

**Hands-On:**
```java
// Get ApplicationContext
ApplicationContext context = new ClassPathXmlApplicationContext("config.xml");
MyBean bean = context.getBean(MyBean.class);

// Or in Spring Boot (automatic)
@SpringBootApplication
public class Application {
  public static void main(String[] args) {
    SpringApplication.run(Application.class, args);
    // ApplicationContext created automatically
  }
}

// Access ApplicationContext in bean
@Component
public class MyComponent {
  @Autowired
  ApplicationContext context; // Can access other beans
}
```

**Interview Questions:**
1. What is ApplicationContext?
2. Difference between BeanFactory and ApplicationContext?
3. How does component scanning work?
4. What is auto-configuration?
5. What is @SpringBootApplication?

---

### 1.4 Dependency Injection Methods

**Three Ways to Inject Dependencies:**

**1. Constructor Injection (PREFERRED):**
```java
@Component
public class UserService {
  private final UserRepository repository;
  
  @Autowired
  public UserService(UserRepository repository) {
    this.repository = repository;
  }
}
// Advantages: Immutability, testability, explicit dependencies
```

**2. Setter Injection:**
```java
@Component
public class UserService {
  private UserRepository repository;
  
  @Autowired
  public void setRepository(UserRepository repository) {
    this.repository = repository;
  }
}
// Disadvantages: Mutable, can be null, optional dependencies unclear
```

**3. Field Injection:**
```java
@Component
public class UserService {
  @Autowired
  private UserRepository repository;
}
// Disadvantages: Harder to test (reflection needed), can't see dependencies
```

**Why Constructor Injection is Best:**
- Dependencies are immutable (final)
- All dependencies visible in constructor
- Easy to test (pass mocks to constructor)
- Fails fast if dependency missing

**Interview Questions:**
1. What are the ways to inject dependencies?
2. Why is constructor injection preferred?
3. What is the difference between field and constructor injection?
4. How do you make dependencies immutable?
5. Why is setter injection problematic?

---

## Module 2: Spring Boot Fundamentals & Auto-Configuration (6 hours)

### 2.1 Spring Boot Starters & Auto-Configuration

**Core Concepts:**
- **Starters:** Pre-configured dependency groups (spring-boot-starter-web, spring-boot-starter-security)
- **Auto-Configuration:** Spring Boot automatically configures beans based on classpath
- **@SpringBootApplication:** Combines @Configuration, @ComponentScan, @EnableAutoConfiguration
- **Convention over Configuration:** Default behavior matches most use cases

**Common Starters:**
- `spring-boot-starter-web`: REST APIs, embedded Tomcat
- `spring-boot-starter-security`: Spring Security
- `spring-boot-starter-validation`: Bean validation
- `spring-boot-starter-logging`: Logging (Logback)
- `spring-boot-starter-test`: Testing (JUnit, Mockito)

**Auto-Configuration Example:**
```xml
<!-- pom.xml -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Spring Boot automatically:
- Adds DispatcherServlet
- Configures embedded Tomcat
- Sets up Jackson for JSON serialization
- Configures common web beans

**Hands-On:**
```java
@SpringBootApplication
public class Application {
  public static void main(String[] args) {
    SpringApplication.run(Application.class, args);
  }
}
// Spring Boot automatically:
// - Scans packages for @Component, @Service, @Controller
// - Configures DispatcherServlet
// - Starts embedded Tomcat on port 8080
// - Loads application.properties or application.yml
```

**Interview Questions:**
1. What is a Spring Boot starter?
2. What does @SpringBootApplication do?
3. What is auto-configuration?
4. How does Spring Boot decide what to auto-configure?
5. Can you disable auto-configuration?
6. What is convention over configuration?

---

### 2.2 Application Properties & Configuration

**Configuration Sources (Priority Order):**
1. Command-line arguments
2. System properties
3. Environment variables
4. application.properties / application.yml
5. @PropertySource annotations

**Common Properties:**
```properties
# Server configuration
server.port=8080
server.servlet.context-path=/api

# Logging
logging.level.root=INFO
logging.level.com.myapp=DEBUG
logging.pattern.console=%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n

# Spring configuration
spring.application.name=my-app
spring.profiles.active=dev
```

**YAML Format (Cleaner):**
```yaml
server:
  port: 8080
  servlet:
    context-path: /api

logging:
  level:
    root: INFO
    com.myapp: DEBUG

spring:
  application:
    name: my-app
  profiles:
    active: dev
```

**Using @Value:**
```java
@Component
public class AppConfig {
  @Value("${server.port}")
  private int port;
  
  @Value("${spring.application.name}")
  private String appName;
}
```

**Using @ConfigurationProperties:**
```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {
  private String name;
  private String version;
  private boolean debug;
  
  // Getters and setters
}

// application.yml
app:
  name: MyApp
  version: 1.0.0
  debug: true
```

**Interview Questions:**
1. How does Spring Boot load configuration?
2. What is the priority of configuration sources?
3. Difference between @Value and @ConfigurationProperties?
4. What is profiles in Spring Boot?
5. How do you use different configurations for different environments?
6. What is spring.profiles.active?

---

### 2.3 Profiles & Environment-Specific Configuration

**Profiles:** Logical grouping of bean definitions and configurations for different environments

**Common Profiles:**
- dev: Development environment
- test: Testing environment
- prod: Production environment

**Using Profiles:**
```properties
# application.properties (base)
spring.application.name=myapp

# application-dev.properties
server.port=8080
logging.level.root=DEBUG
spring.datasource.url=jdbc:mysql://localhost:3306/mydb_dev

# application-prod.properties
server.port=8443
logging.level.root=INFO
spring.datasource.url=jdbc:mysql://prod-db:3306/mydb_prod
```

**Activate Profile:**
```bash
java -jar app.jar --spring.profiles.active=prod
# OR in application.yml
spring:
  profiles:
    active: prod
```

**Conditional Bean Creation:**
```java
@Configuration
@Profile("dev")
public class DevConfig {
  @Bean
  public DataSource dataSource() {
    // In-memory H2 database for development
  }
}

@Configuration
@Profile("prod")
public class ProdConfig {
  @Bean
  public DataSource dataSource() {
    // Production MySQL database
  }
}
```

**Interview Questions:**
1. What are Spring profiles?
2. How do you activate a profile?
3. How do you create environment-specific configurations?
4. Can you have multiple profiles active?
5. What is @Profile annotation?

---

## Module 3: REST API Development with Spring MVC (10 hours)

### 3.1 @RestController & @RequestMapping

**Core Concepts:**
- **@RestController:** Marks class as REST endpoint; combines @Controller + @ResponseBody
- **@RequestMapping:** Maps HTTP requests to handler methods
- **HTTP Methods:** GET, POST, PUT, DELETE, PATCH
- **Path Variables:** Extract values from URL path (@PathVariable)
- **Query Parameters:** Extract values from query string (@RequestParam)

**Basic REST Endpoint:**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
  
  @Autowired
  private UserService userService;
  
  // GET /api/users/{id}
  @GetMapping("/{id}")
  public User getUser(@PathVariable int id) {
    return userService.getUserById(id);
  }
  
  // POST /api/users
  @PostMapping
  public User createUser(@RequestBody User user) {
    return userService.saveUser(user);
  }
  
  // PUT /api/users/{id}
  @PutMapping("/{id}")
  public User updateUser(@PathVariable int id, @RequestBody User user) {
    user.setId(id);
    return userService.updateUser(user);
  }
  
  // DELETE /api/users/{id}
  @DeleteMapping("/{id}")
  public void deleteUser(@PathVariable int id) {
    userService.deleteUser(id);
  }
  
  // GET /api/users?name=Alice
  @GetMapping
  public List<User> getUsersByName(@RequestParam String name) {
    return userService.getUsersByName(name);
  }
}
```

**Path Variables vs Query Parameters:**
```java
// Path variable: identifies specific resource
GET /api/users/123 → Get user with ID 123

// Query parameters: filter or search
GET /api/users?role=ADMIN&status=active → Get all admins who are active
```

**Interview Questions:**
1. What is @RestController?
2. Difference between @RequestMapping and @GetMapping?
3. What is @PathVariable and @RequestParam?
4. When would you use path variable vs query parameter?
5. How do you return different HTTP status codes?
6. What is @ResponseBody?

---

### 3.2 Request & Response Handling

**@RequestBody:**
```java
@PostMapping
public User createUser(@RequestBody User user) {
  // Spring automatically deserializes JSON to User object
  // Requires Jackson or similar JSON processor
  return userService.saveUser(user);
}

// Client sends:
POST /api/users
Content-Type: application/json
{
  "name": "Alice",
  "email": "alice@example.com"
}
```

**@ResponseEntity:**
```java
@GetMapping("/{id}")
public ResponseEntity<User> getUser(@PathVariable int id) {
  User user = userService.getUserById(id);
  if (user == null) {
    return ResponseEntity.notFound().build(); // 404
  }
  return ResponseEntity.ok(user); // 200 with body
}

@PostMapping
public ResponseEntity<User> createUser(@RequestBody User user) {
  User created = userService.saveUser(user);
  return ResponseEntity
    .status(HttpStatus.CREATED) // 201
    .body(created);
}

// Also supports:
ResponseEntity.badRequest().body(...) // 400
ResponseEntity.forbidden().build() // 403
ResponseEntity.internalServerError().build() // 500
```

**Content Negotiation:**
```java
@GetMapping("/{id}")
public User getUser(@PathVariable int id) {
  // Spring automatically serializes to requested format
  // If Accept: application/json → JSON
  // If Accept: application/xml → XML
}
```

**Custom Response Object:**
```java
public class ApiResponse<T> {
  private int status;
  private String message;
  private T data;
  
  public ApiResponse(int status, String message, T data) {
    this.status = status;
    this.message = message;
    this.data = data;
  }
}

@PostMapping
public ResponseEntity<ApiResponse<User>> createUser(@RequestBody User user) {
  User created = userService.saveUser(user);
  return ResponseEntity.ok(
    new ApiResponse<>(200, "User created", created)
  );
}
```

**Interview Questions:**
1. What is @RequestBody?
2. What is ResponseEntity and when do you use it?
3. How do you return different HTTP status codes?
4. How does Spring deserialize JSON to Java objects?
5. What is content negotiation?
6. How do you return a custom response object?

---

### 3.3 HTTP Methods & REST Conventions

**GET:** Retrieve resource (safe, idempotent)
```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable int id) { ... }
```

**POST:** Create new resource (not idempotent)
```java
@PostMapping("/users")
public ResponseEntity<User> createUser(@RequestBody User user) {
  return ResponseEntity.status(HttpStatus.CREATED).body(created);
}
```

**PUT:** Replace entire resource (idempotent)
```java
@PutMapping("/users/{id}")
public User updateUser(@PathVariable int id, @RequestBody User user) {
  user.setId(id);
  return userService.updateUser(user); // Full replacement
}
```

**PATCH:** Partial update (idempotent)
```java
@PatchMapping("/users/{id}")
public User partialUpdate(@PathVariable int id, @RequestBody Map<String, Object> updates) {
  return userService.partialUpdate(id, updates);
}
```

**DELETE:** Remove resource (idempotent)
```java
@DeleteMapping("/users/{id}")
public ResponseEntity<Void> deleteUser(@PathVariable int id) {
  userService.deleteUser(id);
  return ResponseEntity.noContent().build(); // 204
}
```

**REST Conventions:**
- Use nouns for resources, not verbs
- ✅ POST /users (create user)
- ❌ POST /createUser
- ✅ DELETE /users/123 (delete user with ID 123)
- ❌ POST /deleteUser/123
- Status codes match action:
  - 200 OK: Successful GET, PUT, PATCH
  - 201 Created: Successful POST
  - 204 No Content: Successful DELETE
  - 400 Bad Request: Invalid input
  - 401 Unauthorized: Authentication required
  - 403 Forbidden: Permission denied
  - 404 Not Found: Resource not found
  - 500 Internal Server Error: Server error

**Interview Questions:**
1. What is REST?
2. Difference between PUT and PATCH?
3. When do you use POST vs PUT?
4. What HTTP status code should you return for successful POST?
5. What status code for resource not found?
6. How do you follow REST conventions?

---

## Module 4: Spring Security & Authentication (12 hours)

### 4.1 Spring Security Fundamentals

**Core Concepts:**
- **Authentication:** Verify user identity (who are you?)
- **Authorization:** Verify user permission (what can you do?)
- **Principal:** Currently authenticated user
- **Credentials:** Username, password, tokens, certificates
- **Filter Chain:** DispatcherServlet processes requests through filter chain

**Security Architecture:**
```
Request
  ↓
FilterChainProxy (Spring Security entry point)
  ↓
SecurityFilterChain (default filters applied)
  ├─ UsernamePasswordAuthenticationFilter (login)
  ├─ BearerTokenAuthenticationFilter (JWT)
  ├─ CsrfFilter (CSRF protection)
  ├─ CorsFilter (CORS handling)
  └─ ... (other filters)
  ↓
DispatcherServlet
  ↓
Controller
```

**Basic Security Configuration:**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
  
  @Bean
  public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
      .authorizeHttpRequests(authz -> authz
        .requestMatchers("/public/**").permitAll() // Anyone can access
        .requestMatchers("/admin/**").hasRole("ADMIN") // Only admin
        .anyRequest().authenticated() // Everything else requires login
      )
      .formLogin(form -> form
        .loginPage("/login")
        .defaultSuccessUrl("/")
      )
      .logout(logout -> logout
        .logoutUrl("/logout")
      )
      .csrf(csrf -> csrf.disable()); // Disable CSRF for now (enable in production)
    
    return http.build();
  }
}
```

**Interview Questions:**
1. What is authentication vs authorization?
2. How does Spring Security work?
3. What is a security filter?
4. What is the SecurityFilterChain?
5. How do you configure Spring Security?

---

### 4.2 JWT (JSON Web Tokens) Authentication

**Why JWT:**
- Stateless: No session storage needed
- Scalable: Works across multiple servers
- Secure: Signed, can't be tampered with
- Self-contained: User info in token itself

**JWT Structure:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkFsaWNlIn0.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

Header.Payload.Signature
```

- **Header:** Algorithm (HS256, RS256)
- **Payload:** Claims (user ID, roles, expiration)
- **Signature:** HMAC(header.payload, secret)

**JWT Flow:**
```
1. User POSTs username/password to /login
2. Server verifies credentials
3. Server creates JWT token with user info
4. Server returns token to client
5. Client includes token in Authorization header: "Bearer <token>"
6. Server verifies token signature
7. Server grants access if token valid
```

**Implementation:**
```java
// JWT utility class
@Component
public class JwtTokenProvider {
  
  @Value("${app.jwt.secret}")
  private String jwtSecret;
  
  @Value("${app.jwt.expiration}")
  private long jwtExpiration;
  
  // Create token
  public String generateToken(Authentication authentication) {
    String username = authentication.getName();
    Date now = new Date();
    Date expiryDate = new Date(now.getTime() + jwtExpiration);
    
    return Jwts.builder()
      .setSubject(username)
      .setIssuedAt(now)
      .setExpiration(expiryDate)
      .signWith(SignatureAlgorithm.HS256, jwtSecret)
      .compact();
  }
  
  // Validate token
  public boolean validateToken(String token) {
    try {
      Jwts.parser()
        .setSigningKey(jwtSecret)
        .parseClaimsJws(token);
      return true;
    } catch (JwtException | IllegalArgumentException e) {
      return false;
    }
  }
  
  // Extract username from token
  public String getUsernameFromToken(String token) {
    Claims claims = Jwts.parser()
      .setSigningKey(jwtSecret)
      .parseClaimsJws(token)
      .getBody();
    return claims.getSubject();
  }
}

// Login endpoint
@RestController
@RequestMapping("/api/auth")
public class AuthController {
  
  @Autowired
  private AuthenticationManager authenticationManager;
  
  @Autowired
  private JwtTokenProvider tokenProvider;
  
  @PostMapping("/login")
  public ResponseEntity<JwtResponse> login(@RequestBody LoginRequest loginRequest) {
    // Authenticate user
    Authentication authentication = authenticationManager.authenticate(
      new UsernamePasswordAuthenticationToken(
        loginRequest.getUsername(),
        loginRequest.getPassword()
      )
    );
    
    // Generate token
    String token = tokenProvider.generateToken(authentication);
    
    return ResponseEntity.ok(new JwtResponse(token));
  }
}

// JWT filter for protected endpoints
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {
  
  @Autowired
  private JwtTokenProvider tokenProvider;
  
  @Override
  protected void doFilterInternal(
      HttpServletRequest request,
      HttpServletResponse response,
      FilterChain filterChain) throws ServletException, IOException {
    
    try {
      // Extract token from Authorization header
      String jwt = getJwtFromRequest(request);
      
      if (jwt != null && tokenProvider.validateToken(jwt)) {
        // Token valid, extract username and authenticate
        String username = tokenProvider.getUsernameFromToken(jwt);
        UserDetails userDetails = loadUserByUsername(username);
        
        // Create authentication and set in security context
        UsernamePasswordAuthenticationToken authentication =
          new UsernamePasswordAuthenticationToken(
            userDetails, null, userDetails.getAuthorities());
        
        SecurityContextHolder.getContext().setAuthentication(authentication);
      }
    } catch (Exception ex) {
      // Token invalid, continue without authentication
    }
    
    filterChain.doFilter(request, response);
  }
  
  private String getJwtFromRequest(HttpServletRequest request) {
    String bearerToken = request.getHeader("Authorization");
    if (bearerToken != null && bearerToken.startsWith("Bearer ")) {
      return bearerToken.substring(7);
    }
    return null;
  }
}

// Configuration
@Configuration
@EnableWebSecurity
public class SecurityConfig {
  
  @Bean
  public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
      .authorizeHttpRequests(authz -> authz
        .requestMatchers("/api/auth/login", "/api/auth/register").permitAll()
        .anyRequest().authenticated()
      )
      .addFilterBefore(jwtAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class)
      .csrf(csrf -> csrf.disable())
      .httpBasic(httpBasic -> httpBasic.disable());
    
    return http.build();
  }
  
  @Bean
  public JwtAuthenticationFilter jwtAuthenticationFilter() {
    return new JwtAuthenticationFilter();
  }
}
```

**application.yml:**
```yaml
app:
  jwt:
    secret: my-super-secret-key-min-32-chars-for-hs256-algorithm
    expiration: 86400000 # 24 hours in milliseconds
```

**Interview Questions:**
1. What is JWT?
2. How does JWT authentication work?
3. What is the structure of JWT?
4. Difference between JWT and session-based authentication?
5. How do you validate a JWT token?
6. What is the signature in JWT?
7. Can JWT be decoded? Is it secure?
8. How long should JWT tokens last?
9. What happens if JWT token expires?
10. How do you refresh JWT tokens?

---

### 4.3 Role-Based Access Control (RBAC)

**Concepts:**
- **Role:** Permission group (ADMIN, USER, MODERATOR)
- **Authority:** Individual permission (READ, WRITE, DELETE)
- **Principal:** Currently authenticated user

**RBAC Configuration:**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
  
  @Bean
  public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
      .authorizeHttpRequests(authz -> authz
        // Public endpoints
        .requestMatchers("/public/**").permitAll()
        
        // Admin only
        .requestMatchers("/admin/**").hasRole("ADMIN")
        
        // Multiple roles
        .requestMatchers("/moderator/**").hasAnyRole("ADMIN", "MODERATOR")
        
        // By authority
        .requestMatchers(HttpMethod.DELETE, "/api/users/**").hasAuthority("USER_DELETE")
        
        // Everything else requires authentication
        .anyRequest().authenticated()
      )
      .formLogin(form -> form.loginPage("/login"))
      .logout(logout -> logout.logoutUrl("/logout"));
    
    return http.build();
  }
}
```

**Using @PreAuthorize for Method-Level Security:**
```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class MethodSecurityConfig {}

@RestController
@RequestMapping("/api/users")
public class UserController {
  
  // Only ADMIN can call
  @DeleteMapping("/{id}")
  @PreAuthorize("hasRole('ADMIN')")
  public void deleteUser(@PathVariable int id) {
    userService.deleteUser(id);
  }
  
  // Authenticated users can call
  @GetMapping("/{id}")
  @PreAuthorize("isAuthenticated()")
  public User getUser(@PathVariable int id) {
    return userService.getUserById(id);
  }
  
  // Custom authorization logic
  @PutMapping("/{id}")
  @PreAuthorize("@authorizationService.canEditUser(#id)")
  public User updateUser(@PathVariable int id, @RequestBody User user) {
    return userService.updateUser(user);
  }
}

// Custom authorization service
@Service
public class AuthorizationService {
  
  public boolean canEditUser(int userId) {
    Authentication auth = SecurityContextHolder.getContext().getAuthentication();
    int currentUserId = ((UserPrincipal) auth.getPrincipal()).getId();
    
    // User can edit own profile, or admin can edit anyone
    return currentUserId == userId || auth.getAuthorities()
      .stream()
      .anyMatch(a -> a.getAuthority().equals("ROLE_ADMIN"));
  }
}
```

**User Principal:**
```java
@Component
public class CustomUserDetailsService implements UserDetailsService {
  
  @Autowired
  private UserRepository userRepository;
  
  @Override
  public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
    User user = userRepository.findByUsername(username)
      .orElseThrow(() -> new UsernameNotFoundException("User not found"));
    
    return User.builder()
      .username(user.getUsername())
      .password(user.getPassword()) // Should be hashed
      .authorities(getAuthorities(user))
      .accountExpired(false)
      .accountLocked(false)
      .credentialsExpired(false)
      .disabled(false)
      .build();
  }
  
  private List<GrantedAuthority> getAuthorities(User user) {
    return user.getRoles()
      .stream()
      .map(role -> new SimpleGrantedAuthority("ROLE_" + role.getName()))
      .collect(Collectors.toList());
  }
}
```

**Interview Questions:**
1. What is RBAC?
2. Difference between role and authority?
3. How do you implement role-based access?
4. What is @PreAuthorize?
5. How do you check current user's role?
6. How do you implement custom authorization logic?
7. Can authorization be checked at method level?

---

## Module 5: Exception Handling & Validation (8 hours)

### 5.1 Custom Exception Handling

**Global Exception Handler with @ControllerAdvice:**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
  
  // Handle specific exception
  @ExceptionHandler(UserNotFoundException.class)
  public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
    ErrorResponse error = new ErrorResponse(
      404,
      ex.getMessage(),
      LocalDateTime.now()
    );
    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
  }
  
  // Handle validation errors
  @ExceptionHandler(MethodArgumentNotValidException.class)
  public ResponseEntity<ErrorResponse> handleValidationException(MethodArgumentNotValidException ex) {
    Map<String, String> errors = new HashMap<>();
    ex.getBindingResult().getFieldErrors().forEach(error -> {
      errors.put(error.getField(), error.getDefaultMessage());
    });
    
    ErrorResponse response = new ErrorResponse(
      400,
      "Validation failed",
      LocalDateTime.now()
    );
    response.setErrors(errors);
    return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(response);
  }
  
  // Handle all exceptions
  @ExceptionHandler(Exception.class)
  public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
    ErrorResponse error = new ErrorResponse(
      500,
      "Internal server error: " + ex.getMessage(),
      LocalDateTime.now()
    );
    return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
  }
}

// Error response DTO
public class ErrorResponse {
  private int status;
  private String message;
  private LocalDateTime timestamp;
  private Map<String, String> errors;
  
  public ErrorResponse(int status, String message, LocalDateTime timestamp) {
    this.status = status;
    this.message = message;
    this.timestamp = timestamp;
  }
  
  // Getters and setters
}

// Custom exception
public class UserNotFoundException extends RuntimeException {
  public UserNotFoundException(String message) {
    super(message);
  }
}
```

**Usage in Controller:**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
  
  @Autowired
  private UserService userService;
  
  @GetMapping("/{id}")
  public User getUser(@PathVariable int id) {
    User user = userService.getUserById(id);
    if (user == null) {
      throw new UserNotFoundException("User with ID " + id + " not found");
    }
    return user;
  }
}
```

**Interview Questions:**
1. What is @ControllerAdvice?
2. How do you handle exceptions globally?
3. How do you handle validation errors?
4. What is @ExceptionHandler?
5. How do you return custom error response?
6. Should you expose internal exception details to client?

---

### 5.2 Validation with Bean Validation (JSR 380)

**Common Annotations:**
```java
@Entity
public class User {
  
  @NotNull(message = "ID cannot be null")
  private Integer id;
  
  @NotBlank(message = "Name is required")
  @Size(min = 2, max = 100, message = "Name must be between 2 and 100 characters")
  private String name;
  
  @NotBlank
  @Email(message = "Email should be valid")
  private String email;
  
  @Min(value = 18, message = "Age must be at least 18")
  @Max(value = 120, message = "Age must be at most 120")
  private Integer age;
  
  @Pattern(regexp = "^[0-9]{10}$", message = "Phone must be 10 digits")
  private String phone;
  
  @Positive(message = "Salary must be positive")
  private BigDecimal salary;
  
  @PastOrPresent(message = "Birth date cannot be in future")
  private LocalDate birthDate;
}
```

**Validation at Controller Level:**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
  
  @PostMapping
  public ResponseEntity<User> createUser(@Valid @RequestBody User user) {
    // If @Valid fails, Spring automatically returns 400 Bad Request
    // with validation error details
    User created = userService.saveUser(user);
    return ResponseEntity.status(HttpStatus.CREATED).body(created);
  }
}
```

**Custom Validation Annotation:**
```java
// Define custom annotation
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = PhoneNumberValidator.class)
public @interface ValidPhoneNumber {
  String message() default "Invalid phone number";
  Class<?>[] groups() default {};
  Class<? extends Payload>[] payload() default {};
}

// Define validator
public class PhoneNumberValidator implements ConstraintValidator<ValidPhoneNumber, String> {
  
  @Override
  public boolean isValid(String value, ConstraintValidatorContext context) {
    if (value == null) {
      return true; // Let @NotNull handle null check
    }
    return value.matches("^[0-9]{10}$"); // 10 digits
  }
}

// Use in entity
@Entity
public class User {
  @ValidPhoneNumber
  private String phone;
}
```

**Interview Questions:**
1. What is Bean Validation?
2. What are common validation annotations?
3. What is @Valid and @Validated?
4. How do you create custom validators?
5. When is validation triggered?
6. How do you access validation errors in controller?

---

# TIER 2: IMPORTANT TOPICS ⭐⭐

## Module 6: HTTP Request/Response Handling & Content Negotiation (6 hours)

### 6.1 Headers & Cookies

**Request Headers:**
```java
@RestController
@RequestMapping("/api/test")
public class TestController {
  
  // Get specific header
  @GetMapping
  public String test(@RequestHeader("X-Custom-Header") String customHeader) {
    return "Received: " + customHeader;
  }
  
  // Get all headers
  @GetMapping("/headers")
  public Map<String, String> getAllHeaders(@RequestHeaders HttpHeaders headers) {
    Map<String, String> result = new HashMap<>();
    headers.forEach((key, values) -> {
      result.put(key, values.get(0));
    });
    return result;
  }
  
  // Get HttpServletRequest for full control
  @GetMapping("/request")
  public String handleRequest(HttpServletRequest request) {
    String userAgent = request.getHeader("User-Agent");
    String contentType = request.getContentType();
    return "User-Agent: " + userAgent + ", Content-Type: " + contentType;
  }
}
```

**Response Headers:**
```java
@PostMapping
public ResponseEntity<User> createUser(@RequestBody User user) {
  User created = userService.saveUser(user);
  
  // Add custom header
  HttpHeaders headers = new HttpHeaders();
  headers.add("X-Custom-Header", "value");
  headers.add("Location", "/api/users/" + created.getId());
  
  return new ResponseEntity<>(created, headers, HttpStatus.CREATED);
}
```

**Cookies:**
```java
// Read cookie
@GetMapping
public String getCookie(@CookieValue(name = "sessionId", defaultValue = "") String sessionId) {
  return "Session ID: " + sessionId;
}

// Set cookie
@PostMapping("/login")
public ResponseEntity<LoginResponse> login(@RequestBody LoginRequest request) {
  // ... authenticate ...
  
  HttpHeaders headers = new HttpHeaders();
  headers.add(
    HttpHeaders.SET_COOKIE,
    "sessionId=" + sessionId + "; Path=/; Max-Age=3600; HttpOnly"
  );
  
  return new ResponseEntity<>(response, headers, HttpStatus.OK);
}
```

**Interview Questions:**
1. How do you read request headers?
2. How do you set response headers?
3. What is the difference between headers and cookies?
4. How do you handle authentication via headers vs cookies?
5. What is HttpOnly flag on cookies?

---

### 6.2 File Upload & Download

**File Upload:**
```java
@PostMapping("/upload")
public ResponseEntity<String> uploadFile(@RequestParam("file") MultipartFile file) {
  
  // Validate file
  if (file.isEmpty()) {
    return ResponseEntity.badRequest().body("File is empty");
  }
  
  if (file.getSize() > 5 * 1024 * 1024) { // 5MB limit
    return ResponseEntity.badRequest().body("File is too large");
  }
  
  String originalFileName = file.getOriginalFilename();
  
  try {
    // Save file
    String uploadDir = "uploads/";
    String fileName = System.currentTimeMillis() + "_" + originalFileName;
    Path uploadPath = Paths.get(uploadDir);
    
    Files.createDirectories(uploadPath);
    Files.write(uploadPath.resolve(fileName), file.getBytes());
    
    return ResponseEntity.ok("File uploaded: " + fileName);
    
  } catch (IOException e) {
    return ResponseEntity.internalServerError().body("Upload failed");
  }
}

// Multiple files
@PostMapping("/upload-multiple")
public ResponseEntity<List<String>> uploadMultipleFiles(
    @RequestParam("files") MultipartFile[] files) {
  
  List<String> uploadedFiles = new ArrayList<>();
  
  for (MultipartFile file : files) {
    // ... save each file ...
    uploadedFiles.add(file.getOriginalFilename());
  }
  
  return ResponseEntity.ok(uploadedFiles);
}
```

**File Download:**
```java
@GetMapping("/download/{filename}")
public ResponseEntity<Resource> downloadFile(@PathVariable String filename) {
  
  try {
    Path filePath = Paths.get("uploads/").resolve(filename);
    Resource resource = new UrlResource(filePath.toUri());
    
    if (!resource.exists()) {
      return ResponseEntity.notFound().build();
    }
    
    return ResponseEntity.ok()
      .header(HttpHeaders.CONTENT_DISPOSITION,
        "attachment; filename=\"" + filename + "\"")
      .body(resource);
      
  } catch (MalformedURLException e) {
    return ResponseEntity.badRequest().build();
  }
}
```

**Interview Questions:**
1. How do you handle file upload?
2. How do you validate uploaded files?
3. How do you implement file download?
4. What is MultipartFile?
5. How do you handle file size limits?

---

### 6.3 Interceptors & Filters

**Interceptors (Spring MVC level):**
```java
@Component
public class LoggingInterceptor implements HandlerInterceptor {
  
  private static final Logger logger = LoggerFactory.getLogger(LoggingInterceptor.class);
  
  // Before request reaches controller
  @Override
  public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                          Object handler) throws Exception {
    logger.info("Request: {} {}", request.getMethod(), request.getRequestURI());
    return true; // Return false to stop processing
  }
  
  // After controller returns, before response sent
  @Override
  public void postHandle(HttpServletRequest request, HttpServletResponse response,
                        Object handler, ModelAndView modelAndView) throws Exception {
    logger.info("Response: {} {}", response.getStatus(), request.getRequestURI());
  }
  
  // After response sent
  @Override
  public void afterCompletion(HttpServletRequest request, HttpServletResponse response,
                             Object handler, Exception ex) throws Exception {
    if (ex != null) {
      logger.error("Exception: ", ex);
    }
  }
}

// Register interceptor
@Configuration
public class WebConfig implements WebMvcConfigurer {
  
  @Autowired
  private LoggingInterceptor loggingInterceptor;
  
  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(loggingInterceptor)
      .addPathPatterns("/api/**")
      .excludePathPatterns("/api/auth/login");
  }
}
```

**Filters (Servlet level):**
```java
@Component
public class RequestIdFilter implements Filter {
  
  private static final Logger logger = LoggerFactory.getLogger(RequestIdFilter.class);
  
  @Override
  public void doFilter(ServletRequest request, ServletResponse response,
                      FilterChain chain) throws IOException, ServletException {
    
    HttpServletRequest httpRequest = (HttpServletRequest) request;
    
    // Generate request ID
    String requestId = UUID.randomUUID().toString();
    httpRequest.setAttribute("requestId", requestId);
    
    // Log request
    logger.info("Request ID: {}, Path: {}", requestId, httpRequest.getRequestURI());
    
    try {
      // Pass to next filter
      chain.doFilter(request, response);
    } finally {
      // Cleanup
      logger.info("Request ID: {} completed", requestId);
    }
  }
}
```

**Difference:**
- **Filter:** Works at Servlet level; can modify request/response
- **Interceptor:** Works at Spring MVC level; has access to Spring context

**Interview Questions:**
1. What is the difference between filters and interceptors?
2. When would you use each?
3. What is the order of execution?
4. How do you register interceptors?
5. Can interceptors modify request/response?

---

## Module 7: Configuration & Beans (8 hours)

### 7.1 @Configuration & @Bean

**Explicit Bean Definition:**
```java
@Configuration
public class AppConfig {
  
  // Define bean explicitly
  @Bean
  public DataSource dataSource() {
    HikariConfig config = new HikariConfig();
    config.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
    config.setUsername("root");
    config.setPassword("password");
    return new HikariDataSource(config);
  }
  
  // Bean depends on another bean
  @Bean
  public UserService userService(UserRepository repository) {
    return new UserService(repository);
  }
  
  // Conditional bean creation
  @Bean
  @ConditionalOnProperty(name = "feature.cache", havingValue = "true")
  public CacheManager cacheManager() {
    return new SimpleCacheManager();
  }
}
```

**Bean Scope:**
```java
@Configuration
public class BeanScopeConfig {
  
  @Bean
  @Scope("singleton") // Default; one instance per app
  public SingletonBean singletonBean() {
    return new SingletonBean();
  }
  
  @Bean
  @Scope("prototype") // New instance each time requested
  public PrototypeBean prototypeBean() {
    return new PrototypeBean();
  }
}
```

**Interview Questions:**
1. What is @Configuration?
2. What is @Bean?
3. When would you use @Bean over @Component?
4. How do you inject beans into @Bean methods?
5. What are bean scopes?
6. What is @ConditionalOnProperty?

---

### 7.2 Custom Properties & Configuration Classes

**Type-Safe Configuration:**
```java
@Component
@ConfigurationProperties(prefix = "app.mail")
@Validated
public class MailProperties {
  
  @NotBlank
  private String host;
  
  @Min(0)
  @Max(65535)
  private int port;
  
  private String from;
  
  private boolean tlsEnabled;
  
  // Getters and setters
  
  public static class Auth {
    @NotBlank
    private String username;
    
    @NotBlank
    private String password;
    
    // Getters and setters
  }
}

// application.yml
app:
  mail:
    host: smtp.gmail.com
    port: 587
    from: noreply@myapp.com
    tls-enabled: true
```

**Using Configured Properties:**
```java
@Component
public class EmailService {
  
  @Autowired
  private MailProperties mailProperties;
  
  public void sendEmail(String to, String subject, String body) {
    // Use mailProperties
    System.out.println("Sending from: " + mailProperties.getFrom());
  }
}
```

**Interview Questions:**
1. What is @ConfigurationProperties?
2. Why use configuration classes instead of @Value?
3. How do you validate configuration properties?
4. What is prefix in @ConfigurationProperties?

---

## Module 8: Testing (10 hours)

### 8.1 Unit Testing with JUnit 5 & Mockito

**Basic Unit Test:**
```java
public class UserServiceTest {
  
  private UserService userService;
  
  @Mock
  private UserRepository userRepository;
  
  @BeforeEach
  public void setUp() {
    MockitoAnnotations.openMocks(this);
    userService = new UserService(userRepository);
  }
  
  @Test
  public void testGetUserById_Success() {
    // Arrange
    int userId = 1;
    User mockUser = new User(userId, "Alice", "alice@example.com");
    when(userRepository.findById(userId)).thenReturn(Optional.of(mockUser));
    
    // Act
    User result = userService.getUserById(userId);
    
    // Assert
    assertEquals("Alice", result.getName());
    assertEquals("alice@example.com", result.getEmail());
    verify(userRepository, times(1)).findById(userId);
  }
  
  @Test
  public void testGetUserById_NotFound() {
    // Arrange
    when(userRepository.findById(999)).thenReturn(Optional.empty());
    
    // Act & Assert
    assertThrows(UserNotFoundException.class, () -> {
      userService.getUserById(999);
    });
  }
}
```

**Mockito Annotations:**
- `@Mock`: Create mock object
- `@InjectMocks`: Create instance and inject mocks
- `@Spy`: Real object with mocking capability
- `@Captor`: Capture arguments

**Interview Questions:**
1. What is unit testing?
2. What is mocking?
3. What is @Mock vs @InjectMocks?
4. How do you verify method calls?
5. What is ArgumentCaptor?

---

### 8.2 Integration Testing with @SpringBootTest

**Integration Test:**
```java
@SpringBootTest
@AutoConfigureMockMvc
public class UserControllerIntegrationTest {
  
  @Autowired
  private MockMvc mockMvc;
  
  @Autowired
  private UserRepository userRepository;
  
  @BeforeEach
  public void setUp() {
    userRepository.deleteAll();
  }
  
  @Test
  public void testCreateUser_Success() throws Exception {
    User newUser = new User(null, "Alice", "alice@example.com");
    
    mockMvc.perform(post("/api/users")
      .contentType(MediaType.APPLICATION_JSON)
      .content(objectMapper.writeValueAsString(newUser)))
      .andExpect(status().isCreated())
      .andExpect(jsonPath("$.name").value("Alice"))
      .andExpect(jsonPath("$.email").value("alice@example.com"));
  }
  
  @Test
  public void testGetUser_Success() throws Exception {
    User user = userRepository.save(new User(null, "Bob", "bob@example.com"));
    
    mockMvc.perform(get("/api/users/" + user.getId()))
      .andExpect(status().isOk())
      .andExpect(jsonPath("$.name").value("Bob"));
  }
  
  @Test
  public void testGetUser_NotFound() throws Exception {
    mockMvc.perform(get("/api/users/999"))
      .andExpect(status().isNotFound());
  }
}
```

**MockMvc Methods:**
- `perform()`: Execute request
- `andExpect()`: Assert response
- `andReturn()`: Get response details

**Interview Questions:**
1. What is integration testing?
2. What is @SpringBootTest?
3. What is MockMvc?
4. How do you test REST endpoints?
5. How do you test with database?

---

## Module 9: Logging & Monitoring (6 hours)

### 9.1 Logging with SLF4J & Logback

**SLF4J (Simple Logging Facade):**
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Service
public class UserService {
  
  private static final Logger logger = LoggerFactory.getLogger(UserService.class);
  
  public User getUserById(int id) {
    logger.info("Fetching user with ID: {}", id);
    
    try {
      User user = userRepository.findById(id)
        .orElseThrow(() -> new UserNotFoundException("User not found"));
      
      logger.debug("User found: {}", user);
      return user;
      
    } catch (UserNotFoundException ex) {
      logger.warn("User not found with ID: {}", id);
      throw ex;
    }
  }
}
```

**Log Levels:**
- **TRACE:** Most detailed (rarely used)
- **DEBUG:** Development debugging
- **INFO:** General information (default)
- **WARN:** Warning messages (potential issues)
- **ERROR:** Error messages (should be fixed)

**Logback Configuration (logback.xml):**
```xml
<configuration>
  <!-- Console appender -->
  <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>
  
  <!-- File appender -->
  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>logs/app.log</file>
    <encoder>
      <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
    <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
      <fileNamePattern>logs/app-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
      <maxFileSize>10MB</maxFileSize>
      <maxHistory>30</maxHistory>
    </rollingPolicy>
  </appender>
  
  <!-- Root logger -->
  <root level="INFO">
    <appender-ref ref="CONSOLE"/>
    <appender-ref ref="FILE"/>
  </root>
  
  <!-- Specific logger for app -->
  <logger name="com.myapp" level="DEBUG"/>
  
  <!-- Spring logger -->
  <logger name="org.springframework" level="INFO"/>
</configuration>
```

**application.yml:**
```yaml
logging:
  level:
    root: INFO
    com.myapp: DEBUG
    org.springframework.web: DEBUG
  pattern:
    console: "%d{HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"
  file:
    name: logs/app.log
```

**Interview Questions:**
1. What is SLF4J?
2. What are log levels?
3. How do you configure logging?
4. What is Logback?
5. Why use parameterized messages?

---

### 9.2 Actuator & Health Checks

**Actuator Endpoints:**
```
/actuator/health → Application health
/actuator/metrics → Performance metrics
/actuator/env → Environment properties
/actuator/mappings → Endpoint mappings
/actuator/loggers → Logger levels
/actuator/threaddump → Thread dump
```

**Configuration:**
```yaml
spring:
  application:
    name: my-app
  
management:
  endpoints:
    web:
      exposure:
        include: health,metrics,env,loggers,info
  endpoint:
    health:
      show-details: when-authorized
    metrics:
      enable:
        jvm: true
        process: true
```

**Custom Health Check:**
```java
@Component
public class DatabaseHealthIndicator extends AbstractHealthIndicator {
  
  @Autowired
  private DataSource dataSource;
  
  @Override
  protected void doHealthCheck(Health.Builder builder) throws Exception {
    try (Connection connection = dataSource.getConnection()) {
      builder.up()
        .withDetail("database", "MySQL")
        .withDetail("version", connection.getMetaData().getDatabaseVersion());
    } catch (SQLException ex) {
      builder.down()
        .withDetail("error", ex.getMessage());
    }
  }
}
```

**Interview Questions:**
1. What is Actuator?
2. What are common Actuator endpoints?
3. How do you enable Actuator endpoints?
4. How do you create custom health checks?
5. What is metrics?

---

# TIER 3: NICE-TO-KNOW TOPICS ⭐

## Module 10: Advanced Topics (10 hours)

### 10.1 AOP (Aspect-Oriented Programming)

**Concepts:**
- **Aspect:** Cross-cutting concern (logging, security, caching)
- **Join Point:** Point where aspect is applied
- **Pointcut:** Expression matching join points
- **Advice:** Code executed at join point

**Example: Logging Aspect:**
```java
@Aspect
@Component
public class LoggingAspect {
  
  private static final Logger logger = LoggerFactory.getLogger(LoggingAspect.class);
  
  // Advice for all methods in service package
  @Before("execution(* com.myapp.service.*.*(..))")
  public void logBefore(JoinPoint joinPoint) {
    String methodName = joinPoint.getSignature().getName();
    logger.info("Entering method: {}", methodName);
  }
  
  @After("execution(* com.myapp.service.*.*(..))")
  public void logAfter(JoinPoint joinPoint) {
    String methodName = joinPoint.getSignature().getName();
    logger.info("Exiting method: {}", methodName);
  }
  
  @AfterReturning(pointcut = "execution(* com.myapp.service.*.*(..))", 
                  returning = "result")
  public void logAfterReturning(JoinPoint joinPoint, Object result) {
    logger.info("Method returned: {}", result);
  }
  
  @AfterThrowing(pointcut = "execution(* com.myapp.service.*.*(..))", 
                 throwing = "ex")
  public void logAfterThrowing(JoinPoint joinPoint, Exception ex) {
    logger.error("Method threw exception: {}", ex.getMessage());
  }
}
```

**Interview Questions:**
1. What is AOP?
2. What are aspects, join points, pointcuts?
3. When would you use AOP?
4. What are types of advice?
5. Can AOP be used for performance monitoring?

---

### 10.2 Caching Basics

**Spring Cache Annotation:**
```java
@Configuration
@EnableCaching
public class CacheConfig {}

@Service
public class UserService {
  
  @Cacheable(value = "users", key = "#id")
  public User getUserById(int id) {
    // Only executes if not in cache
    return userRepository.findById(id).orElse(null);
  }
  
  @CachePut(value = "users", key = "#user.id")
  public User updateUser(User user) {
    // Executes and updates cache
    return userRepository.save(user);
  }
  
  @CacheEvict(value = "users", key = "#id")
  public void deleteUser(int id) {
    // Executes and removes from cache
    userRepository.deleteById(id);
  }
  
  @CacheEvict(value = "users", allEntries = true)
  public void clearUserCache() {
    // Clear entire cache
  }
}
```

**Interview Questions:**
1. What is caching?
2. What is @Cacheable?
3. Difference between @Cacheable and @CachePut?
4. What is @CacheEvict?
5. When should you use caching?

---

### 10.3 Events (Observer Pattern)

**Spring Events:**
```java
// Define event
public class UserCreatedEvent extends ApplicationEvent {
  private User user;
  
  public UserCreatedEvent(Object source, User user) {
    super(source);
    this.user = user;
  }
  
  public User getUser() {
    return user;
  }
}

// Publisher
@Service
public class UserService {
  
  @Autowired
  private ApplicationEventPublisher eventPublisher;
  
  public User createUser(User user) {
    User created = userRepository.save(user);
    eventPublisher.publishEvent(new UserCreatedEvent(this, created));
    return created;
  }
}

// Listener
@Component
public class UserEventListener {
  
  @EventListener
  public void onUserCreated(UserCreatedEvent event) {
    User user = event.getUser();
    System.out.println("User created: " + user.getName());
    // Send welcome email, update analytics, etc.
  }
}
```

**Interview Questions:**
1. What is the Observer pattern?
2. How does Spring Events work?
3. When would you use events?
4. What is @EventListener?

---

## Module 11: Performance Optimization & Best Practices (6 hours)

### 11.1 Performance Tips

**1. Lazy Loading vs Eager Loading:**
```java
// Eager loading: Load everything upfront
@Service
public class OrderService {
  public Order getOrderWithAll(int id) {
    return orderRepository.findByIdWithItems(id);
  }
}

// Lazy loading: Load only when needed
@Service
public class OrderService {
  public Order getOrder(int id) {
    return orderRepository.findById(id).orElse(null);
    // Items loaded only if accessed
  }
}
```

**2. Connection Pooling:**
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: password
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 30000
```

**3. Pagination:**
```java
@GetMapping
public Page<User> getUsers(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size) {
  
  PageRequest pageRequest = PageRequest.of(page, size);
  return userRepository.findAll(pageRequest);
}
```

**4. Async Processing:**
```java
@Configuration
@EnableAsync
public class AsyncConfig {}

@Service
public class EmailService {
  
  @Async
  public void sendEmailAsync(String to, String subject, String body) {
    // Runs in background thread
    Thread.sleep(5000); // Simulate sending
  }
}

@RestController
public class UserController {
  
  @PostMapping
  public ResponseEntity<User> createUser(@RequestBody User user) {
    User created = userService.saveUser(user);
    emailService.sendEmailAsync(user.getEmail(), "Welcome", "...");
    return ResponseEntity.status(HttpStatus.CREATED).body(created);
  }
}
```

**Interview Questions:**
1. What is N+1 query problem?
2. How do you optimize database queries?
3. What is connection pooling?
4. Why use pagination?
5. When should you use async processing?

---

### 11.2 Best Practices

**1. Use Immutable Objects:**
```java
@Entity
public class User {
  @Id
  private Integer id;
  
  private final String name; // final, can't change
  private final String email; // final, can't change
  
  // No setters
  public User(Integer id, String name, String email) {
    this.id = id;
    this.name = name;
    this.email = email;
  }
}
```

**2. Use DTOs (Data Transfer Objects):**
```java
// DTO for API response
public class UserDto {
  private Integer id;
  private String name;
  private String email;
  // Only expose what client needs
}

@GetMapping
public UserDto getUser(@PathVariable int id) {
  User user = userService.getUserById(id);
  return new UserDto(user.getId(), user.getName(), user.getEmail());
}
```

**3. Fail Fast:**
```java
public void processUser(User user) {
  // Validate early
  if (user == null) {
    throw new IllegalArgumentException("User cannot be null");
  }
  if (user.getName() == null || user.getName().isEmpty()) {
    throw new IllegalArgumentException("Name is required");
  }
  // ... process ...
}
```

**4. Error Handling:**
```java
@PostMapping
public ResponseEntity<ApiResponse> createUser(@RequestBody User user) {
  try {
    User created = userService.saveUser(user);
    return ResponseEntity.ok(new ApiResponse(200, "Success", created));
  } catch (ValidationException ex) {
    return ResponseEntity.badRequest()
      .body(new ApiResponse(400, ex.getMessage(), null));
  } catch (Exception ex) {
    return ResponseEntity.internalServerError()
      .body(new ApiResponse(500, "Internal server error", null));
  }
}
```

**Interview Questions:**
1. What are best practices in REST API design?
2. Should you expose database entities or DTOs?
3. Why should you validate input early?
4. How should errors be handled?

---

## Study Timeline: Your Next 8 Weeks

### Week 1–2: Fundamentals (14 hours)
- Module 1: IoC & DI, Beans
- Module 2: Spring Boot basics, auto-configuration
- **Mini-project:** Simple REST API with 2 endpoints
- **Hands-on:** Dependency injection, bean scoping

### Week 3: REST API Development (10 hours)
- Module 3: @RestController, @RequestMapping, CRUD endpoints
- **Mini-project:** Full CRUD API
- **Hands-on:** Build endpoints, test with Postman

### Week 4: Security (12 hours)
- Module 4: Spring Security, JWT, RBAC
- **Mini-project:** Secure REST API with login
- **Hands-on:** JWT implementation, role-based access

### Week 5: Error Handling & Validation (8 hours)
- Module 5: Exception handling, Bean Validation
- **Mini-project:** Comprehensive error handling
- **Hands-on:** Custom validators, error responses

### Week 6: Request/Response & Configuration (10 hours)
- Module 6: Headers, cookies, file upload
- Module 7: Configuration, properties, beans
- **Mini-project:** File upload feature
- **Hands-on:** Headers, interceptors, configuration

### Week 7: Testing (10 hours)
- Module 8: Unit testing, integration testing
- **Mini-project:** Write tests for API
- **Hands-on:** Mock objects, integration tests

### Week 8: Logging & Advanced (12 hours)
- Module 9: Logging, Actuator
- Module 10: AOP, caching, events (overview)
- Module 11: Performance, best practices
- **Mini-project:** Complete API with logging, monitoring
- **Final review:** Full integration of concepts

---

## Quick Reference: Most Asked Interview Questions

**Top 20 Spring Boot Questions (95%+ frequency):**

1. What is Spring and Spring Boot?
2. What is IoC and dependency injection?
3. What are Spring beans?
4. What is @SpringBootApplication?
5. What are auto-configurations?
6. What is @RestController?
7. What is @RequestMapping and HTTP methods?
8. What is @RequestBody and @ResponseEntity?
9. How does Spring Security work?
10. What is JWT?
11. What is RBAC?
12. How do you handle exceptions globally?
13. What is Bean Validation?
14. What is @Autowired?
15. What is @Configuration and @Bean?
16. How do you test REST endpoints?
17. What is MockMvc?
18. How do you log in Spring?
19. What is Actuator?
20. What are profiles in Spring Boot?

---

## Success Metrics

**Before applying to roles:**
- [ ] Can explain IoC, DI, beans confidently
- [ ] Can build a complete REST API with CRUD operations
- [ ] Can secure APIs with JWT and RBAC
- [ ] Can write unit and integration tests
- [ ] Can handle errors globally and validate input
- [ ] Average 75%+ on mock interviews

**When interview scheduled:**
- [ ] Can answer 80+ Spring Boot questions without notes
- [ ] Can code a REST API feature end-to-end
- [ ] Can explain security implementation in detail
- [ ] Can design API error handling strategy
- [ ] Average 80%+ on mock interviews

---

## Resources

**Official Documentation:**
- Spring Framework: https://spring.io/projects/spring-framework
- Spring Boot: https://spring.io/projects/spring-boot
- Spring Security: https://spring.io/projects/spring-security

**Learning:**
- Baeldung: https://baeldung.com (best tutorials)
- Spring Official Guides: https://spring.io/guides

**Books:**
- "Spring in Action" by Craig Walls (comprehensive)
- "Spring Security in Action" by Laurențiu Spilcă (security deep dive)

---

## Next Steps

**This week:**
1. Read Module 1 (Fundamentals) → 3 hours
2. Code examples from Module 1 → 1 hour
3. Build first bean and DI example → 2 hours

**By end of Week 1:**
- Complete Module 1 & 2
- Have first working Spring Boot app
- Understand dependency injection

**By end of Week 8:**
- Master all 11 modules
- Build complete REST API with security and testing
- Ready for backend Java interviews

You now have everything. Execute consistently.

---

**This is your Spring Boot blueprint. Use it systematically, practice actively, and you'll be production-ready.**