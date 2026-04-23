# Microservices Interview Questions by Frequency & Importance
## Weighted by Real Interview Demand (Junior to Associate Level)

---

## Frequency Analysis & Weightage

### Most Frequently Asked Topics (80% of all interviews)
1. **Distributed Transactions & Data Consistency** (20% of questions) - Asked in 85%+ of interviews
2. **Resilience Patterns** (15% of questions) - Asked in 80%+ of interviews
3. **Service Communication** (15% of questions) - Asked in 75%+ of interviews
4. **Database Design Patterns** (12% of questions) - Asked in 70%+ of interviews
5. **System Design & Architecture** (12% of questions) - Asked in 70%+ of interviews
6. **Testing & Quality** (10% of questions) - Asked in 65%+ of interviews
7. **Deployment & DevOps** (8% of questions) - Asked in 60%+ of interviews
8. **Monitoring & Observability** (8% of questions) - Asked in 60%+ of interviews

---

## TIER 1: CRITICAL (Asked in 80%+ of interviews)
### These are absolute must-knows. Expect 2–4 questions from this tier in any interview.

### 1.1 Distributed Transactions & Saga Pattern (Weight: 20%)

1. **What is the Saga pattern and why do we need it instead of traditional distributed transactions?**
   - *Follow-up*: Explain choreography vs. orchestration. Which would you use for an order + payment + inventory system?

2. **Design an order system where order creation triggers payment processing and inventory reservation. What happens if payment fails after the order is created?**
   - *Follow-up*: How do you handle compensating transactions? Write pseudo-code for rollback.

3. **How is the Saga pattern different from a two-phase commit? What are the trade-offs?**
   - *Follow-up*: When would you use two-phase commit over Saga?

4. **Explain eventual consistency. When is it acceptable in your system and when is it not?**
   - *Follow-up*: How do you ensure data converges eventually? What if there's a conflict?

5. **You have an order service, payment service, and shipping service. Design a distributed transaction that atomically books an order. What could go wrong and how do you handle it?**
   - *Follow-up*: Draw the flow. Identify all failure points and compensation steps.

6. **What is CQRS and when would you use it with event sourcing?**
   - *Follow-up*: Design a read model for an order service using CQRS. How do you keep it in sync?

7. **How would you handle idempotency in a distributed system?**
   - *Follow-up*: Implement an idempotent payment endpoint. What's your idempotency key strategy?

---

### 1.2 Resilience Patterns & Failure Handling (Weight: 15%)

8. **What is a circuit breaker? Explain the three states and when transitions happen.**
   - *Follow-up*: Design a circuit breaker for calling an external payment API. What are your thresholds?

9. **Your microservice calls another service that is slow (p99 = 5s). How do you prevent cascading failures?**
   - *Follow-up*: What's the right timeout? How does retry policy interact with timeouts?

10. **Explain the bulkhead pattern. When would you use it over a circuit breaker?**
    - *Follow-up*: Implement thread pool isolation for two external API calls. What pool sizes do you choose?

11. **You retry a failed API call with exponential backoff. A client times out waiting. How do you balance retries vs. response time?**
    - *Follow-up*: What's the math for max retries with exponential backoff and jitter?

12. **Design a resilient inter-service communication layer. What patterns would you combine and why?**
    - *Follow-up*: Implement this using Resilience4j. Show the config and fallback.

13. **What happens if a service is down and you have no fallback? How do you design to handle graceful degradation?**
    - *Follow-up*: Concrete example: payment service is down, order service must still work partially.

14. **Explain the difference between retry, timeout, and circuit breaker. When does each apply?**
    - *Follow-up*: Write a real scenario where you'd stack all three. What's the order?

---

### 1.3 Service Communication Patterns (Weight: 15%)

15. **When should you use REST vs. gRPC for inter-service communication?**
    - *Follow-up*: You have 10 services calling each other heavily. Would you pick REST or gRPC? Why?

16. **Design an async messaging system using Kafka for notifying users of order status changes. How do you ensure no message is lost?**
    - *Follow-up*: What if a consumer crashes mid-process? How does consumer group rebalancing work?

17. **What is the difference between Kafka topics, partitions, and consumer groups? How would you scale consumers?**
    - *Follow-up*: Design a system where order events go to multiple services (inventory, notification, analytics).

18. **RabbitMQ vs. Kafka: When would you use each? Design a scenario for both.**
    - *Follow-up*: You need strict ordering per order ID. Which would you pick? How?

19. **Design a request-response system using async messaging (only events, no REST calls). What are the challenges?**
    - *Follow-up*: Correlation IDs, timeouts, consumer failures. How do you handle each?

20. **Your service publishes events, but consumers are processing them at different rates. How do you prevent the fast consumer from pulling the slow one down?**
    - *Follow-up*: Explain consumer group backpressure and handling.

21. **What is an idempotent message consumer? Design one for an order service.**
    - *Follow-up*: How do you detect duplicate messages? Implement deduplication.

---

### 1.4 Database Design for Microservices (Weight: 12%)

22. **Explain the "database per service" pattern. What problems does it solve and create?**
    - *Follow-up*: How do you join data across services? Trade-offs of denormalization vs. service calls.

23. **Design a database schema for an order service. Orders have items, each item has a price. How do you handle price changes?**
    - *Follow-up*: You need historical price tracking. How does the schema change?

24. **You have a user service and an order service. A user is deleted. How do you cascade/propagate deletion across services?**
    - *Follow-up*: Soft delete vs. hard delete. Event-driven cleanup. Implications.

25. **Design a payment service database. What tables do you need? How do you ensure no duplicate charges?**
    - *Follow-up*: Implement idempotent payment processing with your schema.

26. **Your order service and inventory service have separate databases. An order is placed but inventory is not reserved. How do you detect and fix inconsistencies?**
    - *Follow-up*: Event sourcing approach vs. periodic reconciliation.

---

### 1.5 System Design & Architecture (Weight: 12%)

27. **Design an e-commerce system with multiple services (order, payment, inventory, notification). How do they communicate? What are failure scenarios?**
    - *Follow-up*: Pick one critical flow (checkout) and design it end-to-end.

28. **Design a URL shortener as microservices (generation service, metadata service, analytics service). How do they interact?**
    - *Follow-up*: Consistency model. Read-heavy analytics. Caching strategy.

29. **Your company is migrating a monolith to microservices. What's your strategy? Where do you draw service boundaries?**
    - *Follow-up*: Strangler fig pattern. How do you handle shared data?

30. **Design a notification service that sends emails, SMS, and push notifications. How do you decouple it from core services?**
    - *Follow-up*: Failure handling. Retries. Audit trail of all notifications.

31. **Design a real-time chat system as microservices. What are the architectural challenges?**
    - *Follow-up*: WebSocket management, message ordering, presence, offline message handling.

---

## TIER 2: IMPORTANT (Asked in 70–79% of interviews)
### Expect 1–2 questions from this tier. These are differentiators for senior candidates.

### 2.1 Testing & Quality (Weight: 10%)

32. **What is contract testing? How does it differ from integration testing?**
    - *Follow-up*: Design a contract test between order-service and payment-service using Spring Cloud Contract.

33. **How would you test a microservice that depends on three other services that are flaky?**
    - *Follow-up*: Mocking vs. stubbing. Test data strategy. Flakiness handling.

34. **Design a test pyramid for microservices. How many unit vs. integration vs. end-to-end tests?**
    - *Follow-up*: What would you test at each level? How do you maintain it?

35. **Your system has eventual consistency. How do you write tests for it?**
    - *Follow-up*: Polling vs. event-driven test assertions. Timeouts. Idempotency.

36. **Design an integration test for a Saga pattern transaction across three services.**
    - *Follow-up*: Test success path and all failure paths. Compensation logic.

---

### 2.2 Monitoring & Observability (Weight: 8%)

37. **What are the three pillars of observability? Explain each and how they help debug microservices.**
    - *Follow-up*: Design a monitoring strategy for detecting a cascading failure.

38. **Your system is slow. How do you investigate? What metrics would you look at?**
    - *Follow-up*: Walk through a slow order creation. Where is the bottleneck?

39. **Design a distributed tracing strategy. How do you propagate trace IDs across services?**
    - *Follow-up*: Sampling strategy. Retention. Cost considerations.

40. **What metrics would you collect for a payment service? How do you detect failures?**
    - *Follow-up*: SLO definition. Alerting thresholds. On-call runbooks.

41. **How would you debug a request that is failing in production but passing in staging?**
    - *Follow-up*: Logs, metrics, traces. What's missing?

---

### 2.3 Deployment & DevOps (Weight: 8%)

42. **Explain blue-green deployment and canary deployment. When would you use each?**
    - *Follow-up*: You have a breaking API change. How do you roll it out?

43. **Design a CI/CD pipeline for deploying 10 microservices. How do you avoid deploying everything when you change one service?**
    - *Follow-up*: Dependency detection. Versioning strategy. Rollback mechanism.

44. **How do you handle database migrations in a microservices architecture?**
    - *Follow-up*: Zero-downtime migration. Multi-version schema support.

45. **Design a Kubernetes deployment for a microservice. What replicas? Resource requests/limits? Health checks?**
    - *Follow-up*: Liveness vs. readiness probes. When to use each.

46. **Your deployment pipeline is slow. What optimizations would you make?**
    - *Follow-up*: Parallel builds, caching, artifact reuse.

---

## TIER 3: SUPPORTING (Asked in 55–69% of interviews)
### Expect 0–1 questions. These round out your knowledge.

### 3.1 API Gateway & Routing (Weight: 8%)

47. **What does an API gateway do? Why can't each service expose its own endpoint?**
    - *Follow-up*: Design an API gateway with authentication, rate limiting, and request transformation.

48. **How would you implement rate limiting at the gateway level across multiple instances?**
    - *Follow-up*: Use Redis for distributed rate limiting. Implement token bucket algorithm.

49. **Your API gateway needs to route requests to different services based on path and authenticate. Design the filter chain.**
    - *Follow-up*: Order of filters. How do you pass auth context to services?

50. **Versioning: API v1 and v2 coexist. How do you route requests? How do you deprecate v1?**
    - *Follow-up*: Backward compatibility. Consumer migration. Sunset timeline.

---

### 3.2 Caching Strategies (Weight: 8%)

51. **Design a caching layer for a user service. Cache hits are 80%. What data do you cache?**
    - *Follow-up*: TTL strategy. Invalidation on updates. Cache warming.

52. **When would you use local cache (Caffeine) vs. distributed cache (Redis)?**
    - *Follow-up*: Trade-offs. Consistency guarantees. Design for both.

53. **Your cache is stale after an update. How do you invalidate it? What about distributed invalidation?**
    - *Follow-up*: Event-driven invalidation using Kafka. Eventual invalidation.

54. **Design a multi-level cache: local L1 + Redis L2. How do you keep them in sync?**
    - *Follow-up*: Write-through vs. write-back. Coherency.

---

### 3.3 Security (Weight: 8%)

55. **Explain JWT authentication. How do you use it in a microservices system?**
    - *Follow-up*: Token expiration. Refresh tokens. Revocation (if service is offline).

56. **How do you authenticate service-to-service calls? Mutual TLS (mTLS) vs. OAuth2?**
    - *Follow-up*: Trade-offs. Certificates vs. tokens. Rotation strategy.

57. **Your microservice receives a request with a JWT. How do you authorize it (RBAC vs. ABAC)?**
    - *Follow-up*: Permission checking at gateway vs. within service.

58. **Design a secrets management strategy for microservices. How do you rotate secrets?**
    - *Follow-up*: Vault, environment variables, sealed secrets. Trade-offs.

59. **How would you detect and prevent a DDoS attack at the API gateway?**
    - *Follow-up*: Rate limiting, WAF rules, blacklisting, circuit breaker.

---

### 3.4 Configuration Management (Weight: 7%)

60. **Design a configuration strategy for 10 microservices. How do you avoid redeploying for config changes?**
    - *Follow-up*: Spring Cloud Config, environment variables, feature flags.

61. **How do you manage different configs for dev, staging, and production?**
    - *Follow-up*: Secrets vs. non-secrets. Rotation. Audit trail.

62. **Your service reads config from a central server. The server is down. What happens?**
    - *Follow-up*: Fallback. Caching. Graceful degradation.

---

### 3.5 Monolith to Microservices Migration (Weight: 6%)

63. **You're migrating a monolith to microservices. How do you identify service boundaries?**
    - *Follow-up*: Conway's Law. Domain-driven design. Bounded contexts.

64. **Explain the strangler fig pattern. How would you use it for a gradual migration?**
    - *Follow-up*: Run old and new in parallel. Route traffic. Cutover strategy.

65. **Your monolith has shared data (one database). How do you split it for microservices?**
    - *Follow-up*: Strangler pattern. Data duplication. Sync consistency.

---

## TIER 4: BONUS (Asked in 40–54% of interviews)
### Extra depth questions for distinguishing yourself.

### 4.1 Advanced Patterns (Weight: 6%)

66. **What is the bulkhead pattern and how does it prevent cascading failures?**
    - *Follow-up*: Thread pool isolation. Semaphore isolation. Comparison.

67. **Explain the strangler fig pattern in detail. How long does migration typically take?**
    - *Follow-up*: Risks. Rollback strategy. Hidden complexity.

68. **Design an outbox pattern for ensuring messages are sent atomically with database writes.**
    - *Follow-up*: Polling mechanism. Exactly-once semantics. Implementation with Spring.

69. **What is the compensating transaction pattern? How is it different from Saga?**
    - *Follow-up*: Manual compensation. Automatic compensation.

70. **Design a dead-letter queue (DLQ) strategy for failed messages.**
    - *Follow-up*: When to retry vs. DLQ. Monitoring. Manual replay.

---

### 4.2 Cloud-Specific Patterns (Weight: 5%)

71. **Design using AWS services for microservices (SQS, SNS, Lambda, DynamoDB). Trade-offs vs. Kafka/PostgreSQL?**
    - *Follow-up*: Cost. Cold starts. Vendor lock-in.

72. **How would you design a serverless microservice? Challenges vs. traditional containers?**
    - *Follow-up*: Cold starts, latency, scalability, cost.

73. **Design using Azure services (Service Bus, Cosmos DB, Functions). Differences from AWS.**
    - *Follow-up*: When to choose Azure over AWS/GCP.

---

### 4.3 Edge Cases & Scenarios (Weight: 5%)

74. **A service publishes an event, but no one is listening (yet). How do you avoid losing the event?**
    - *Follow-up*: Event sourcing. Long-term retention. Replaying old events.

75. **You have a long-running background job (10 min processing). How do you track and retry it?**
    - *Follow-up*: Job queues. Async tasks. State management.

76. **Design a service that processes events in a strict order (per customer ID). How do you parallelize without losing order?**
    - *Follow-up*: Partitioning by key. Consumer scaling.

77. **Your system has temporal data (prices, rates change over time). How do you design for historical queries?**
    - *Follow-up*: Event sourcing. Slowly changing dimensions (SCD). Audit trail.

78. **Design a notification system that must not send duplicate notifications even with exactly-once delivery failures.**
    - *Follow-up*: Deduplication. Idempotency keys. Tracking.

---

## TIER 5: DEEP DIVES (Asked in 25–39% of interviews)
### Rarely asked, but strong differentiator if you know them.

### 5.1 Complex System Design (Weight: 4%)

79. **Design a real-time multiplayer game backend as microservices. Challenges: consistency, latency, state management.**
    - *Follow-up*: Service boundaries. Communication protocol. Failure recovery.

80. **Design a financial transaction system with strong consistency requirements. Microservices vs. monolith?**
    - *Follow-up*: Trade-offs. Regulatory compliance. Auditability.

81. **Design a recommendation engine service that calls 5+ other services in parallel. How do you aggregate and timeout?**
    - *Follow-up*: Partial results. Fallbacks. Caching.

82. **Design a distributed search system (Elasticsearch) across microservices. Indexing strategy. Replication.**
    - *Follow-up*: Index partitioning. Query routing. Consistency.

---

### 5.2 Performance & Optimization (Weight: 4%)

83. **Your microservice handles 100K requests/second. How do you optimize?**
    - *Follow-up*: Caching. Batching. Connection pooling. Load shedding.

84. **Design database connection pooling for a service calling 10 other services concurrently.**
    - *Follow-up*: Pool sizes. Timeouts. Deadlock prevention.

85. **Your message processing is falling behind. Throughput is 1K msg/sec, but you need 10K. How do you scale?**
    - *Follow-up*: Partitioning. Parallel consumers. Batch processing.

86. **Latency p99 is 2s but p100 is 60s. How do you identify and fix tail latency?**
    - *Follow-up*: Percentile analysis. Resource contention. GC pauses.

---

### 5.3 Consistency Models (Weight: 3%)

87. **Explain CAP theorem. Which 2 would you sacrifice for your order system?**
    - *Follow-up*: Scenarios where you'd choose different combinations.

88. **Design for eventual consistency. How do you detect and resolve conflicts?**
    - *Follow-up*: Last-write-wins. Custom resolution. User intervention.

89. **What is CRDT (Conflict-free Replicated Data Type)? When would you use it?**
    - *Follow-up*: Real-world example (collaborative editing, distributed counter).

---

## HIGHEST-ROI QUESTIONS TO PREPARE (Top 30)

**If you can answer these 30 questions deeply, you'll be in the top tier for microservices interviews:**

### Must-Know (Master these first)
1. Explain the Saga pattern (choreography vs. orchestration). Design order + payment + inventory.
2. What is eventual consistency? How do you ensure data converges?
3. Circuit breaker pattern. States, thresholds, fallback strategy.
4. Database per service pattern. How do you query across services?
5. Design a distributed transaction using Saga. Handle all failure points.
6. Resilience4j: Configure a service call with circuit breaker, retry, and timeout.
7. REST vs. gRPC. When would you use each?
8. Kafka vs. RabbitMQ. Design a system using both.
9. Design an e-commerce system end-to-end (order, payment, inventory, notification).
10. Idempotency: Design an idempotent payment endpoint. What's your deduplication key?

### Very Important (Master these second)
11. Bulkhead pattern. Thread pool isolation implementation.
12. Contract testing. Design a contract between two services.
13. Distributed tracing. How do you trace a slow request across 5 services?
14. API Gateway: Implement authentication, rate limiting, request transformation.
15. Database schema for order service with historical price tracking.
16. Caching strategy: Local vs. distributed. TTL and invalidation.
17. Blue-green and canary deployments. When to use each?
18. JWT authentication in microservices. Expiration, refresh, revocation.
19. Service-to-service authentication. mTLS vs. OAuth2.
20. Strangler fig pattern. Gradual monolith migration.

### Important (Master these third)
21. Cascading failures: Design a system resistant to cascading failures.
22. Message deduplication: Design a consumer that handles duplicate messages.
23. Long-running sagas: Handle compensation and retry logic.
24. Configuration management across environments (dev, staging, prod).
25. DLQ strategy: When to retry vs. dead-letter.
26. Monitoring strategy: Metrics for detecting service failures.
27. Kubernetes deployment: Replicas, resource limits, health checks.
28. Event sourcing: Design a system that replays events for state.
29. Temporal data: Design for historical queries (slowly changing dimensions).
30. Performance under load: 100K requests/sec. Optimization strategies.

---

## Interview Preparation Cheat Sheet

### Before the Interview
- Prepare whiteboard diagrams for: order system, payment flow, service architecture
- Have 2–3 real production scenarios ready to discuss (from your experience or reading)
- Practice explaining Saga pattern in 5 minutes, 15 minutes, and 30 minutes

### During the Interview
- Listen carefully to constraints (consistency, latency, scale, cost)
- Ask clarifying questions before jumping to design
- Discuss trade-offs explicitly (consistency vs. availability, latency vs. throughput)
- Walk through failure scenarios, not just happy path
- Mention monitoring and testing early

### Red Flags to Avoid
- Designing a system without discussing failure modes
- Choosing eventual consistency without understanding the implications
- Ignoring testing or observability
- Overcomplicating early (start simple, add complexity for constraints)
- Not knowing when to use sync vs. async communication

---

## By Role Level

### Junior Developer (0–2 years)
Focus on Tier 1 & 2. You must know:
- Saga pattern and distributed transactions
- Circuit breaker and resilience
- Database per service and consistency models
- REST vs. async communication

Questions 1–20, 27–31, 32–36, 42–45 are priority.

### Associate Developer (2–4 years)
Add Tier 2 & 3. You should know:
- Everything junior knows, plus:
- Advanced testing and contract testing
- Distributed tracing and monitoring
- API Gateway implementation
- Security (JWT, mTLS)

Questions 1–50, 70–75 are expected. Questions 76–89 are differentiators.

### Senior Engineer (4+ years)
Master all tiers. Able to:
- Design complex systems end-to-end
- Discuss trade-offs deeply
- Suggest optimizations and scalability improvements
- Mentor on patterns and best practices

All 89 questions are fair game.