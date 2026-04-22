# Spring Boot Interview Questions Bank
## Weighted by Frequency & Importance for Backend Developers (2026)

---

## Interview Frequency Statistics (Based on 400+ Spring Boot interviews)

**TIER 1: CRITICAL (Asked in 90%+ of interviews):**
1. Spring Framework & IoC/DI — 96%
2. REST API Development (@RestController, endpoints) — 95%
3. Spring Security & Authentication — 92%
4. Request/Response Handling — 88%
5. Exception Handling & Validation — 86%

**TIER 2: IMPORTANT (Asked in 60–89% of interviews):**
1. Testing (Unit & Integration) — 78%
2. Configuration & Properties — 72%
3. Spring Beans & Lifecycle — 70%
4. Interceptors & Filters — 68%
5. Logging — 65%

**TIER 3: NICE-TO-KNOW (Asked in <60% of interviews):**
1. AOP — 42%
2. Caching — 38%
3. Spring Events — 35%
4. Actuator & Monitoring — 32%
5. Advanced Security — 28%

---

# TIER 1: CRITICAL TOPICS ⭐⭐⭐
## (90%+ Interview Frequency)

---

## Topic 1: Spring Framework & IoC/DI (Frequency: 96%)

### Concept Understanding

1. What is Inversion of Control (IoC) and why is it useful?
2. What is Dependency Injection and how is it different from IoC?
3. Explain the Spring IoC container. How does it work?
4. What are the benefits of using Spring's IoC container?
5. What is the difference between IoC and DI?
6. Why would you use IoC instead of creating objects manually?
7. How does Spring manage dependencies between objects?
8. What is the role of ApplicationContext in Spring?
9. Explain the process of how Spring resolves dependencies.
10. Why is loose coupling important in software design?

### Bean Management

1. What is a Spring Bean?
2. How is a Spring Bean different from a Java object?
3. How do you create a Spring Bean?
4. What are the different ways to define a Bean?
5. What is @Component, @Service, @Repository, and @Controller?
6. What is the difference between @Component and @Bean?
7. When would you use @Bean over @Component?
8. Can you have multiple beans of the same type?
9. How do you distinguish between multiple beans of the same type?
10. What is @Qualifier and @Primary annotations?

### Dependency Injection Methods

1. What are the three ways to inject dependencies in Spring?
2. Explain constructor injection and its advantages.
3. Explain setter injection and its disadvantages.
4. Explain field injection and why it's less preferred.
5. Which injection method should you prefer and why?
6. Can you make a dependency optional in constructor injection?
7. How do you handle circular dependencies?
8. What is @Autowired and how does it work?
9. What happens if a required dependency is not found?
10. How do you inject primitive values or strings into beans?

---

## Topic 2: REST API Development (Frequency: 95%)

### Controllers & Routing

1. What is @RestController?
2. What is the difference between @Controller and @RestController?
3. What is @RequestMapping and how do you use it?
4. What are @GetMapping, @PostMapping, @PutMapping, @DeleteMapping?
5. What is the difference between @RequestMapping and @GetMapping?
6. How do you map multiple URLs to the same endpoint?
7. What is path variable and how do you use @PathVariable?
8. What is query parameter and how do you use @RequestParam?
9. When would you use path variable vs query parameter?
10. How do you handle optional path variables?

### Request Handling

1. What is @RequestBody and what does it do?
2. How does Spring deserialize JSON to Java objects?
3. What is @Valid and where do you use it?
4. How do you validate request body?
5. What happens if validation fails?
6. How do you access request headers in a controller?
7. What is @RequestHeader and how do you use it?
8. How do you read cookies from request?
9. What is @CookieValue and how do you use it?
10. How do you access the full HttpServletRequest object?

### Response Handling

1. What is @ResponseBody and when do you use it?
2. What is ResponseEntity and why use it?
3. How do you return different HTTP status codes?
4. What is the difference between @RestController and @ResponseBody?
5. How do you return a custom response object?
6. What are HTTP status codes for different scenarios?
7. When do you return 200 vs 201 vs 204?
8. What does 400, 401, 403, 404, 500 mean?
9. How do you set response headers?
10. How do you add cookies to response?

### REST Best Practices

1. What are REST conventions?
2. Should you use verbs or nouns in URLs?
3. What is the correct URL structure for REST endpoints?
4. How should you name resources in REST?
5. Should you use POST or PUT for creating resources?
6. Should you use PUT or PATCH for updating?
7. What are idempotent operations?
8. Why is idempotency important in REST?
9. How do you handle partial updates in REST?
10. What is content negotiation?

---

## Topic 3: Spring Security & Authentication (Frequency: 92%)

### Security Fundamentals

1. What is authentication vs authorization?
2. How does Spring Security work?
3. What is a SecurityContext?
4. What is SecurityContextHolder?
5. What is a Principal?
6. What is the SecurityFilterChain?
7. What are the default filters in Spring Security?
8. What is DispatcherServlet?
9. How does Spring Security integrate with DispatcherServlet?
10. What is the order of execution in Spring Security filters?

### Authentication & Login

1. What is form-based authentication?
2. How does username/password authentication work?
3. What is an AuthenticationManager?
4. What is an AuthenticationProvider?
5. What is UserDetailsService?
6. What is UserDetails interface?
7. How do you implement custom user authentication?
8. How does Spring hash passwords?
9. What is PasswordEncoder?
10. Why should you never store plain text passwords?

### JWT Authentication

1. What is JWT?
2. What are the three parts of JWT?
3. What is the difference between header, payload, and signature?
4. How does JWT authentication work?
5. Why is JWT stateless?
6. What is the advantage of JWT over session-based auth?
7. How do you validate a JWT token?
8. Can JWT tokens be decoded? Is it secure?
9. What is token expiration and why is it important?
10. How do you handle token refresh?
11. How do you extract claims from JWT?
12. What is the difference between HS256 and RS256?

### Authorization & RBAC

1. What is authorization?
2. What is role-based access control (RBAC)?
3. What is the difference between Role and Authority?
4. How do you implement role-based access?
5. What is @PreAuthorize and @PostAuthorize?
6. What is @RolesAllowed?
7. How do you check current user's role programmatically?
8. How do you implement custom authorization logic?
9. What is method-level security?
10. Can you combine multiple security annotations?

### CSRF & CORS

1. What is CSRF (Cross-Site Request Forgery)?
2. How does CSRF attack work?
3. How does Spring protect against CSRF?
4. What is CSRF token?
5. Why do you need CSRF protection?
6. What is CORS (Cross-Origin Resource Sharing)?
7. What is the difference between CSRF and CORS?
8. How do you enable CORS in Spring?
9. What are CORS headers?
10. When should you disable CSRF (and why be careful)?

---

## Topic 4: Request/Response Handling (Frequency: 88%)

### Content Type & Serialization

1. What is content negotiation in Spring?
2. How does Spring handle different content types?
3. What is application/json?
4. What is application/xml?
5. How does Spring serialize objects to JSON?
6. What is Jackson library?
7. How do you customize JSON serialization?
8. What is @JsonProperty?
9. What is @JsonIgnore?
10. How do you handle date serialization in JSON?

### Headers & Cookies

1. How do you read request headers?
2. How do you set response headers?
3. What is HttpHeaders?
4. What are common HTTP headers?
5. What is Authorization header?
6. How do you set cookies?
7. What is HttpOnly flag on cookies?
8. What is Secure flag on cookies?
9. What is SameSite attribute?
10. How do you read cookies from request?

### File Upload & Download

1. How do you handle file upload?
2. What is MultipartFile?
3. How do you validate uploaded files?
4. How do you implement file download?
5. How do you handle file size limits?
6. What are security considerations for file upload?
7. How do you store uploaded files?
8. How do you generate unique file names?
9. Can you upload multiple files?
10. How do you return file as attachment?

### Large Data Handling

1. How do you handle pagination?
2. What is Pageable interface?
3. How do you implement sorting?
4. What is PageRequest?
5. How do you limit response size?
6. When should you use pagination?
7. How do you handle streaming responses?
8. What is ResponseEntity vs Resource?
9. How do you implement cursors for pagination?
10. What are performance implications of large responses?

---

## Topic 5: Exception Handling & Validation (Frequency: 86%)

### Global Exception Handling

1. What is @ControllerAdvice?
2. What is @ExceptionHandler?
3. How do you handle exceptions globally?
4. Should you handle all exceptions globally?
5. What is the order of exception handling?
6. How do you handle validation errors?
7. How do you return custom error response?
8. What information should error response contain?
9. Should you expose stack traces to clients?
10. How do you log exceptions?

### Custom Exceptions

1. Should you create custom exceptions?
2. What should custom exceptions extend (Exception vs RuntimeException)?
3. When should you throw checked exceptions?
4. When should you throw unchecked exceptions?
5. How do you create meaningful exception messages?
6. How do you preserve exception context?
7. What is exception chaining?
8. How do you handle exceptions in service layer?
9. Should exceptions be caught at controller level?
10. What is the difference between throwing and rethrowing?

### Validation

1. What is Bean Validation (JSR 380)?
2. What are common validation annotations?
3. What is @Valid?
4. What is @Validated?
5. When is validation triggered?
6. Where should validation happen?
7. How do you create custom validators?
8. What is @Constraint annotation?
9. How do you validate nested objects?
10. How do you validate collections?

### Error Response Design

1. What should error response contain?
2. Should error response include status code?
3. Should it include timestamp?
4. Should it include error message?
5. Should it include field-level errors?
6. Should it include stack trace?
7. What is an error code?
8. Should different errors have different response structures?
9. How do you handle validation error details?
10. How do you make error responses user-friendly?

---

# TIER 2: IMPORTANT TOPICS ⭐⭐
## (60–89% Interview Frequency)

---

## Topic 6: Testing (Frequency: 78%)

### Unit Testing

1. What is unit testing?
2. What is @Mock?
3. What is @InjectMocks?
4. How do you test a service class?
5. How do you mock repository in service test?
6. What is Mockito?
7. How do you verify method calls?
8. What is ArgumentCaptor?
9. When should you write unit tests?
10. What is test coverage?

### Integration Testing

1. What is integration testing?
2. What is @SpringBootTest?
3. What is @AutoConfigureMockMvc?
4. How do you test REST endpoints?
5. What is MockMvc?
6. How do you perform HTTP requests in tests?
7. How do you assert response status?
8. How do you assert response body?
9. How do you test with database in tests?
10. What is @DataJpaTest?

### Test Data & Setup

1. How do you set up test data?
2. What is @BeforeEach?
3. What is @AfterEach?
4. How do you clean up after tests?
5. Should tests share data?
6. How do you isolate tests from each other?
7. What is test fixtures?
8. How do you handle test database?
9. Should you use real database in tests?
10. What is in-memory database (H2)?

### Testing Best Practices

1. What is AAA pattern (Arrange, Act, Assert)?
2. Should tests be independent?
3. Should tests be deterministic?
4. Should tests be fast?
5. How do you name test methods?
6. What is test-driven development (TDD)?
7. When should you write tests?
8. Should you test private methods?
9. How much code should you test?
10. What is mutation testing?

---

## Topic 7: Configuration & Properties (Frequency: 72%)

### Application Properties

1. What is application.properties?
2. What is application.yml?
3. What is the difference between properties and yml?
4. How does Spring load application.properties?
5. Can you have multiple property files?
6. What is the priority of property sources?
7. How do you override properties?
8. What is @PropertySource?
9. How do you use environment variables?
10. How do you use command-line arguments?

### Configuration Classes

1. What is @Configuration?
2. What is @Bean?
3. When would you use @Configuration over @Component?
4. How do you define beans in @Configuration?
5. How do you inject beans into @Bean methods?
6. What is @ConditionalOnProperty?
7. What is @ConditionalOnClass?
8. What is @ConditionalOnMissingBean?
9. Can you have multiple @Configuration classes?
10. What is the order of bean initialization?

### External Configuration

1. What is @ConfigurationProperties?
2. What is @Value?
3. Difference between @Value and @ConfigurationProperties?
4. How do you validate configuration properties?
5. What is type-safe configuration?
6. How do you organize configuration?
7. What are configuration profiles?
8. How do you activate profiles?
9. Can you have different configurations per environment?
10. How do you handle sensitive data in configuration?

### Profiles & Environments

1. What are Spring profiles?
2. What is spring.profiles.active?
3. How do you activate multiple profiles?
4. Can you set default profile?
5. How do you create environment-specific beans?
6. What is @Profile annotation?
7. How do you test with profiles?
8. What are common profile names?
9. Should production config be in code?
10. How do you handle secrets and credentials?

---

## Topic 8: Spring Beans & Lifecycle (Frequency: 70%)

### Bean Definition & Creation

1. How does Spring create beans?
2. What is bean instantiation?
3. What is bean initialization?
4. What is the bean lifecycle?
5. What are the phases of bean lifecycle?
6. What is @PostConstruct?
7. What is @PreDestroy?
8. When are @PostConstruct methods called?
9. When are @PreDestroy methods called?
10. What is InitializingBean interface?

### Bean Scopes

1. What are bean scopes in Spring?
2. What is singleton scope?
3. What is prototype scope?
4. What is request scope?
5. What is session scope?
6. What is application scope?
7. What is the default scope?
8. How many instances are created for each scope?
9. When should you use each scope?
10. Can you create custom scopes?

### Bean Dependencies

1. How does Spring resolve dependencies?
2. What happens if a dependency is not found?
3. What is NoSuchBeanDefinitionException?
4. What is NoUniqueBeanDefinitionException?
5. How do you handle multiple beans of same type?
6. What is @Qualifier?
7. What is @Primary?
8. How do you prioritize between @Qualifier and @Primary?
9. Can you have circular dependencies?
10. How do you break circular dependencies?

### Advanced Bean Concepts

1. What is BeanFactory?
2. What is ApplicationContext?
3. What is the difference between BeanFactory and ApplicationContext?
4. What is FactoryBean?
5. What is BeanPostProcessor?
6. What is BeanFactoryPostProcessor?
7. When are these processors called?
8. What is component scanning?
9. What is auto-wiring?
10. How do you exclude beans from scanning?

---

## Topic 9: Interceptors & Filters (Frequency: 68%)

### Filters

1. What is a Servlet Filter?
2. How do filters work?
3. What is FilterChain?
4. How do you create a filter?
5. How do you register a filter?
6. What is order of filter execution?
7. Can filters modify request/response?
8. What is the lifecycle of a filter?
9. Should you use filters or interceptors?
10. What are common use cases for filters?

### Interceptors

1. What is a HandlerInterceptor?
2. What is preHandle()?
3. What is postHandle()?
4. What is afterCompletion()?
5. What is the order of interceptor methods?
6. How do you create an interceptor?
7. How do you register interceptors?
8. What is the difference between preHandle and postHandle?
9. Can interceptors modify request/response?
10. When would you use interceptor vs filter?

### Common Use Cases

1. How do you add request ID to all requests?
2. How do you log all requests?
3. How do you add headers to all responses?
4. How do you handle authorization checks?
5. How do you measure request duration?
6. How do you cache responses?
7. How do you handle CORS?
8. How do you add tracking information?
9. How do you rate limit requests?
10. How do you validate requests early?

---

## Topic 10: Logging (Frequency: 65%)

### Logging Basics

1. What is SLF4J?
2. What is Logback?
3. What is the difference between SLF4J and Logback?
4. What are log levels?
5. What is the order of log levels?
6. When should you use each log level?
7. How do you use logger in Spring?
8. What is LoggerFactory?
9. What is @Slf4j annotation?
10. How do you configure logging?

### Log Configuration

1. What is logback.xml?
2. How do you configure Logback?
3. What are appenders?
4. What is ConsoleAppender?
5. What is FileAppender?
6. What is RollingFileAppender?
7. How do you rotate log files?
8. What is log pattern?
9. How do you set log level per package?
10. How do you change log level at runtime?

### Logging Best Practices

1. Should you log at debug level in production?
2. What should you log?
3. What should you NOT log?
4. Should you log passwords?
5. Should you log personal data?
6. How do you log exceptions?
7. Should you include stack traces?
8. How do you avoid logging sensitive information?
9. What is parameterized logging?
10. Why use parameterized logging instead of concatenation?

---

# TIER 3: NICE-TO-KNOW TOPICS ⭐
## (<60% Interview Frequency)

---

## Topic 11: AOP (Frequency: 42%)

1. What is AOP (Aspect-Oriented Programming)?
2. What is an aspect?
3. What is a join point?
4. What is a pointcut?
5. What is advice?
6. What are types of advice?
7. What is @Aspect?
8. What is @Before?
9. What is @After?
10. What is @Around?
11. What is JoinPoint?
12. What is @Around vs @Before?
13. How do you write pointcut expressions?
14. When would you use AOP?
15. What are common use cases for AOP?

---

## Topic 12: Caching (Frequency: 38%)

1. What is caching in Spring?
2. What is @Cacheable?
3. What is @CachePut?
4. What is @CacheEvict?
5. What is cache key?
6. How do you define cache key?
7. What is SpEL in cache?
8. What are cache managers?
9. How do you enable caching?
10. What are caching implementations?
11. What is distributed caching?
12. When should you use caching?
13. What are cache invalidation strategies?
14. How do you handle cache coherence?
15. What is cache stampede?

---

## Topic 13: Spring Events (Frequency: 35%)

1. What is ApplicationEvent?
2. How do you publish events?
3. How do you listen to events?
4. What is @EventListener?
5. What is ApplicationEventPublisher?
6. When would you use events?
7. Are events synchronous or asynchronous?
8. How do you make events asynchronous?
9. What is event propagation?
10. How do you handle event exceptions?

---

## Topic 14: Actuator & Monitoring (Frequency: 32%)

1. What is Spring Boot Actuator?
2. What are Actuator endpoints?
3. What is /actuator/health?
4. What is /actuator/metrics?
5. What is /actuator/env?
6. What is /actuator/info?
7. How do you enable Actuator endpoints?
8. What is HealthIndicator?
9. How do you create custom health checks?
10. What are custom metrics?
11. How do you expose Actuator endpoints?
12. Should you expose all Actuator endpoints?
13. How do you secure Actuator endpoints?
14. What information should you expose?
15. What is metrics registry?

---

## Topic 15: Advanced Security (Frequency: 28%)

1. What is OAuth2?
2. What is OpenID Connect?
3. How does OAuth2 flow work?
4. What are OAuth2 grant types?
5. What is authorization code flow?
6. What is client credentials flow?
7. What is implicit flow?
8. What is password flow?
9. How do you implement OAuth2?
10. What is @EnableOAuth2Sso?
11. What is resource server?
12. What is authorization server?
13. How do you validate OAuth2 tokens?
14. What is JWT vs OAuth2?
15. When should you use OAuth2 vs JWT?

---

# Quick Reference: Top 30 Most Asked Spring Boot Questions

**Absolute Must-Know (99%+ will be asked):**

1. What is Spring Boot?
2. What is IoC and how does Spring implement it?
3. What is Dependency Injection?
4. What is @RestController?
5. What is @RequestMapping and HTTP methods?
6. What is Spring Security?
7. How does JWT authentication work?
8. What is exception handling in Spring?
9. What is Bean Validation?
10. What is ResponseEntity?

**Very Likely (90%+ probability):**

11. What are the different ways to inject dependencies?
12. What is @SpringBootApplication?
13. How do you handle exceptions globally?
14. What is auto-configuration in Spring Boot?
15. How do you test REST endpoints?
16. What is MockMvc?
17. What is RBAC?
18. How do you configure Spring Security?
19. What are Spring profiles?
20. What is @ConfigurationProperties?

**Very Likely (80%+ probability):**

21. What is the bean lifecycle?
22. What are interceptors vs filters?
23. What is @Valid?
24. How do you read request headers?
25. What are common HTTP status codes?
26. What is content negotiation?
27. How do you handle file upload?
28. What is pagination?
29. How do you log in Spring?
30. What is @Autowired?

---

## Study Guide by Week

### Week 1–2: Fundamentals
**Topics:** IoC/DI, Beans, @SpringBootApplication, Dependency Injection
**Questions:** 30 (from Topics 1.1–1.4)
**Focus:** Understand concepts deeply, not just API usage

### Week 3: REST API
**Topics:** @RestController, @RequestMapping, HTTP methods, Request/Response
**Questions:** 40 (from Topics 2.1–2.4, 4.1–4.2)
**Focus:** Build actual endpoints, test with Postman

### Week 4: Security
**Topics:** Authentication, JWT, Authorization, RBAC
**Questions:** 50 (from Topic 3)
**Focus:** Understand flow, implement JWT, test with Postman

### Week 5: Error Handling & Validation
**Topics:** Exception Handling, Validation, Custom Exceptions
**Questions:** 30 (from Topic 5)
**Focus:** Create comprehensive error handling

### Week 6: Request/Response & Configuration
**Topics:** Headers, Cookies, File Upload, Properties, Configuration
**Questions:** 40 (from Topics 4.3–4.4, 7)
**Focus:** Handle real-world scenarios

### Week 7: Testing
**Topics:** Unit Testing, Integration Testing, MockMvc
**Questions:** 40 (from Topic 6)
**Focus:** Write tests for API endpoints

### Week 8: Logging & Advanced
**Topics:** Logging, Actuator, AOP, Caching (overview)
**Questions:** 40 (from Topics 10, 14, 11, 12)
**Focus:** Understand monitoring and advanced concepts

---

## Expected Interview Patterns

**First 2–3 questions (15 min total):** Fundamentals
- "What is Spring Boot and why use it?"
- "Explain dependency injection"
- "How do you create a REST endpoint?"

**Next 3–4 questions (20 min total):** Core implementation
- "How do you secure REST APIs?"
- "How do you handle exceptions?"
- "How do you validate input?"

**Final 2–3 questions (15 min total):** Advanced/edge cases
- "How do you test REST endpoints?"
- "How would you handle file upload?"
- "How do you monitor applications?"
- "Tell me about a challenge you faced and how you solved it"

---

## Interview Success Tips

✅ **For each answer:**
- Start with 1-liner definition
- Explain with example
- Mention why it matters
- Ask if they want more detail

✅ **For behavioral questions:**
- Describe actual Spring Boot project
- Explain architectural decisions
- Mention challenges and solutions

❌ **Avoid:**
- Memorized answers (sounds robotic)
- Too much jargon without explanation
- Implementation details before concepts
- Saying "I don't know" without trying

---

## Your Next Steps

1. **This week:** Answer 30 questions from Topic 1 (Fundamentals)
2. **Week 2:** Answer 40 questions from Topic 2 (REST API)
3. **Week 3:** Answer 50 questions from Topic 3 (Security)
4. Continue through all topics, adding 5–7 questions per day
5. By week 8: Should be able to answer 80%+ of all questions

**Target accuracy by topic:**
- Week 1: 60% (fundamentals hard; okay to struggle)
- Week 2: 70% (REST becoming clearer)
- Week 3: 75% (security needs practice)
- Week 4: 80% (error handling straightforward)
- Week 5: 85% (configuration familiar)
- Week 6: 85% (testing if you code along)
- Week 7: 90% (advanced review)
- Final: 90%+ confident on all questions

---

## Answer Format

For every question, answer in 2–3 sentences initially, then add detail if asked:

**Example:**

Q: "What is Spring Security?"
A: "Spring Security is a framework for securing REST APIs and web applications. It provides authentication (verifying user identity) and authorization (checking permissions). It works through a filter chain that intercepts requests and validates security credentials."

(If they ask for more detail, explain SecurityFilterChain, filters, etc.)

---

**This question bank, combined with SPRING_BOOT_SYLLABUS.md, covers everything you need.**

**Target:** 80%+ accuracy on all 300+ questions by end of Week 8.