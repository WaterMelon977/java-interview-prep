# System Design Interview Questions by Frequency & Importance
## For Junior to Associate Java Backend Developers

---

## Frequency & Importance Analysis

### Topic Frequency Distribution (Based on Interview Data)

| Topic | Interview Frequency | Importance for Junior Role | Why |
|-------|---|---|---|
| **Database Design & Optimization** | ⭐⭐⭐⭐⭐ (90%) | ⭐⭐⭐⭐⭐ | Every backend role touches DBs; indexing, N+1, sharding are critical |
| **Caching Strategy** | ⭐⭐⭐⭐⭐ (85%) | ⭐⭐⭐⭐⭐ | Redis is ubiquitous; cache-aside, invalidation, TTL decisions are asked |
| **API Design & REST** | ⭐⭐⭐⭐⭐ (80%) | ⭐⭐⭐⭐ | Every system needs APIs; idempotency, versioning, pagination asked |
| **Rate Limiting & Throttling** | ⭐⭐⭐⭐ (75%) | ⭐⭐⭐⭐ | Core for protection; token bucket, sliding window, distributed state |
| **Consistency & Transactions** | ⭐⭐⭐⭐ (70%) | ⭐⭐⭐⭐ | ACID, eventual consistency, distributed transactions (saga pattern) |
| **Load Balancing & Scalability** | ⭐⭐⭐⭐ (70%) | ⭐⭐⭐⭐ | Horizontal scaling, stateless design, connection pooling |
| **Message Queues & Async Processing** | ⭐⭐⭐⭐ (65%) | ⭐⭐⭐⭐ | Decoupling, event-driven, Kafka/RabbitMQ patterns |
| **Microservices & Service Communication** | ⭐⭐⭐ (60%) | ⭐⭐⭐ | For mid-size systems; REST vs. gRPC, circuit breaker, retries |
| **Search & Indexing** | ⭐⭐⭐ (45%) | ⭐⭐⭐ | Elasticsearch, full-text search, less common for junior roles |
| **Monitoring, Logging, Observability** | ⭐⭐⭐ (40%) | ⭐⭐ | Non-functional; asked but lower priority in interviews |
| **Security & Auth** | ⭐⭐⭐ (50%) | ⭐⭐ | JWT, OAuth, encryption—more behavioral than system design |
| **CAP Theorem & Distributed Systems** | ⭐⭐ (35%) | ⭐⭐ | Conceptual foundation; rarely asked directly, often implicit |

---

## Interview Questions by Weightage

### TIER 1: HIGH FREQUENCY & HIGH IMPACT (Asked 70%+ of interviews)

These are the "must know" topics. You'll see variations of these in almost every system design round.

---

#### **1. Database Design & Optimization (90% frequency)**

**1.1 Basic Database Design**
- Design a user profile system (UserID, email, name, bio, avatar_url). How would you structure the schema? What indexes would you add?
- You have a users table with 100M rows. SELECT * FROM users WHERE email = 'user@example.com' is slow. How do you optimize?
- What's the N+1 query problem? How do you fix it in a Spring Boot + JPA application?
- You need to store a user's tags (a user has many tags). Should you use a separate tags table or JSON field? Why?

**1.2 Scaling Databases**
- Your single PostgreSQL database handles 10M users and is at 80% CPU. How do you scale read traffic?
- Explain master-slave replication. What happens when the master fails? How do you handle failover?
- When would you shard a database? How would you choose a shard key for a user-centric system?
- Design a sharding strategy for a system with 1B users. What's your shard key and why?

**1.3 Transactions & Consistency**
- What's the difference between ACID and BASE consistency? Give examples of each.
- A bank transfer moves money from Account A to Account B. How do you ensure atomicity across these two tables?
- You're using eventual consistency. A user views their account balance right after a transfer. What could go wrong?
- How would you implement optimistic locking in a Spring Boot JPA application?

**1.4 Advanced Scenarios**
- Design a leaderboard system with 100M players and score updates every second. How do you structure the database to handle 1M writes/sec?
- You have a payments table with billions of rows. New payment processing only needs recent data (last 30 days). How do you optimize?
- Design a notification system where each user receives 100+ notifications. How would you store and query them efficiently?

---

#### **2. Caching Strategy (85% frequency)**

**2.1 Cache Patterns & Trade-offs**
- Explain cache-aside, write-through, and write-behind patterns. When do you use each?
- You cache user profiles in Redis with a TTL of 1 hour. A user updates their profile. The cache still shows old data. How do you fix this?
- A frequently accessed cache key is set to expire every minute. Is this efficient? Why or why not?
- Your cache hit rate is 95%, but users complain about stale data. What's the trade-off and how do you balance it?

**2.2 Redis at Scale**
- You're caching 10M user sessions in Redis. A node fails. Do you lose all sessions? How do you prevent this?
- Design a Redis caching strategy for an e-commerce site. What gets cached? For how long? How do you invalidate?
- When would you use local in-memory caching (e.g., Guava Cache) vs. distributed Redis?
- Explain Redis expiration policies (LRU, LFU). Which would you use for a social feed cache?

**2.3 Cache Consistency**
- Your microservice writes to DB and then updates Redis cache. The cache update fails silently. How do you handle this?
- Design a cache invalidation strategy for a blog platform where users can like, comment, and follow. How do you keep all caches consistent?
- Describe a scenario where you'd use cache-aside over write-through, with numbers.

---

#### **3. API Design & REST (80% frequency)**

**3.1 REST Fundamentals**
- Design a REST API for an e-commerce system. What endpoints do you create? How do you handle relationships (user → orders)?
- A client calls POST /orders twice with the same data (due to network retry). Both orders are created. How do you fix this?
- Your API returns a User object with 50 fields. Mobile clients only need 5. How do you handle this without versioning?
- Design the API contract for a payment API. What does a success response look like? What about failures?

**3.2 Pagination & Filtering**
- You have an endpoint GET /users that returns 10M users. How do you paginate efficiently?
- Offset-based pagination uses LIMIT 1000 OFFSET 100000. This is slow on large tables. Why? What's an alternative?
- Design pagination for a timeline API where new posts are added constantly (cursor-based or offset-based?).
- How would you implement filtering on multiple fields (e.g., GET /products?category=books&min_price=10&max_price=100)?

**3.3 Versioning & Evolution**
- Your API has 10M clients. You need to change the response format. Can you modify the existing endpoint? How do you version?
- What's the difference between URL versioning (/v1/users), header versioning, and query param versioning?
- You deprecated an API endpoint 6 months ago. Should you still support it? For how long?

---

#### **4. Rate Limiting & Throttling (75% frequency)**

**4.1 Rate Limiting Algorithms**
- Implement rate limiting: 10 requests/second per user. How would you do this with Redis?
- Explain token bucket algorithm. A user has burst capacity of 20 requests and refill rate of 10/sec. How many requests can they make in the first second?
- Compare token bucket vs. sliding window vs. fixed window. Which would you use for a public API and why?
- A user gets rate limited after 10 requests/minute. After 1 minute, they still can't make requests. Why? (Hint: edge cases at boundaries)

**4.2 Distributed Rate Limiting**
- You have 5 API servers behind a load balancer. Each counts requests independently with 10 req/min limit. A user can actually make 50 req/min. How do you fix this?
- Implement global rate limiting across 10 servers using Redis. What race conditions can occur? How do you prevent them?
- Design rate limiting for a public API with multiple tiers: free tier (100 req/day), pro tier (10K req/day). How do you track usage?

**4.3 Handling Rate-Limited Requests**
- A user exceeds the rate limit. What HTTP status code do you return? What headers?
- How do you implement graceful degradation when rate limited (queuing requests for later retry)?

---

#### **5. Consistency & Distributed Transactions (70% frequency)**

**5.1 Consistency Models**
- Compare strong consistency vs. eventual consistency. Give 3 real-world examples of each.
- In a distributed system with eventual consistency, a user creates a post but doesn't see it immediately. Is this acceptable?
- What's causal consistency? When would you need it?

**5.2 Transactions in Distributed Systems**
- Design an e-commerce checkout: subtract inventory, charge payment, send confirmation email. These are 3 separate services. How do you ensure consistency?
- Explain the saga pattern (orchestration vs. choreography). Design an order saga with Inventory, Payment, and Notification services.
- A saga step fails (payment denied). How do you rollback previous steps? What about compensating transactions?
- Compare 2-phase commit (2PC) vs. saga pattern. Which is better and why?

**5.3 Distributed Locks & Consensus**
- Two users try to buy the last item in inventory simultaneously. How do you prevent both from succeeding?
- Implement distributed locking using Redis. What happens if the lock holder crashes?
- What's the difference between pessimistic locking and optimistic locking? When would you use each?

---

#### **6. Load Balancing & Scalability (70% frequency)**

**6.1 Scaling Strategies**
- You have a Spring Boot app handling 100 RPS. Traffic grows to 1000 RPS. Do you scale vertically or horizontally? Why?
- Design a stateless API service. Why is statefulness a problem for horizontal scaling?
- You have 10 API servers. How do you distribute traffic? What's the difference between round-robin and least-connections?
- How do you handle sticky sessions (if needed) across a load balancer?

**6.2 Load Balancer Design**
- A load balancer sends 90% of traffic to Server A and 10% to Server B. Why might this happen?
- How does a load balancer detect that a backend server is unhealthy? What's health check strategy?
- Design a load balancer that accounts for server capacity differences (some servers are more powerful).

**6.3 Preventing Cascading Failures**
- One slow backend server causes the load balancer to timeout and retry, overloading other servers. How do you fix this?
- Explain the bulkhead pattern. How does it differ from circuit breaker?

---

#### **7. Message Queues & Async Processing (65% frequency)**

**7.1 When to Use Message Queues**
- A user places an order. You need to: (1) charge payment, (2) update inventory, (3) send confirmation email, (4) log analytics. Should these be synchronous or async?
- Design an async flow using a message queue. What happens if the subscriber service is down?
- Compare synchronous REST calls vs. async messaging. When would you use each?

**7.2 Message Queue Patterns**
- Explain pub-sub vs. point-to-point messaging. Which would you use for order processing?
- A message queue has 100M unprocessed messages. Your consumer is processing 100/sec. How long to clear?
- Design message ordering: Order placed → Payment → Inventory update. Messages arrive out of order. How do you ensure ordering?

**7.3 Reliability & Idempotency**
- A payment confirmation message is processed twice, charging the user twice. How do you ensure messages are processed exactly once?
- A subscriber crashes mid-processing and never acknowledges the message. What happens?
- Implement idempotent message processing in Spring Boot.

---

### TIER 2: MEDIUM FREQUENCY & MEDIUM IMPACT (Asked 40–65% of interviews)

These topics appear regularly but are often secondary to Tier 1. Still essential for solid performance.

---

#### **8. Microservices & Service Communication (60% frequency)**

**8.1 Monolith vs. Microservices**
- When is a monolith a better choice than microservices for a startup?
- You have a monolithic e-commerce app with 50K lines of code. When would you split it into microservices?
- What are the hidden costs of microservices (latency, debugging, operational complexity)?

**8.2 Service-to-Service Communication**
- Compare REST vs. gRPC. Which would you use for a high-frequency trading system?
- Design a system where OrderService calls InventoryService. What happens if InventoryService is slow? How do you timeout?
- Explain circuit breaker pattern. When does it open, close, and half-open?
- Implement retry logic with exponential backoff. How many retries? Maximum backoff?

**8.3 Service Discovery**
- How does Service A discover the IP address of Service B if it scales to 10 instances?
- Compare client-side vs. server-side service discovery.

---

#### **9. Search & Indexing (45% frequency)**

**9.1 Full-Text Search**
- Design a search feature for a 1M-document blog platform. How would you index documents?
- A simple database query for search is slow. Why? How would Elasticsearch help?
- Design an autocomplete feature that suggests 10 results in <100ms for 50M documents.

**9.2 Indexing Strategies**
- Explain inverted indexes. Why are they used for full-text search?
- You're indexing a 10GB dataset. Should you do it in real-time or batch?

---

#### **10. Security & Authentication (50% frequency)**

**10.1 Authentication & Authorization**
- Design a JWT-based authentication system. How do you handle token expiry and refresh?
- A JWT token includes user_id and role. Is this secure?
- Compare stateless (JWT) vs. stateful (session cookies) authentication. When would you use each?

**10.2 API Security**
- How do you prevent SQL injection in a Spring Boot application?
- Design rate limiting to prevent brute-force attacks on a login endpoint.

---

### TIER 3: LOWER FREQUENCY & CONTEXTUAL (Asked <40% of interviews)

These are important but typically asked only if the system naturally involves them.

---

#### **11. Monitoring, Logging & Observability (40% frequency)**

**11.1 Observability Design**
- What metrics would you monitor for an e-commerce checkout service?
- Design a logging strategy for a microservices system with 20 services. How do you trace a request across all of them?
- How would you measure P99 latency for an API endpoint?

**11.2 Alerting**
- You set an alert: "if error rate > 5%, alert on-call engineer." False alarms occur daily. How do you improve the alert?

---

#### **12. CAP Theorem & Distributed Systems Theory (35% frequency)**

**12.1 CAP Theorem**
- Classify different databases (PostgreSQL, MongoDB, Cassandra) as CP, AP, or CA.
- You need consistency and partition tolerance but sacrifice availability. What trade-offs does this imply?

**12.2 Consensus & Replication**
- Explain RAFT consensus algorithm (overview, not deep dive).
- A master database replicates to 3 slaves. How many slaves must acknowledge a write for it to be durable?

---

## Interview Questions by Topic (Organized for Study)

### **MUST KNOW (Tier 1) – 80% of your prep time**

#### Database Design & Optimization
1. Design a schema for a Twitter-like system (users, tweets, likes, followers). What indexes would you add?
2. A query `SELECT * FROM products WHERE category = 'books'` on a 1B-row table is slow. How do you optimize?
3. Explain the N+1 query problem with a Spring Boot JPA example. How do you fix it?
4. How would you shard a users table with 1B rows? What's your shard key and how do you handle range queries?
5. Explain master-slave replication. What happens during failover?
6. Your database is at 80% CPU. Would you add more RAM, more CPUs, or more replicas? Why?
7. Design a leaderboard that updates 1M times per second. How do you structure the database?
8. You have a transactions table with 10B rows growing 100M/day. How do you optimize storage and query performance?
9. What's the difference between ACID and BASE? Give a real-world example of when you'd use each.
10. Design optimistic locking for a concurrent update scenario (e.g., two users editing the same document).

#### Caching Strategy
11. Explain cache-aside, write-through, and write-behind with trade-offs.
12. A cached user profile has a TTL of 1 hour. The user updates their profile but the cache shows old data for 59 minutes. How do you fix this?
13. You cache the top 10K queries in Redis. New queries are ignored. Is this a good strategy? Why or why not?
14. Design a caching strategy for a real-time stock price system. How often do you refresh? What's your TTL?
15. A Redis node fails and you lose all cached data. How do you prevent this?
16. When would you use local in-memory caching vs. distributed Redis caching?
17. Compare LRU, LFU, and TTL expiration policies for a cache. Which would you use for a social feed?
18. Your cache hit rate is 98% but users complain about stale data. How do you balance freshness and performance?
19. Design cache invalidation for a user's profile, posts, and follower list. How do you keep them consistent?
20. Implement write-through caching for an order system. What happens if the cache write fails?

#### API Design & REST
21. Design a REST API for an e-commerce platform. What are your main endpoints?
22. A POST request to create an order is retried due to network issues, creating duplicate orders. How do you ensure idempotency?
23. Your API returns a User object with 50 fields. Mobile clients need only 5. How do you handle this without breaking existing clients?
24. Design pagination for GET /users endpoint with 100M users. Would you use offset or cursor-based pagination?
25. A client requests GET /products?category=books&min_price=10&sort=price&page=5. How do you implement efficient filtering?
26. Design API versioning for a public API. Should you use URL paths (/v1/, /v2/), headers, or query params?
27. Your API needs to return different fields based on user role (admin sees more fields than guest). How do you design this?
28. Design the response format for API errors. What information do you include?
29. You need to deprecate an endpoint used by 100K clients. How do you handle this?
30. Design an API for file uploads. How do you handle large files? Resume capability?

#### Rate Limiting & Throttling
31. Implement rate limiting: 10 requests/second per user. What algorithm do you use?
32. Explain token bucket algorithm. If a user has a burst capacity of 20 and refill rate of 10/sec, how many requests in the first second?
33. Compare token bucket vs. sliding window vs. fixed window rate limiting.
34. You have 5 API servers with independent rate limiting. A user can exploit this to make 50 req/min instead of 10 req/min. How do you fix this?
35. Implement rate limiting across 10 servers using Redis. What race conditions can occur?
36. Design tiered rate limiting: free tier (100 req/day), pro tier (10K req/day).
37. When a user is rate limited, what HTTP status code and headers should you return?
38. How would you implement sliding window rate limiting with minimal Redis overhead?
39. Design rate limiting for a login endpoint to prevent brute-force attacks.
40. What happens at the boundary between rate limit windows? (e.g., 9:59:59 → 10:00:00)

#### Consistency & Distributed Transactions
41. Design a money transfer between two bank accounts in a distributed system.
42. Explain saga pattern (orchestration vs. choreography). Design an order saga.
43. In a saga, a step fails. How do you handle rollback with compensating transactions?
44. Compare 2-phase commit vs. saga. When would you use each?
45. Two users try to buy the last item simultaneously. How do you ensure only one succeeds?
46. Implement distributed locking using Redis. What if the lock holder crashes?
47. Explain optimistic vs. pessimistic locking with examples.
48. You use eventual consistency. A user views their account balance right after a transfer. What could go wrong?
49. Design a notification system where updates must be in order (event A before event B).
50. What's causal consistency and when would you need it?

#### Load Balancing & Scalability
51. Your app handles 100 RPS. Traffic grows to 1000 RPS. Vertical or horizontal scaling? Why?
52. Design a stateless API. Why is statefulness a problem for scaling?
53. You have 10 API servers. How do you distribute traffic? Round-robin or least-connections?
54. A load balancer sends 90% of traffic to Server A and 10% to Server B. Why?
55. How does a load balancer detect unhealthy servers?
56. Design a load balancer for servers with different capacities.
57. How do you handle sticky sessions in a distributed system?
58. A slow backend server causes timeouts and retry storms. How do you prevent this?
59. Explain bulkhead pattern. How is it different from circuit breaker?
60. Design a system that gracefully degrades when under high load.

#### Message Queues & Async Processing
61. A user places an order. Charge payment, update inventory, send confirmation email, log analytics. Sync or async?
62. Design async order processing using a message queue. What if the consumer crashes?
63. Explain pub-sub vs. point-to-point messaging. When would you use each?
64. A message queue has 100M messages. Consumer processes 100/sec. How long to clear?
65. Design message ordering for order processing (placed → paid → shipped).
66. A payment confirmation is processed twice, charging the user twice. How do you ensure exactly-once processing?
67. A subscriber crashes mid-processing. What happens to the message?
68. Implement idempotent message processing. How do you detect duplicates?
69. How do you implement dead-letter queues for failed messages?
70. Design a message queue that guarantees no data loss even if brokers crash.

---

### **SHOULD KNOW (Tier 2) – 15% of your prep time**

#### Microservices & Service Communication
71. When would you choose a monolith over microservices?
72. At what scale do you split a monolith into microservices?
73. Compare REST vs. gRPC for service-to-service communication.
74. Design communication between OrderService and InventoryService. How do you handle failures?
75. Explain circuit breaker pattern with state transitions.
76. Implement retry logic with exponential backoff.
77. How does service discovery work in a microservices architecture?
78. Compare client-side vs. server-side service discovery.

#### Search & Indexing
79. Design a search feature for a 1M-document blog platform.
80. Why is database full-text search slow? How does Elasticsearch improve it?
81. Explain inverted indexes.
82. Design autocomplete for 50M documents with <100ms latency.

#### Security & Authentication
83. Design JWT authentication. How do you handle token expiry and refresh?
84. Compare stateless (JWT) vs. stateful (session) authentication.
85. Design a login system that prevents brute-force attacks.
86. How do you prevent SQL injection in Spring Boot?

---

### **NICE TO KNOW (Tier 3) – 5% of your prep time**

#### Monitoring & Observability
87. What metrics would you monitor for an e-commerce checkout?
88. Design distributed tracing for a 20-service microservices system.
89. How would you measure P99 latency?

#### CAP Theorem & Theory
90. Classify PostgreSQL, MongoDB, Cassandra as CP, AP, or CA.
91. Explain RAFT consensus algorithm (high-level).

---

## How to Use This Document

### Study Strategy by Phase

**Phase 1 (Weeks 1–4): Tier 1 Only**
- Pick 1–2 questions per topic each day
- Code solutions (actual Spring Boot implementations)
- Aim for depth, not breadth

**Phase 2 (Weeks 5–8): Tier 1 Deep + Tier 2 Introduction**
- Combine Tier 1 questions into mini case studies
- Start Tier 2 questions (microservices, search)
- Practice mock interviews with Tier 1 questions

**Phase 3 (Weeks 9–12): Full Coverage + Mock Interviews**
- Practice full system design sessions (60 min)
- Tier 1: 40 min, Tier 2: 15 min, Tier 3: 5 min
- Use real interview platforms (Pramp, Interviewing.io)

### During Interviews

**Allocate Time:**
- Clarify requirements: 5 min
- High-level design: 10 min
- Tier 1 components deep dive: 20 min
- Tier 2 if asked: 10 min
- Q&A: 5 min

**Prioritize Tier 1 in discussions:**
- Always discuss database indexing
- Always discuss caching strategy
- Always discuss API design (pagination, idempotency)
- Always discuss rate limiting (if scale allows)

---

## Quick Reference: Top 20 Most-Asked Questions

These 20 questions appear in 80% of interviews. Master these first.

1. Design a schema for [social network / e-commerce / ride-sharing]. What indexes?
2. Explain N+1 query problem and how to fix it.
3. Design database sharding strategy for 1B users.
4. Explain cache-aside vs. write-through. When to use each?
5. A cached value is stale. How do you handle invalidation?
6. Design a REST API for [system]. What endpoints? How do you paginate?
7. Implement rate limiting: 10 req/sec per user.
8. How do you prevent duplicate order creation on POST retry?
9. Design saga pattern for order (inventory → payment → notification).
10. How do you scale from 100 RPS to 1000 RPS? Vertical or horizontal?
11. Explain distributed locking for concurrent inventory updates.
12. Compare strong consistency vs. eventual consistency with examples.
13. Design async message flow using message queue.
14. How do you prevent message duplication (exactly-once processing)?
15. Compare REST vs. gRPC for service communication.
16. Implement circuit breaker pattern.
17. Design load balancing for 10 servers.
18. Explain bulkhead pattern vs. circuit breaker.
19. Design cache invalidation for a user's profile + posts + followers.
20. How do you handle cascading failures in a distributed system?

---

## Question Breakdown by Topic (for Quick Review)

| Topic | # Questions | Priority |
|-------|-------------|----------|
| Database Design | 10 | 🔴 Must know |
| Caching Strategy | 10 | 🔴 Must know |
| API Design & REST | 10 | 🔴 Must know |
| Rate Limiting | 10 | 🔴 Must know |
| Consistency & Transactions | 10 | 🔴 Must know |
| Load Balancing & Scalability | 10 | 🔴 Must know |
| Message Queues | 10 | 🔴 Must know |
| Microservices | 8 | 🟡 Should know |
| Search & Indexing | 4 | 🟡 Should know |
| Security & Auth | 4 | 🟡 Should know |
| Monitoring & Observability | 3 | 🟠 Nice to know |
| CAP Theorem & Theory | 2 | 🟠 Nice to know |

**Total: 91 Interview Questions**

---

## Answer Strategy Template

For each question, structure your answer as:

1. **Clarify** – "Are we designing for 1M or 1B users? Read-heavy or write-heavy?"
2. **High-level** – "I'd use [pattern/structure] because [reason]."
3. **Tradeoffs** – "This choice improves [X] but trades off [Y]."
4. **Code/Details** – "In Spring Boot, I'd use [annotation/class]. Here's a sketch..."
5. **Scaling** – "If scale increases to [10x], I'd [change this]."

Interviewers value clear thinking over perfect answers. Show your reasoning.