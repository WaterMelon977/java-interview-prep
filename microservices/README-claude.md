# Microservices Syllabus for Java Backend Developer Interviews
## Junior Developer (0–2 years) to Associate Developer (2–4 years)

---

## Module 1: Monolith vs. Microservices Fundamentals

### 1.1 Core Concepts
- **Monolithic Architecture**: Definition, characteristics, pros/cons
- **Microservices Architecture**: Service independence, loose coupling, scalability
- **Key Difference**: Deployment models, communication patterns, data ownership
- **When to Use Each**: Trade-offs (complexity, team size, scalability needs)

### 1.2 Business & Organizational Context
- Conway's Law: Organizational structure mirrors system architecture
- Domain-Driven Design (DDD) basics: Bounded contexts, ubiquitous language
- Why microservices: Independent scaling, technology diversity, faster deployment cycles
- **Interview Focus**: Explain when you'd advocate for monolith vs. microservices for a given scenario

### 1.3 Common Pitfalls
- Premature microservices adoption (golden hammer syndrome)
- Treating microservices as just "distributed monolith"
- Underestimating operational complexity

---

## Module 2: Service Communication Patterns

### 2.1 Synchronous Communication (Request-Response)
**REST APIs**
- HTTP methods (GET, POST, PUT, DELETE, PATCH)
- Status codes (2xx, 3xx, 4xx, 5xx), idempotency
- Best practices: resource-oriented design, versioning (URL path vs. header)
- When to use: Real-time responses, strongly consistent workflows

**gRPC**
- Protocol Buffers (protobuf): Message definition, code generation
- HTTP/2 multiplexing, binary serialization
- Performance benefits vs. REST
- When to use: High-performance, low-latency internal service-to-service communication
- Trade-off: Complexity, less human-readable than REST

### 2.2 Asynchronous Communication (Event-Driven)
**Message Brokers & Pub-Sub**
- Apache Kafka: Topics, partitions, consumer groups, offsets
- RabbitMQ: Exchanges, queues, message routing
- AWS SNS/SQS: Use cases, differences
- Event sourcing: Immutable event log, rebuilding state

**Key Concepts**
- Fire-and-forget semantics
- Eventual consistency
- Dead-letter queues, retry mechanisms
- When to use: Decoupling services, asynchronous workflows, broadcasting events

### 2.3 Communication Pattern Comparison
| Pattern | Latency | Consistency | Complexity | Use Case |
|---------|---------|-------------|-----------|----------|
| REST | Higher | Strong | Low | User-facing APIs, simple workflows |
| gRPC | Lower | Strong | Medium | Service-to-service, high throughput |
| Kafka | Variable | Eventual | High | Event streaming, decoupling |
| RabbitMQ | Variable | Eventual | Medium | Task queues, pub-sub |

**Interview Question**: "Design a payment system. Would you use sync or async communication? Why?"

---

## Module 3: Distributed System Challenges & Solutions

### 3.1 Failure Handling
**Resilience Patterns**
- Retry logic: Exponential backoff, jitter, max retries
- Circuit breaker pattern: States (closed, open, half-open), threshold configuration
- Timeout: Call-level, request-level, service-level
- Bulkhead pattern: Isolate resources, thread pool separation

**Popular Libraries**
- Resilience4j: Spring Boot integration
- Netflix Hystrix (legacy, deprecated in favor of Resilience4j)
- Retrofit + OkHttp for HTTP clients

**Implementation Example (Resilience4j)**
```java
@CircuitBreaker(name = "order-service", fallbackMethod = "orderFallback")
@Retry(name = "order-service")
@Timeout(name = "order-service")
public Order getOrderFromRemoteService(String orderId) {
    // call remote service
}

public Order orderFallback(String orderId, Exception e) {
    // fallback response
    return new Order(orderId, "fallback");
}
```

### 3.2 Distributed Transactions & Consistency
**The Problem**: No distributed ACID across services

**Solution Patterns**

1. **Saga Pattern** (Two-Phase Commit alternative)
   - Choreography: Services emit events, others listen and react
   - Orchestration: Central coordinator directs transaction steps
   - Compensation: Rollback through compensating transactions
   - When: Cross-service transactions (e.g., order → payment → inventory)

2. **Event Sourcing + CQRS**
   - Event Sourcing: Store all state changes as immutable events
   - CQRS: Command Query Responsibility Segregation (separate read/write models)
   - Rebuild state by replaying events
   - When: Audit trails, temporal queries, event-driven architectures

3. **Eventual Consistency**
   - Accept temporary inconsistency, guarantee convergence
   - Compensations & conflict resolution
   - When: High-volume, distributed systems where immediate consistency is impractical

**Interview Question**: "How would you handle a distributed transaction across order, payment, and inventory services?"

### 3.3 Service Discovery & Load Balancing
- **Client-side discovery**: Clients resolve service locations (Eureka, Consul)
- **Server-side discovery**: Load balancer routes to services (API Gateway)
- Service registry: Registration, health checks, deregistration
- Load balancing strategies: Round-robin, least connections, sticky sessions

**Tools**
- Spring Cloud Netflix Eureka
- Spring Cloud Config
- Kubernetes DNS (for containerized services)
- Nginx, HAProxy (traditional load balancers)

### 3.4 Network Reliability & Latency
- Network is not reliable: Timeouts, retries, idempotency keys
- P99 latency monitoring
- Cascading failures: How to prevent (timeouts, circuit breakers)

---

## Module 4: Spring Boot Microservices Implementation

### 4.1 Project Structure & Dependencies
**Typical POM Dependencies**
```xml
<!-- Spring Boot Starter Web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- Spring Data JPA -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- Spring Cloud (Eureka, Config, etc.) -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

<!-- Resilience4j -->
<dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot2</artifactId>
</dependency>

<!-- Feign for inter-service communication -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

### 4.2 REST API Design
**Controller Best Practices**
```java
@RestController
@RequestMapping("/api/v1/orders")
public class OrderController {
    @Autowired
    private OrderService orderService;

    @GetMapping("/{orderId}")
    public ResponseEntity<OrderDTO> getOrder(@PathVariable String orderId) {
        try {
            OrderDTO order = orderService.getOrderById(orderId);
            return ResponseEntity.ok(order);
        } catch (OrderNotFoundException e) {
            return ResponseEntity.notFound().build();
        }
    }

    @PostMapping
    public ResponseEntity<OrderDTO> createOrder(@Valid @RequestBody CreateOrderRequest request) {
        OrderDTO createdOrder = orderService.createOrder(request);
        return ResponseEntity.status(HttpStatus.CREATED).body(createdOrder);
    }

    @PutMapping("/{orderId}")
    public ResponseEntity<OrderDTO> updateOrder(
            @PathVariable String orderId,
            @Valid @RequestBody UpdateOrderRequest request) {
        OrderDTO updated = orderService.updateOrder(orderId, request);
        return ResponseEntity.ok(updated);
    }

    @DeleteMapping("/{orderId}")
    public ResponseEntity<Void> deleteOrder(@PathVariable String orderId) {
        orderService.deleteOrder(orderId);
        return ResponseEntity.noContent().build();
    }
}
```

**Key Principles**
- Use proper HTTP methods and status codes
- DTOs (Data Transfer Objects) for request/response
- Validation annotations (@Valid, @NotNull, @Email)
- Exception handling via @ControllerAdvice

### 4.3 Service Layer & Business Logic
```java
@Service
@Transactional
public class OrderService {
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private PaymentClient paymentClient;
    
    @Autowired
    private InventoryClient inventoryClient;

    public OrderDTO createOrder(CreateOrderRequest request) {
        // validate input
        if (request.getItems().isEmpty()) {
            throw new ValidationException("Order must have at least one item");
        }

        // check inventory
        boolean inventoryAvailable = inventoryClient.checkAvailability(request.getItems());
        if (!inventoryAvailable) {
            throw new InsufficientInventoryException("Items not available");
        }

        // create order entity
        Order order = new Order();
        order.setStatus(OrderStatus.PENDING);
        order.setItems(request.getItems());
        order.setCreatedAt(LocalDateTime.now());

        // save order
        Order savedOrder = orderRepository.save(order);

        // trigger payment (async or sync based on pattern)
        try {
            paymentClient.processPayment(savedOrder.getId(), request.getAmount());
        } catch (PaymentException e) {
            orderRepository.updateStatus(savedOrder.getId(), OrderStatus.FAILED);
            throw e;
        }

        return mapToDTO(savedOrder);
    }

    @Transactional(readOnly = true)
    public OrderDTO getOrderById(String orderId) {
        Order order = orderRepository.findById(orderId)
            .orElseThrow(() -> new OrderNotFoundException("Order not found: " + orderId));
        return mapToDTO(order);
    }

    private OrderDTO mapToDTO(Order order) {
        return new OrderDTO(order.getId(), order.getStatus(), order.getCreatedAt());
    }
}
```

**Interview Points**
- Service handles business logic, orchestrates client calls
- Transaction boundaries (when to use @Transactional, read-only optimizations)
- Exception handling strategy

### 4.4 Data Access Layer
```java
@Repository
public interface OrderRepository extends JpaRepository<Order, String> {
    List<Order> findByCustomerId(String customerId);
    
    @Query("SELECT o FROM Order o WHERE o.status = :status AND o.createdAt > :date")
    List<Order> findRecentOrdersByStatus(
        @Param("status") OrderStatus status,
        @Param("date") LocalDateTime date);
}
```

**Best Practices**
- Use JPA repositories for CRUD
- Custom @Query for complex filters
- Index frequently queried columns in the database
- Pagination for large result sets

---

## Module 5: Inter-Service Communication

### 5.1 REST Client with Feign
```java
@FeignClient(name = "payment-service", url = "${payment.service.url}")
public interface PaymentClient {
    @PostMapping("/api/v1/payments")
    PaymentResponse processPayment(@RequestBody PaymentRequest request);

    @GetMapping("/api/v1/payments/{paymentId}")
    PaymentResponse getPayment(@PathVariable String paymentId);
}
```

**Configuration**
```yaml
feign:
  client:
    config:
      payment-service:
        connectTimeout: 5000
        readTimeout: 10000
        loggerLevel: full
```

**Error Handling**
```java
@Configuration
public class FeignErrorDecoder implements ErrorDecoder {
    @Override
    public Exception decode(String methodKey, Response response) {
        if (response.status() == 404) {
            return new PaymentNotFoundException("Payment not found");
        } else if (response.status() == 500) {
            return new PaymentServiceException("Payment service unavailable");
        }
        return new Exception("Unknown error");
    }
}
```

### 5.2 Resilience Patterns in Spring Boot
**Circuit Breaker with Resilience4j**
```java
@Service
public class PaymentService {
    @CircuitBreaker(name = "paymentService", fallbackMethod = "paymentFallback")
    @Retry(name = "paymentService", fallbackMethod = "paymentFallback")
    @Timeout(name = "paymentService")
    public PaymentResponse processPayment(PaymentRequest request) {
        // call payment API
        return paymentClient.processPayment(request);
    }

    public PaymentResponse paymentFallback(PaymentRequest request, Exception e) {
        // fallback: queue for retry, return cached response, etc.
        logger.error("Payment service failed, enqueueing for retry", e);
        paymentQueue.enqueue(request);
        return new PaymentResponse("QUEUED", request.getOrderId());
    }
}
```

**Configuration (application.yml)**
```yaml
resilience4j:
  circuitbreaker:
    instances:
      paymentService:
        registerHealthIndicator: true
        slidingWindowSize: 10
        failureRateThreshold: 50
        waitDurationInOpenState: 10000
        permittedNumberOfCallsInHalfOpenState: 3
  retry:
    instances:
      paymentService:
        maxAttempts: 3
        waitDuration: 1000
  timeout:
    instances:
      paymentService:
        timeoutDuration: 5000
```

---

## Module 6: Data Management & Database Patterns

### 6.1 Database per Service Pattern
- **Each service owns its database**: Enforces loose coupling
- **Trade-off**: Complexity of distributed queries, eventual consistency
- **Foreign keys across services**: Not possible; use application-level joins or denormalization

**Implementation Example**
```
Order Service (PostgreSQL) → owns orders, order_items tables
Payment Service (PostgreSQL) → owns payments table
Inventory Service (MongoDB) → owns inventory_items collection

No foreign key relationships across services.
Query Order + Payment Details → Order Service calls Payment Service via REST.
```

### 6.2 Caching Strategies
**Local Cache (Caffeine, Guava)**
```java
@Configuration
public class CacheConfig {
    @Bean
    public CacheManager cacheManager() {
        return new CaffeineCacheManager("orders", "products");
    }
}

@Service
public class OrderService {
    @Cacheable(value = "orders", key = "#orderId")
    public OrderDTO getOrderById(String orderId) {
        return orderRepository.findById(orderId).map(this::mapToDTO).orElse(null);
    }

    @CacheEvict(value = "orders", key = "#orderId")
    public void deleteOrder(String orderId) {
        orderRepository.deleteById(orderId);
    }
}
```

**Distributed Cache (Redis)**
```yaml
spring:
  redis:
    host: localhost
    port: 6379
    timeout: 2000ms
```

```java
@Service
public class ProductService {
    @Autowired
    private RedisTemplate<String, ProductDTO> redisTemplate;

    public ProductDTO getProduct(String productId) {
        String cacheKey = "product:" + productId;
        ProductDTO product = redisTemplate.opsForValue().get(cacheKey);
        
        if (product == null) {
            product = productRepository.findById(productId).orElse(null);
            if (product != null) {
                redisTemplate.opsForValue().set(cacheKey, product, Duration.ofMinutes(10));
            }
        }
        
        return product;
    }
}
```

**Cache Invalidation Strategies**
- TTL (time-to-live)
- Event-driven invalidation (via Kafka)
- Write-through vs. write-behind

### 6.3 Transactions & Consistency
**Single Service Transaction (ACID)**
```java
@Service
public class OrderService {
    @Transactional
    public OrderDTO createOrder(CreateOrderRequest request) {
        // All DB operations in this method are part of one transaction
        // Rollback if any exception occurs
        Order order = new Order();
        orderRepository.save(order);
        orderItemRepository.saveAll(order.getItems());
        return mapToDTO(order);
    }
}
```

**Distributed Transaction (Saga Pattern)**
```java
@Service
public class OrderSaga {
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private PaymentClient paymentClient;
    
    @Autowired
    private InventoryClient inventoryClient;

    @Transactional
    public OrderDTO createOrderWithSaga(CreateOrderRequest request) {
        // Step 1: Create order
        Order order = new Order();
        Order savedOrder = orderRepository.save(order);

        try {
            // Step 2: Process payment
            paymentClient.processPayment(savedOrder.getId(), request.getAmount());
            
            // Step 3: Reserve inventory
            inventoryClient.reserveItems(savedOrder.getId(), request.getItems());
            
            // Step 4: Confirm order
            savedOrder.setStatus(OrderStatus.CONFIRMED);
            orderRepository.save(savedOrder);
            
        } catch (Exception e) {
            // Compensating transactions: rollback
            orderRepository.updateStatus(savedOrder.getId(), OrderStatus.FAILED);
            paymentClient.refund(savedOrder.getId());
            inventoryClient.releaseReservation(savedOrder.getId());
            throw new OrderCreationException("Order creation failed", e);
        }

        return mapToDTO(savedOrder);
    }
}
```

---

## Module 7: Logging, Monitoring & Observability

### 7.1 Structured Logging
**SLF4J + Logback**
```java
@Slf4j
@Service
public class OrderService {
    public OrderDTO createOrder(CreateOrderRequest request) {
        String correlationId = UUID.randomUUID().toString();
        MDC.put("correlationId", correlationId);
        
        try {
            log.info("Creating order", new Object[]{
                "customerId", request.getCustomerId(),
                "itemCount", request.getItems().size(),
                "correlationId", correlationId
            });
            
            OrderDTO order = // creation logic
            
            log.info("Order created successfully", 
                new Object[]{"orderId", order.getId(), "correlationId", correlationId});
            return order;
            
        } catch (Exception e) {
            log.error("Failed to create order", new Object[]{
                "customerId", request.getCustomerId(),
                "correlationId", correlationId
            }, e);
            throw e;
        } finally {
            MDC.clear();
        }
    }
}
```

**Configuration (logback-spring.xml)**
```xml
<configuration>
    <property name="LOG_PATTERN" value="%d{yyyy-MM-dd HH:mm:ss} [%X{correlationId}] %logger{36} - %msg%n"/>
    
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
    </appender>
    
    <root level="INFO">
        <appender-ref ref="STDOUT"/>
    </root>
</configuration>
```

### 7.2 Metrics & Monitoring
**Micrometer + Prometheus**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: prometheus,health,metrics
  metrics:
    export:
      prometheus:
        enabled: true
```

```java
@Service
public class OrderService {
    @Autowired
    private MeterRegistry meterRegistry;

    public OrderDTO createOrder(CreateOrderRequest request) {
        Timer timer = Timer.start(meterRegistry);
        
        try {
            // order creation
            meterRegistry.counter("orders.created").increment();
            return order;
        } catch (Exception e) {
            meterRegistry.counter("orders.creation.failed").increment();
            throw e;
        } finally {
            timer.stop(Timer.Sample.start(meterRegistry)
                .stop(meterRegistry.timer("orders.creation.duration")));
        }
    }
}
```

**Monitoring Queries (Prometheus)**
```
rate(orders_created[5m])  # Orders created per 5 minutes
orders_creation_duration_seconds  # Histogram of creation times
orders_creation_failed_total  # Total failed creations
```

### 7.3 Distributed Tracing
**Spring Cloud Sleuth + Jaeger/Zipkin**
```yaml
spring:
  sleuth:
    sampler:
      probability: 0.1  # Sample 10% of requests
  zipkin:
    baseUrl: http://localhost:9411
```

Automatically adds trace IDs and span IDs to logs and HTTP headers.

---

## Module 8: API Gateway & Routing

### 8.1 API Gateway Pattern
**Why**: Single entry point, routing, authentication, rate limiting, request transformation

**Tools**
- Spring Cloud Gateway
- Kong, AWS API Gateway, Nginx

**Spring Cloud Gateway Example**
```java
@Configuration
public class GatewayConfig {
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
        return builder.routes()
            .route("order-service", r -> r
                .path("/api/v1/orders/**")
                .uri("lb://order-service"))
            .route("payment-service", r -> r
                .path("/api/v1/payments/**")
                .uri("lb://payment-service"))
            .route("inventory-service", r -> r
                .path("/api/v1/inventory/**")
                .uri("lb://inventory-service"))
            .build();
    }
}
```

### 8.2 Cross-Cutting Concerns (Filters)
**Authentication Filter**
```java
@Component
public class AuthenticationFilter extends AbstractGatewayFilterFactory<AuthenticationFilter.Config> {
    @Autowired
    private JwtUtil jwtUtil;

    @Override
    public GatewayFilter apply(Config config) {
        return (exchange, chain) -> {
            ServerHttpRequest request = exchange.getRequest();
            
            if (!request.getHeaders().containsKey("Authorization")) {
                return onError(exchange, "Missing authorization header", HttpStatus.UNAUTHORIZED);
            }

            String token = request.getHeaders().get("Authorization").get(0).replace("Bearer ", "");
            
            if (!jwtUtil.isValidToken(token)) {
                return onError(exchange, "Invalid token", HttpStatus.UNAUTHORIZED);
            }

            return chain.filter(exchange);
        };
    }

    private Mono<Void> onError(ServerWebExchange exchange, String message, HttpStatus status) {
        ServerHttpResponse response = exchange.getResponse();
        response.setStatusCode(status);
        return response.writeWith(Mono.just(new DefaultDataBufferFactory()
            .wrap(message.getBytes())));
    }

    public static class Config {}
}
```

---

## Module 9: Containerization & Deployment

### 9.1 Docker for Spring Boot
**Dockerfile**
```dockerfile
FROM eclipse-temurin:17-jdk as builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

**Docker Compose (Multi-service)**
```yaml
version: '3.8'
services:
  order-service:
    build: ./order-service
    ports:
      - "8081:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/orders
      SPRING_DATASOURCE_USERNAME: postgres
      SPRING_DATASOURCE_PASSWORD: password
    depends_on:
      - postgres
      - kafka

  payment-service:
    build: ./payment-service
    ports:
      - "8082:8080"
    depends_on:
      - kafka

  postgres:
    image: postgres:14
    environment:
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"

  kafka:
    image: confluentinc/cp-kafka:7.0.0
    ports:
      - "9092:9092"
```

### 9.2 Kubernetes Basics
**Deployment YAML**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: order-service
  template:
    metadata:
      labels:
        app: order-service
    spec:
      containers:
      - name: order-service
        image: myregistry.azurecr.io/order-service:1.0.0
        ports:
        - containerPort: 8080
        env:
        - name: SPRING_DATASOURCE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: connection-string
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /actuator/health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10

---
apiVersion: v1
kind: Service
metadata:
  name: order-service
spec:
  selector:
    app: order-service
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
  type: LoadBalancer
```

---

## Module 10: Testing Microservices

### 10.1 Unit Testing
**JUnit 5 + Mockito**
```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {
    @Mock
    private OrderRepository orderRepository;
    
    @Mock
    private PaymentClient paymentClient;
    
    @InjectMocks
    private OrderService orderService;

    @Test
    void testCreateOrderSuccess() {
        // arrange
        CreateOrderRequest request = new CreateOrderRequest("cust1", List.of("item1"));
        Order savedOrder = new Order("ord1", OrderStatus.PENDING);
        when(orderRepository.save(any())).thenReturn(savedOrder);
        when(paymentClient.processPayment(anyString(), anyDouble()))
            .thenReturn(new PaymentResponse("SUCCESS", "ord1"));

        // act
        OrderDTO result = orderService.createOrder(request);

        // assert
        assertThat(result.getId()).isEqualTo("ord1");
        assertThat(result.getStatus()).isEqualTo(OrderStatus.PENDING);
        verify(paymentClient).processPayment("ord1", request.getAmount());
    }

    @Test
    void testCreateOrderPaymentFailure() {
        // arrange
        CreateOrderRequest request = new CreateOrderRequest("cust1", List.of("item1"));
        when(paymentClient.processPayment(anyString(), anyDouble()))
            .thenThrow(new PaymentException("Payment failed"));

        // act & assert
        assertThrows(PaymentException.class, () -> orderService.createOrder(request));
    }
}
```

### 10.2 Integration Testing
**Spring Boot Test**
```java
@SpringBootTest
@AutoConfigureMockMvc
class OrderControllerIntegrationTest {
    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private OrderRepository orderRepository;

    @Test
    void testCreateOrderEndpoint() throws Exception {
        CreateOrderRequest request = new CreateOrderRequest("cust1", List.of("item1"));

        mockMvc.perform(post("/api/v1/orders")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.id").exists())
            .andExpect(jsonPath("$.status").value("PENDING"));
    }
}
```

### 10.3 Contract Testing
**Spring Cloud Contract** (API contracts between services)
```groovy
Contract.make {
    description "should return order by id"
    request {
        method GET()
        url "/api/v1/orders/ord123"
    }
    response {
        status 200
        body(
            id: "ord123",
            status: "PENDING"
        )
    }
}
```

---

## Module 11: Security in Microservices

### 11.1 Authentication & Authorization
**JWT with Spring Security**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeRequests()
                .antMatchers("/auth/**").permitAll()
                .anyRequest().authenticated()
            .and()
            .httpBasic();
        
        return http.build();
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration config) 
            throws Exception {
        return config.getAuthenticationManager();
    }
}

@RestController
@RequestMapping("/auth")
public class AuthController {
    @PostMapping("/login")
    public ResponseEntity<?> login(@RequestBody LoginRequest request) {
        // verify credentials
        String token = jwtUtil.generateToken(request.getUsername());
        return ResponseEntity.ok(new LoginResponse(token));
    }
}
```

**JWT Verification Filter**
```java
@Component
public class JwtAuthFilter extends OncePerRequestFilter {
    @Autowired
    private JwtUtil jwtUtil;

    @Override
    protected void doFilterInternal(HttpServletRequest request, 
            HttpServletResponse response, 
            FilterChain filterChain) throws ServletException, IOException {
        
        String header = request.getHeader("Authorization");
        if (header != null && header.startsWith("Bearer ")) {
            String token = header.substring(7);
            if (jwtUtil.isValidToken(token)) {
                String username = jwtUtil.extractUsername(token);
                // Set authentication in context
                SecurityContextHolder.getContext().setAuthentication(
                    new UsernamePasswordAuthenticationToken(username, null, new ArrayList<>())
                );
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

### 11.2 Service-to-Service Authentication
**OAuth2 / Mutual TLS (mTLS)**
- Service A needs OAuth2 token to call Service B
- Feign client configuration with credentials
- mTLS for certificate-based authentication in Kubernetes

---

## Module 12: Common Interview Questions & Scenarios

### 12.1 Design Scenarios
1. "Design an e-commerce system with order, payment, and inventory services. How would you handle a failed payment after order creation?"
   - Answer: Saga pattern with compensation, event-driven cleanup

2. "How would you handle a service that's experiencing timeout issues when calling another service?"
   - Answer: Circuit breaker, timeout, retry with exponential backoff, fallback

3. "Design a notification service that sends emails/SMS to users asynchronously."
   - Answer: Kafka topics, event listeners, dedicated notification service, DLQ for failures

4. "How would you implement rate limiting across multiple microservice instances?"
   - Answer: Distributed rate limiting with Redis, API Gateway filtering

### 12.2 Troubleshooting Scenarios
1. "Your microservice is experiencing high latency. How do you debug it?"
   - Check logs (correlation IDs), distributed tracing, DB query performance, external API calls, network latency

2. "A distributed transaction failed midway. How do you recover?"
   - Saga compensations, manual cleanup, event replay, monitoring & alerting

3. "Service A is calling Service B frequently, causing performance issues. How do you optimize?"
   - Caching, batch operations, async processing, service consolidation, CQRS

### 12.3 Code Challenges
- Implement a circuit breaker
- Build a caching layer with invalidation
- Write a saga orchestrator
- Create an idempotent endpoint
- Implement distributed tracing

---

## Study Plan & Timeline

### Month 1: Fundamentals (Weeks 1–4)
- Week 1: Modules 1–2 (Architecture, Communication)
- Week 2: Module 3 (Distributed Challenges)
- Week 3: Module 4 (Spring Boot Basics)
- Week 4: Module 5 (Inter-Service Communication)

### Month 2: Advanced Topics (Weeks 5–8)
- Week 5: Module 6 (Data Management)
- Week 6: Module 7 (Logging & Monitoring)
- Week 7: Module 8 (API Gateway)
- Week 8: Module 9 (Containerization)

### Month 3: Testing & Deployment (Weeks 9–12)
- Week 9: Module 10 (Testing)
- Week 10: Module 11 (Security)
- Week 11–12: Consolidate, practice interview questions, code challenges

---

## Practice Resources

**Books**
- "Building Microservices" by Sam Newman
- "Microservices Patterns" by Chris Richardson
- "Spring in Action" (6th edition)

**Online Platforms**
- LeetCode System Design problems
- Educative.io: Grokking the System Design Interview
- YouTube: JavaTechie, Spring Framework Guru

**GitHub Repositories**
- spring-cloud-samples
- microservices-patterns examples
- Real-world microservices projects

**Tools to Practice With**
- Spring Boot starter templates
- Docker & Docker Compose for local setup
- Postman/Insomnia for API testing
- Prometheus + Grafana for monitoring

---

## Key Takeaways for Interviews

1. **Trade-offs**: Monolith vs. microservices, consistency vs. availability, complexity vs. scalability
2. **Resilience**: Circuit breakers, retries, timeouts, fallbacks
3. **Data Management**: Database per service, eventual consistency, saga pattern
4. **Observability**: Logging, tracing, metrics
5. **Testing**: Unit, integration, contract tests
6. **Real-world Scenarios**: Talk through past experiences, handle edge cases, consider failure scenarios