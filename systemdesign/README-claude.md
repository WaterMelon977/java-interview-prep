# System Design Syllabus for Java Backend Developers
## Junior to Associate Level (0–3 YOE)

---

## 1. Foundation: Core Concepts (Week 1–2)

### 1.1 Scalability Basics
- **Vertical vs. Horizontal Scaling**
  - When to use each; trade-offs (cost, complexity, latency)
  - Example: E-commerce platform handling 10K → 100K concurrent users
- **Load Balancing**
  - Round-robin, least connections, weighted distribution
  - Sticky sessions vs. stateless design
  - Practical: How a load balancer distributes traffic to Java app instances
- **Caching Strategies**
  - Cache-aside (Lazy Loading), Write-through, Write-behind
  - When to cache (read-heavy vs. write-heavy workloads)
  - Tools: Redis, Memcached (distributed caches for microservices)

### 1.2 Consistency & Availability
- **CAP Theorem**
  - CP, AP, CA trade-offs with real examples (SQL databases, NoSQL)
  - Why Java devs care: ACID vs. BASE in distributed systems
- **Consistency Models**
  - Strong consistency (synchronous writes) vs. Eventual consistency (async replicas)
  - Example: Payment systems (strong) vs. Social feeds (eventual)

### 1.3 Database Fundamentals
- **SQL vs. NoSQL: When & Why**
  - Relational (PostgreSQL, MySQL): structured, ACID, complex queries
  - Document (MongoDB): flexible schema, horizontal scaling
  - Key-value (Redis): ultra-fast reads, no complex queries
  - Time-series (InfluxDB): metrics, logs, sensor data
- **Indexing & Query Optimization**
  - B-tree indexes, composite indexes
  - EXPLAIN plans, slow query logs
  - N+1 query problem (critical for Java/JPA developers)
- **Replication & Sharding**
  - Master-slave, multi-master replication
  - Sharding strategies (range, hash, geographic)
  - Rebalancing challenges

**Deliverable:** Design a simple user authentication system with caching. Diagram shows: Client → Load Balancer → Java instances → Cache (Redis) → Database (PostgreSQL).

---

## 2. Java-Specific Architecture Patterns (Week 3–4)

### 2.1 Monolith vs. Microservices
- **When Monoliths Work** (junior-level reality)
  - 1–3 engineers, <10K RPS, single domain
  - Easier debugging, simpler transactions
- **When Microservices Make Sense**
  - Independent deployment, team scaling, domain separation
  - Cost: complexity in distributed debugging, eventual consistency, network calls
- **Practical Trade-offs**
  - Monolith: Deploy all, scale all (wasteful for 1 service spike)
  - Microservices: Scale specific service, but manage service discovery, circuit breakers

### 2.2 Service Communication Patterns
- **Synchronous (REST, gRPC)**
  - REST (JSON over HTTP): simple, widespread
  - gRPC (Protocol Buffers): high performance, typed contracts
  - Timeout & retry strategies (Hystrix, Spring Cloud Resilience4j)
  - Circuit breaker pattern (prevent cascade failures)
- **Asynchronous (Messaging)**
  - Pub-Sub (Kafka, RabbitMQ, AWS SNS)
  - Event sourcing & CQRS basics
  - Use case: Order placed → Payment service, Notification service, Analytics (async, decoupled)

### 2.3 Spring Boot Patterns & Best Practices
- **Layered Architecture** (Controller → Service → Repository → DB)
  - Why it works: separation of concerns, testable
  - Spring Data JPA for data access (avoid N+1)
- **Dependency Injection & Testing**
  - Constructor injection for immutability
  - Mocking with Mockito for unit tests
- **Error Handling & Resilience**
  - Global exception handlers (@ControllerAdvice)
  - Retry logic (Spring Retry or Resilience4j)
  - Timeouts and backoff strategies

**Deliverable:** Refactor a monolithic e-commerce app into two microservices (Order Service, Inventory Service). Diagram: Show API Gateway, two Spring Boot services, separate databases, async messaging for order events.

---

## 3. Real-World Case Studies (Week 5–6)

### 3.1 URL Shortener (Tinyurl-like)
**Why this case?** Tests basic CRUD, hashing/encoding, caching, and rate limiting.

**Requirements:**
- Convert long URL → short URL (e.g., bit.ly/abc123)
- 1M DAU, 100 requests/second peak
- 99.9% availability

**Design Points:**
1. **API Design**
   - POST /shorten (body: long_url) → {short_code}
   - GET /{short_code} → redirect to long_url
2. **Data Model**
   - Table: short_codes (id, long_url, short_code, created_at, expiry)
   - Index on short_code for fast lookups
3. **Encoding Strategy**
   - Base62 encoding of ID (numeric → alphanumeric)
   - Collision handling: retry or incremental counter
4. **Caching**
   - Redis cache for popular short codes (80/20 rule)
   - TTL: 30 days (match expiry logic)
5. **Rate Limiting**
   - Token bucket: 10 shorten requests/minute per user
   - Use Redis for distributed rate limiting (sliding window)
6. **Scalability**
   - Single database write bottleneck? Use ID generator service (Twitter Snowflake or UUID)
   - Read replicas for GET requests
   - Horizontal scaling: API Gateway → N Spring Boot instances (stateless)

**Scaling to 100K RPS:**
- Implement database sharding (by short_code range or hash)
- CDN for redirects (cache 302 responses)
- Analytics asynchronously (Kafka → data warehouse)

**Java Implementation Highlights:**
```
- Controller: @PostMapping, @GetMapping, return ResponseEntity
- Service: Encode logic, cache checks, DB operations
- Repository: JPA with custom @Query for short_code lookup
- Resilience: Circuit breaker on cache, fallback to DB
```

### 3.2 Rate Limiting Service (Token Bucket / Sliding Window)
**Why?** Critical for protecting backend; tests concurrent state management & distributed systems.

**Requirements:**
- Allow 10 requests/second per user
- Distributed (multiple API servers must share state)
- Low latency (<1ms overhead)

**Design:**
1. **Sliding Window with Redis**
   - Key: user_id
   - Increment counter with expiry window
   - Compare against limit before allowing request
2. **Token Bucket Alternative**
   - Store tokens + refill rate in Redis
   - Subtract tokens on request, refill periodically
3. **Code Pattern (Spring Boot + Resilience4j)**
   - Use @RateLimiter annotation (out-of-box)
   - Custom Redis implementation for distributed case

**Trade-offs:**
- Token bucket: smooth bursts, complex
- Sliding window: simple, counts last N seconds

### 3.3 Search Autocomplete (Type-ahead)
**Why?** Tests caching, trie data structures, and performance.

**Requirements:**
- User types "java" → suggest ["java", "javascript", "java spring"]
- <100ms response, top 10 results
- 50M searches/day

**Design:**
1. **Data Structure: Trie + Cache**
   - Trie for fast prefix matching (O(m) where m = query length)
   - Cache top 10K queries in Redis (covers 80% of traffic)
2. **Offline + Online**
   - Batch job (nightly): compute top queries, rebuild trie
   - Online: serve from cache, fallback to trie
3. **Ranking**
   - By frequency + recency (MongoDB, weighted score)
   - Personalization (user history in Redis)

**Java Implementation:**
```
- Trie implementation: nested Map<Character, TrieNode>
- Redis: cache for (query → [suggestions])
- Batch: Spark job to compute frequencies from logs
- API: GET /autocomplete?q=java
```

### 3.4 E-Commerce Order System (Inventory + Payments)
**Why?** Tests distributed transactions, saga pattern, and event-driven architecture.

**Requirements:**
- User places order → reserve inventory, process payment, send confirmation
- Consistency: inventory must match orders
- 1000 orders/second

**Design Challenges:**
1. **Distributed Transaction Problem**
   - Order Service, Inventory Service, Payment Service (separate DBs)
   - Solution: Saga Pattern (Orchestration or Choreography)
     - **Orchestration:** OrderService calls Inventory, then Payment (centralized)
     - **Choreography:** Events trigger services (Order placed → Inventory reserves → Payment processes)
   - Handling failure: Compensating transactions (rollback logic)

2. **Data Consistency**
   - Optimistic locking (version fields) to prevent double-booking
   - Eventual consistency with event sourcing

3. **Code Example (Orchestration Saga)**
   ```
   PlaceOrderRequest → OrderService:
     1. Create order (status: PENDING)
     2. Call InventoryService.reserve() → on success, continue; on failure, reject
     3. Call PaymentService.charge() → on success, mark order CONFIRMED; on failure, undo inventory
     4. Publish OrderConfirmedEvent → Notification service
   ```

---

## 4. Non-Functional Requirements (Week 7)

### 4.1 High Availability & Reliability
- **Redundancy**
  - Multi-region deployments (primary, standby)
  - Database replication (master-slave, quorum-based)
  - Health checks & auto-failover
- **Monitoring & Alerting**
  - Metrics (P99 latency, error rates, throughput)
  - Distributed logging (ELK stack, Datadog)
  - SLOs/SLIs (Service Level Objectives/Indicators)
- **Graceful Degradation**
  - Feature flags for quick rollbacks
  - Fallback logic (serve cached data if DB slow)
  - Bulkhead pattern (isolate failures)

### 4.2 Security
- **Authentication & Authorization**
  - JWT vs. Session cookies (stateless vs. stateful)
  - OAuth 2.0 for third-party integrations
- **Data Protection**
  - Encryption in transit (TLS)
  - Encryption at rest (database, backups)
  - API key rotation, password hashing (bcrypt)
- **Common Threats**
  - SQL injection (use parameterized queries, JPA)
  - CSRF, XSS (relevant for frontend integration)
  - Rate limiting (prevent brute force, DoS)
- **Compliance**
  - GDPR: data retention, right to be forgotten
  - PCI-DSS for payments (don't store card data)

### 4.3 Performance Optimization
- **Latency Reduction**
  - Caching (multi-level: app, Redis, CDN)
  - Connection pooling (HikariCP for Java)
  - Async processing (ExecutorService, Spring @Async)
  - Query optimization (indexes, pagination)
- **Throughput Improvement**
  - Batch processing (collect 100 events, write once)
  - Denormalization for read-heavy paths
  - Database read replicas
- **Resource Efficiency**
  - Memory: object pooling, GC tuning
  - Disk: log rotation, data archival
  - Network: compression, protocol choice

---

## 5. Communication & Trade-Off Frameworks (Week 8)

### 5.1 How to Structure Your Answer
**Key: GREASE Framework (Google's approach)**
1. **Goals** – Clarify functional & non-functional requirements
2. **Requirements** – Scale, latency, availability, consistency
3. **Exploration** – Brainstorm components & trade-offs
4. **Architecture** – Draw diagram, explain data flow
5. **System Design** – Detail critical components (DB choice, caching strategy)
6. **Evaluation** – Discuss bottlenecks, improvements, scaling

**In an Interview:**
- Ask clarifying questions first (don't assume 1M vs. 1B users)
- Draw as you talk (Miro, whiteboard sketch in description)
- Explain trade-offs: "I chose REST over gRPC because interop is simpler; if latency becomes critical, we pivot to gRPC."
- Be ready to zoom in on any component

### 5.2 Common Trade-Off Discussions
| Scenario | Option A | Option B | When A, When B |
|----------|----------|----------|---|
| Consistency | Strong (ACID) | Eventual (BASE) | Payment systems | Social feeds |
| Communication | Sync (REST) | Async (Kafka) | Real-time | Decoupled, latency OK |
| Storage | SQL | NoSQL | Relations, ACID | Schema-less, scale horizontal |
| Caching | Cache-aside | Write-through | Reads >> writes | Reads = writes |
| Sharding | Range | Hash | Time-based | Uniform distribution |

---

## 6. Practice Projects (Week 9–12)

### Project 1: Rate Limiter Service (2–3 hours)
**Stack:** Spring Boot + Redis
- Implement token bucket or sliding window
- Write unit tests (Mockito)
- Load test with JMH or ApacheBench

**Success Criteria:**
- <1ms overhead per request
- Handles distributed state (Redis cluster simulation)
- Clear API contract (pass/fail decision)

### Project 2: URL Shortener (4–5 hours)
**Stack:** Spring Boot + PostgreSQL + Redis
- Complete CRUD API with encoding
- Database indexing for GET /{code}
- Caching strategy with TTL
- Rate limiting on POST

**Success Criteria:**
- <50ms P99 latency for GET
- Handles 1000 simultaneous requests
- Graceful degradation if Redis down

### Project 3: E-Commerce Order Service (6–8 hours)
**Stack:** Spring Boot (microservices) + PostgreSQL + Kafka
- Order, Inventory, Payment services
- Saga orchestration for consistency
- Event publishing on order state change
- Integration tests with Testcontainers

**Success Criteria:**
- Handles concurrent orders without double-booking
- Compensation logic works (order rollback)
- Events delivered reliably

### Project 4: Cache Coherency System (Advanced, 8+ hours)
**Stack:** Spring Boot + Redis + PostgreSQL
- Implement cache-aside pattern
- Handle cache invalidation on writes
- Multi-tier caching (L1: local cache, L2: Redis, L3: DB)
- Load test to measure hit rates

---

## 7. Study Resources & Problem Bank

### Books & Courses
1. **"Designing Data-Intensive Applications"** (Kleppmann) – industry bible
2. **"System Design Interview"** (Alex Xu) – 2 volumes, practical
3. **"Microservices Patterns"** (Richardson) – Java-focused
4. **LinkedIn Learning / Educative.io** – practice problems

### Practice Platforms
- **LeetCode Premium:** System Design category (40+ problems)
- **Educative.io:** "Grokking System Design" course (15+ case studies)
- **Mock Interview Sessions:** Pramp.com, Interviewing.io

### Java-Specific Topics to Deepen
- Spring Cloud (Config, Registry, Load Balancer)
- Message Brokers (Kafka, RabbitMQ)
- Distributed Tracing (OpenTelemetry, Jaeger)
- Container Orchestration (Docker, Kubernetes basics)

---

## 8. Interview Cheat Sheet

### Opening Moves (First 5 Minutes)
1. Clarify scope: "How many users? Read/write ratio? Consistency requirement?"
2. Define scale: "1000 RPS? 1M RPS? Daily, monthly, yearly growth?"
3. Identify constraints: "Latency budget? Cost ceiling? Regulatory?"

### Component Checklist
- [ ] Load balancer (distribute traffic)
- [ ] API Gateway (rate limit, auth)
- [ ] Cache layer (Redis/Memcached)
- [ ] Database (with replication & indexing)
- [ ] Message queue (async work)
- [ ] Search engine (if needed: Elasticsearch)
- [ ] Monitoring & logging (observability)

### Red Flags (Avoid These)
- Single point of failure (no redundancy)
- Unbounded queues (memory leak)
- Synchronous cascades (P99 latency spike)
- No rate limiting (vulnerable to abuse)
- Ignoring data consistency (eventual consistency without safeguards)

### Fallback Phrases
- "If scale requires it, we'd shard by [dimension]."
- "This assumes read-heavy; if write-heavy, we'd move to write-optimized structure."
- "For now, we tolerate eventual consistency; if stricter requirements emerge, we add distributed locks."

---

## 9. Progression Checklist

### Junior Java Dev (0–1 YOE)
- [ ] Understand monolithic Spring Boot architecture
- [ ] Know when to use SQL vs. NoSQL (conceptual)
- [ ] Implement basic REST APIs with layered design
- [ ] Write unit tests, understand mocking
- [ ] Know CAP theorem and basic consistency models
- [ ] Design a simple URL shortener or rate limiter

### Associate Java Dev (1–3 YOE)
- [ ] Design multi-service systems with trade-offs
- [ ] Implement saga patterns for distributed transactions
- [ ] Optimize queries (indexes, N+1 fixes, pagination)
- [ ] Use caching strategically (Redis, in-memory)
- [ ] Understand message-driven architecture
- [ ] Design systems handling 10K–100K RPS
- [ ] Communicate trade-offs clearly in interviews

### Next Level (Senior)
- [ ] Multi-region, multi-cloud deployments
- [ ] Petabyte-scale data systems
- [ ] Advanced replication & consensus (Raft, Paxos)
- [ ] Cost optimization & resource planning

---

## Study Timeline (12 Weeks)

| Week | Focus | Deliverable |
|------|-------|---|
| 1–2 | Core concepts (CAP, consistency, caching) | Diagram: simple auth system |
| 3–4 | Java patterns (monolith vs. microservices) | Refactor monolith to 2 microservices |
| 5–6 | Case studies (URL shortener, rate limiter) | Code + design doc for one case |
| 7 | Non-functional requirements (HA, security) | Updated previous design with HA |
| 8 | Communication frameworks | Practice mock interview |
| 9–12 | Projects + practice problems | Complete 2 projects + 5 mock interviews |

---

## Final Tips

1. **Start Simple** – Nail the fundamentals before scaling to 1B users.
2. **Trade-off Mindset** – No perfect solution; every choice has cost.
3. **Code It** – Theoretical knowledge alone fails interviews. Build prototypes.
4. **Explain Clearly** – "Here's my architecture (draw), here's the trade-off (sync vs. async), here's why (latency vs. complexity)."
5. **Stay Current** – System design evolves (serverless, edge computing, etc.); stay curious.

Good luck with your interviews!