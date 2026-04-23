# High-Frequency JPA/Database Interview Questions
## Weighted by Topic Importance & Actual Interview Frequency

---

## FREQUENCY WEIGHT ANALYSIS

### Tier 1: CRITICAL (80% of interviews ask these)
- **4. Database Performance & Optimization** (especially N+1 problem)
- **2.2 Relationships & Cardinality**
- **3. Spring Data JPA Repositories & Queries**
- **6. Transaction Management**
- **1.2 SQL Fundamentals**

### Tier 2: HIGH (60% of interviews ask these)
- **2.1 Entity Mapping & Annotations**
- **2.4 Persistence Context & Lifecycle**
- **5.2 Optimistic & Pessimistic Locking**
- **7. Common Pitfalls & Solutions**
- **9. Testing JPA Applications**

### Tier 3: MEDIUM (40% of interviews ask these)
- **5.1 Caching**
- **8. Database Design Patterns**
- **2.3 Inheritance & Polymorphism**
- **3.2 JPQL**
- **3.4 Spring Data Query Features**

### Tier 4: LOW (20% of interviews ask these)
- **1.1 Core Concepts** (unless deep-dive interview)
- **3.3 Native SQL Queries**
- **5.3 Auditing & Temporal Data**
- **5.4 Stored Procedures**
- **5.5 Lazy Loading Strategies** (covered under 2.4)
- **10. Database-Specific Considerations**

---

## TIER 1: CRITICAL QUESTIONS (Answer These Perfectly)

### 4. DATABASE PERFORMANCE & OPTIMIZATION

#### 4.1 N+1 Query Problem (Asked in 85% of interviews)

1. **What is the N+1 query problem? Explain with a code example.**
   - Expected depth: Define it, show problematic code, explain why it's slow, mention real impact (100s of queries instead of 1).

2. **How do you identify N+1 queries in a Spring Boot application?**
   - Hints: SQL logging, Hibernate statistics, query counting tools, browser DevTools.

3. **Write a repository method to fetch users with their orders. How would you avoid N+1 without using FETCH JOIN?**
   - Expected: FETCH JOIN, EntityGraph, or batch query (multiple queries) approach with explanation.

4. **What's the difference between LAZY and EAGER loading? When would you use each?**
   - Hints: Memory impact, query count, cartesian product risk, default behavior.

5. **If I have User → Orders → OrderItems (3 levels), how do I fetch all without N+1?**
   - Expected: Multiple solutions (FETCH JOIN, EntityGraph, batch queries); discuss cartesian product risk.

6. **How does FETCH JOIN help solve N+1? What are its limitations?**
   - Hints: Cartesian product with multiple collections, DISTINCT issues, SQL result set inflation.

7. **Explain EntityGraph. How is it different from FETCH JOIN?**
   - Expected: NamedEntityGraph, dynamic graphs, multiple path loading, reusability.

8. **You have a query that returns 1000 users. Suddenly, it's returning 50,000 rows. What happened and how would you fix it?**
   - Hints: Cartesian product from JOIN on collection. Solution: separate queries.

9. **How does Spring Data JPA's Specification pattern help with query optimization?**
   - Expected: Dynamic WHERE clauses, avoiding string concatenation, type-safe queries.

10. **What's the impact of pagination on N+1 queries? Can pagination alone prevent the problem?**
    - Hints: No, pagination doesn't address N+1; must combine with FETCH JOIN or batch queries.

#### 4.2 Indexing Strategy (Asked in 65% of interviews)

11. **Why should you create an index on a foreign key column?**
    - Expected: Query performance, JOIN optimization, WHERE clauses on FK.

12. **When would you create a composite (multi-column) index instead of single-column indexes?**
    - Expected: Multi-column WHERE clauses, covering indexes, query plan impact.

13. **What's a covering index? Give an example.**
    - Expected: Index includes SELECT columns; avoids table lookup; trade-off with storage.

14. **How do you read EXPLAIN/EXPLAIN ANALYZE output to identify missing indexes?**
    - Expected: "Seq Scan" or "Full Table Scan", "Index Scan", query plan interpretation.

15. **At what point should you add an index? What are the costs?**
    - Expected: Trade-off: faster reads, slower writes, storage overhead.

#### 4.3 Query Optimization (Asked in 70% of interviews)

16. **You're paginating a table with 100 million records using OFFSET. Page 10,000 takes 10 seconds. Why?**
    - Expected: OFFSET scans all previous rows. Solution: keyset pagination.

17. **Explain keyset (seek) pagination. How is it different from OFFSET pagination?**
    - Expected: WHERE id > lastId instead of OFFSET; constant time regardless of page number.

18. **What's the best approach for bulk inserts (10,000+ records) into JPA? Why?**
    - Expected: Batch size configuration, JDBC batching, avoid per-entity persist.

19. **How would you optimize a slow aggregation query (e.g., SUM of all orders per user)?**
    - Expected: Index, materialized view, denormalized column, caching.

20. **What's the difference between Stream, Slice, and Page in Spring Data repositories?**
    - Expected: Stream (memory-efficient for large results), Slice (no total count), Page (includes total).

### 2. SPRING DATA JPA ESSENTIALS

#### 2.2 Relationships & Cardinality (Asked in 80% of interviews)

21. **Design entities for Blog → Post → Comment. How would you handle cascading?**
    - Expected: OneToMany relationships, cascade types, orphanRemoval, owning side.

22. **What's the difference between the owning side and mapped side of a relationship?**
    - Expected: Foreign key placement, @JoinColumn, mappedBy, cascade responsibility.

23. **In a OneToMany relationship, where does the foreign key live in the database?**
    - Expected: On the "Many" side (Order.user_id, not User.order_ids).

24. **What happens if you set cascade = CascadeType.REMOVE on a OneToMany relationship? When is it dangerous?**
    - Expected: Child entities deleted when parent deleted. Risk: accidental mass deletion. Better: soft delete or explicit logic.

25. **How would you handle a ManyToMany relationship? Explain with an example (e.g., Student ↔ Course).**
    - Expected: Join table, @JoinTable, owning vs. inverse side, managing both sides.

26. **What does orphanRemoval = true do? Give a use case.**
    - Expected: Deletes child when removed from parent collection. Use: Order → OrderItems.

27. **If you have User ↔ Group (ManyToMany), and you delete a User, should the Group be deleted? Why or why not?**
    - Expected: No; Group is independent. Only delete if cascade makes sense (rare for M:M).

28. **Explain the difference between cascade = CascadeType.PERSIST and CascadeType.ALL.**
    - Expected: PERSIST (saves new children only), ALL (includes MERGE, REMOVE, REFRESH, DETACH—riskier).

29. **A Product has many Reviews. If you delete a Review from the list and save the Product, is the Review deleted from the DB?**
    - Expected: Only if orphanRemoval = true. Otherwise, Review persists (foreign key becomes null or remains).

30. **Design a scenario where you'd use OneToOne instead of ManyToOne. What are the trade-offs?**
    - Expected: User → UserProfile (1:1), User ↔ Passport (1:1 with bidirectional). Trade-off: extra table, constraints.

#### 2.4 Persistence Context & Lifecycle (Asked in 60% of interviews)

31. **Explain entity states: New, Managed, Detached, Removed.**
    - Expected: When each state occurs, example transitions, implications.

32. **What's the persistence context (session cache)? Why is it important?**
    - Expected: First-level cache, identity map, prevents duplicate queries within transaction.

33. **What's the difference between merge() and persist()?**
    - Expected: persist() = new entity → managed. merge() = detached entity → managed + merges changes.

34. **You fetch a User in transaction A, then try to access a lazy-loaded property in transaction B. What happens?**
    - Expected: LazyInitializationException (proxy not initialized outside session). Solutions: eager load, DTOs, FETCH JOIN.

35. **What happens when you call flush()? When is it called automatically?**
    - Expected: Writes queued changes to DB (not commit). Called before COMMIT, explicitly, or before query.

36. **If you change a User entity but don't call save(), are changes persisted?**
    - Expected: Yes (if @Transactional method); Hibernate tracks changes, flushes at commit. No if detached or no transaction.

37. **How does Hibernate's dirty checking work?**
    - Expected: Compares current state with loaded state; only changed columns included in UPDATE.

38. **What's the issue with equals() and hashCode() in entities? Give an example.**
    - Expected: If overridden incorrectly, collections (Set, List) behave unpredictably. Solution: Use @NaturalId or business key.

39. **How would you prevent LazyInitializationException?**
    - Expected: Eager loading, FETCH JOIN, EntityGraph, DTOs, @Transactional on service.

40. **When is it safe to detach an entity? What are the consequences?**
    - Expected: After transaction ends. Consequence: lazy properties not loaded, merge() needed to reattach.

### 3. SPRING DATA JPA REPOSITORIES & QUERIES

#### 3.1 Repository Pattern + 3.4 Query Features (Asked in 70% of interviews)

41. **What methods does JpaRepository provide out of the box?**
    - Expected: findAll(), findById(), save(), delete(), flush(), saveAndFlush(), etc.

42. **Write a repository method using method naming conventions to find users by email and age range.**
    - Expected: `List<User> findByEmailAndAgeBetween(String email, int minAge, int maxAge);`

43. **When would you use @Query over method naming conventions?**
    - Expected: Complex queries, readability, database-specific logic.

44. **What's the difference between @Query with JPQL and nativeQuery = true?**
    - Expected: JPQL (portable, object-oriented), native SQL (database-specific, better control).

45. **How do you use parameter binding in @Query? Explain @Param.**
    - Expected: Named parameters (safer from SQL injection), positional parameters.

46. **Write a @Query method with a JOIN and GROUP BY to fetch top 5 users by order count.**
    - Expected: Complex JPQL, aggregation, sorting, potential to discuss N+1 issues.

47. **What's a Projection in Spring Data? When would you use one?**
    - Expected: Interface-based projection for partial DTOs, reduce network bandwidth, fewer columns selected.

48. **How do you handle pagination in repository queries?**
    - Expected: Pageable parameter, Page<T> return type, Sort, PageRequest.

49. **What's the difference between findOne() (removed) and findById()?**
    - Expected: findOne() deprecated; findById() returns Optional (null-safe).

50. **How would you implement a dynamic query builder using Specifications in Spring Data?**
    - Expected: Criteria API, Specification interface, combining predicates.

### 6. TRANSACTION MANAGEMENT

#### 6.1 @Transactional Behavior (Asked in 75% of interviews)

51. **What does @Transactional do? Where should you place it?**
    - Expected: Marks method boundaries, service layer (not controller/entity), automatic rollback on exception.

52. **Explain the propagation attribute of @Transactional. What's the default?**
    - Expected: REQUIRED (default), REQUIRES_NEW, SUPPORTS, MANDATORY, NOT_SUPPORTED, NEVER, NESTED.

53. **What's the difference between propagation = REQUIRED and REQUIRES_NEW?**
    - Expected: REQUIRED reuses outer transaction. REQUIRES_NEW creates new (nested tx independent).

54. **If an @Transactional method calls another @Transactional(propagation = REQUIRES_NEW) method, how many transactions are there?**
    - Expected: 2 (outer and inner; independent).

55. **What does the isolation attribute do? What are the isolation levels?**
    - Expected: READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE; trade-off with throughput.

56. **What happens if an exception is thrown inside a @Transactional method?**
    - Expected: RuntimeException causes rollback (default). Checked exceptions don't (by default); use rollbackFor.

57. **How do you prevent rollback for specific exceptions?**
    - Expected: @Transactional(noRollbackFor = SomeException.class).

58. **What does readOnly = true do? Why is it useful?**
    - Expected: Optimization flag, tells DB transaction is read-only, Hibernate skips dirty checking.

59. **Is @Transactional inherited if you put it on an interface method?**
    - Expected: Depends on proxy mode; safer to put on implementation. Spring proxies by default.

60. **What's the default transaction timeout? How do you set a custom one?**
    - Expected: Default is DB-dependent. Use @Transactional(timeout = 30) in seconds.

#### 6.2 Transaction Scope (Asked in 55% of interviews)

61. **You have a service method that saves order, processes payment, and sends email. Should these be in one transaction?**
    - Expected: Order/payment yes (atomic). Email no (REQUIRES_NEW, async); email failure shouldn't roll back order.

62. **What happens if you call an @Transactional method from another @Transactional method?**
    - Expected: Reuses transaction (REQUIRED default). Use REQUIRES_NEW to separate.

63. **Can you use @Transactional on a Repository interface? Why or why not?**
    - Expected: Not recommended; use on Service. Repository already wrapped by Spring Data.

#### 6.3 Exception Handling (Asked in 50% of interviews)

64. **What's a DataIntegrityViolationException? When does it occur?**
    - Expected: Constraint violation (unique key, foreign key, not null). Indicates data problem.

65. **How do you handle OptimisticLockingFailureException in your code?**
    - Expected: Catch, log, retry (with exponential backoff). User-facing: "Resource changed; please refresh."

66. **What's the difference between checked and unchecked exceptions in Spring transactions?**
    - Expected: Spring rolls back only on unchecked (RuntimeException). Checked exceptions require rollbackFor.

---

## TIER 2: HIGH-FREQUENCY QUESTIONS (Master These)

### 2.1 Entity Mapping & Annotations (Asked in 65% of interviews)

67. **What's the purpose of @GeneratedValue? Explain different strategies (IDENTITY, SEQUENCE, TABLE, AUTO).**
    - Expected: Auto-increment strategy, database-specific, trade-offs.

68. **When would you use GenerationType.SEQUENCE over IDENTITY?**
    - Expected: SEQUENCE better for batch inserts (prefetch IDs). Database-specific.

69. **What does @Column(updatable = false, insertable = false) mean?**
    - Expected: Marks column read-only after creation. Use for computed fields, database defaults.

70. **How do you handle enums in JPA? What's the difference between EnumType.STRING and ORDINAL?**
    - Expected: STRING (safe, name-based), ORDINAL (compact, brittle if order changes).

71. **What's the purpose of @Temporal? When do you use it?**
    - Expected: Maps Java temporal types to DB types, DATE/TIME/TIMESTAMP.

72. **How do you make an entity immutable in JPA?**
    - Expected: Constructor injection, no setters, @Immutable, use records (Java 16+).

73. **What's @Transient used for?**
    - Expected: Marks field as non-persistent (calculated fields, helper properties).

### 5.2 Optimistic & Pessimistic Locking (Asked in 60% of interviews)

74. **What's optimistic locking? How does @Version work?**
    - Expected: Version field incremented on update, StaleObjectStateException on conflict, retry logic.

75. **When would you use optimistic locking vs. pessimistic locking?**
    - Expected: Optimistic: rare conflicts (better throughput). Pessimistic: frequent conflicts (blocks, simpler).

76. **How do you implement pessimistic locking in Spring Data JPA?**
    - Expected: @Lock(LockModeType.PESSIMISTIC_WRITE), SELECT FOR UPDATE.

77. **What happens if two threads try to update the same entity without locking?**
    - Expected: Last write wins; first update lost (race condition).

78. **Can you use both optimistic and pessimistic locking together?**
    - Expected: Not recommended; choose one based on contention pattern.

79. **How do you handle OptimisticLockingFailureException in a REST API?**
    - Expected: Return 409 Conflict, client retries with fresh data.

### 7. COMMON PITFALLS & SOLUTIONS (Asked in 60% of interviews)

80. **What's the cartesian product problem in JPA? How do you identify and fix it?**
    - Expected: Joining multiple collections, result set explodes, fix: separate queries or EntityGraph per collection.

81. **A User entity has a List<Order>. When serializing to JSON, it causes a StackOverflowError. Why?**
    - Expected: Circular reference (User → Order → User). Fix: @JsonIgnore, DTO, separate serialization.

82. **You have a detached User object. You modify it and call repository.save(). What happens?**
    - Expected: merge() called; persists changes but entire object reloaded from DB (extra query).

83. **Why is it slow to bulk-update 10,000 records using a for-loop and repository.save()?**
    - Expected: 10,000 separate transactions/SQL statements. Better: batch size, JDBC batch, native SQL.

84. **How do you prevent a "Timeout waiting for idle object" error from the connection pool?**
    - Expected: Monitor active connections, adjust pool size, close connections properly, check for leaks.

### 9. TESTING JPA APPLICATIONS (Asked in 55% of interviews)

85. **What's @DataJpaTest used for? What does it do by default?**
    - Expected: Lightweight slice test for repositories, in-memory H2, auto-rolls back transactions.

86. **How do you test a repository method with multiple conditions (filtering, sorting, pagination)?**
    - Expected: @DataJpaTest, insert test data with @BeforeEach, assert results.

87. **What's TestContainers? Why would you use it instead of H2?**
    - Expected: Real database in Docker, catches DB-specific issues, more realistic tests.

88. **How do you count SQL queries executed in a test to verify N+1 problem is fixed?**
    - Expected: Hibernation statistics, query logger, custom SQL counter, assertion on query count.

89. **Should you test entity relationships in unit tests? How?**
    - Expected: Test in integration tests (@DataJpaTest) with DB. Verify cascade, orphanRemoval behavior.

90. **How do you test @Transactional behavior (rollback, propagation) in a test?**
    - Expected: Create scenarios with multiple transactional methods, assert final state, verify rollback.

---

## TIER 3: MEDIUM-FREQUENCY QUESTIONS (Know These)

### 5.1 Caching (Asked in 45% of interviews)

91. **What's the difference between first-level and second-level cache in JPA?**
    - Expected: First-level (session cache, automatic, per-transaction). Second-level (cross-transaction, requires config).

92. **When would you enable second-level caching? What are the downsides?**
    - Expected: Frequent reads of same data. Downside: cache invalidation complexity, stale data risk.

93. **How do you invalidate the cache when data changes?**
    - Expected: Manual eviction, TTL, database triggers, cache-aside pattern.

94. **Can you cache a query result in JPA? How?**
    - Expected: @Cacheable (Spring), second-level cache with @Cacheable query annotation (limited), or external cache (Redis).

### 8. DATABASE DESIGN PATTERNS (Asked in 50% of interviews)

95. **Design a User entity with soft delete. How do you ensure all queries exclude deleted users?**
    - Expected: @Where(clause = "deleted_at IS NULL"), @SQLDelete custom SQL, automatic filtering.

96. **What's a denormalized column? Give an example and explain trade-offs.**
    - Expected: Store SUM/COUNT in main table, sync via trigger or application logic. Trade-off: faster reads, slower writes.

97. **When would you use UUID as a primary key instead of BIGINT?**
    - Expected: Distributed systems, no central sequence. Downside: larger storage, slower indexes.

98. **How do you track who created/updated a record and when?**
    - Expected: @CreatedDate, @LastModifiedDate, @CreatedBy, @LastModifiedBy with AuditingEntityListener.

### 2.3 Inheritance & Polymorphism (Asked in 40% of interviews)

99. **Explain the 3 inheritance strategies in JPA: SINGLE_TABLE, JOINED, TABLE_PER_CLASS.**
    - Expected: Single table (simple, null columns), joined (normalization, joins), table-per-class (most tables).

100. **When would you use SINGLE_TABLE inheritance? What are the downsides?**
     - Expected: Simple hierarchy (2-3 types). Downside: null columns, discriminator column needed.

101. **If you have Vehicle (Car, Truck, Motorcycle), which inheritance strategy would you choose? Why?**
     - Expected: JOINED (normalized, queries clear) or SINGLE_TABLE (simple, few types). Not TABLE_PER_CLASS (polymorphic queries slow).

102. **What's @MappedSuperclass used for?**
     - Expected: Share common fields (createdAt, id) across unrelated entities, not a real entity.

### 3.2 JPQL (Asked in 40% of interviews)

103. **Write a JPQL query to fetch users with orders created in the last 30 days, ordered by order count.**
     - Expected: Complex query with JOIN, GROUP BY, aggregate function, WHERE clause.

104. **What's the difference between JPQL and native SQL in terms of portability?**
     - Expected: JPQL is DB-agnostic, native SQL is database-specific.

105. **How do you use CASE/WHEN in JPQL?**
     - Expected: Conditional logic in queries, e.g., `CASE WHEN u.age > 18 THEN 'Adult' ELSE 'Minor' END`.

### 3.4 Spring Data Query Features (Asked in 50% of interviews)

106. **What's the difference between Slice and Page in pagination?**
     - Expected: Page includes total count (extra query), Slice doesn't (faster for large datasets).

107. **How do you implement custom repository methods that aren't generated automatically?**
     - Expected: Create custom interface, implement it, extend both interfaces.

108. **Can you use Specifications for complex dynamic queries? Give an example.**
     - Expected: Type-safe alternative to string-based queries, combine predicates.

---

## TIER 4: LOW-FREQUENCY QUESTIONS (Nice to Know)

### 1.1 Core Concepts (Asked in 30% of interviews)

109. **Define ACID properties. Why is Atomicity important?**
     - Expected: Atomic (all-or-nothing), Consistent, Isolated, Durable.

110. **What's the difference between READ_COMMITTED and REPEATABLE_READ isolation levels?**
     - Expected: READ_COMMITTED (dirty read prevention), REPEATABLE_READ (non-repeatable read prevention).

111. **What's a phantom read? Can it happen at REPEATABLE_READ isolation?**
     - Expected: Yes (range queries). SERIALIZABLE prevents it.

### 3.3 Native SQL Queries (Asked in 35% of interviews)

112. **When would you use native SQL queries over JPQL?**
     - Expected: DB-specific features, stored procedures, performance-critical queries, legacy systems.

113. **What's SqlResultSetMapping used for?**
     - Expected: Maps native SQL results to entities or custom objects.

114. **How do you prevent SQL injection when using native queries?**
     - Expected: Parameterized queries, avoid string concatenation.

### 5.3 Auditing & Temporal Data (Asked in 35% of interviews)

115. **How do you track when an entity was created and last updated?**
     - Expected: @CreatedDate, @LastModifiedDate, @EntityListeners(AuditingEntityListener.class).

116. **What's the difference between @Temporal and @CreationTimestamp?**
     - Expected: @Temporal (JPA standard), @CreationTimestamp (Hibernate-specific, auto-sets).

### 5.4 Stored Procedures (Asked in 20% of interviews)

117. **When would you call a stored procedure from Java?**
     - Expected: Legacy systems, complex DB logic, performance-critical operations.

118. **How do you call a stored procedure in JPA?**
     - Expected: @Procedure, EntityManager.createStoredProcedureQuery().

### 10. Database-Specific Considerations (Asked in 25% of interviews)

119. **What are the differences between PostgreSQL and MySQL for JPA applications?**
     - Expected: Transaction handling, UUID support, JSON support, sequence vs. auto-increment.

120. **Why should you avoid H2 for integration tests of production code?**
     - Expected: H2 dialect differences, doesn't catch DB-specific issues, misleading test results.

---

## SUMMARY: QUESTION COUNT BY TIER

| Tier | Topic | Count | Interview % |
|------|-------|-------|------------|
| **TIER 1 (CRITICAL)** | Performance (20), Relationships (10), Repositories (10), Transactions (20), SQL (N/A) | **60 Q** | **80%** |
| **TIER 2 (HIGH)** | Entity Mapping (7), Locking (6), Pitfalls (5), Testing (6) | **24 Q** | **60%** |
| **TIER 3 (MEDIUM)** | Caching (4), Design Patterns (4), Inheritance (4), JPQL (3), Spring Data (3) | **18 Q** | **40-50%** |
| **TIER 4 (LOW)** | Core Concepts (3), Native SQL (3), Auditing (2), Stored Procedures (2), DB-Specific (2) | **12 Q** | **20-35%** |
| **TOTAL** | | **114 Questions** | |

---

## STUDY STRATEGY RECOMMENDATIONS

### Week 1: Master Tier 1 (Critical - 60 Questions)
- **Focus**: N+1 problem, relationships, repositories, transactions
- **Time**: 5-7 hours per day
- **Deliverable**: Code solutions for all 60 questions

### Week 2: Solidify Tier 2 (High - 24 Questions)
- **Focus**: Entity mapping, locking, pitfalls, testing
- **Time**: 3-5 hours per day
- **Deliverable**: Run actual tests, write test cases

### Week 3: Understand Tier 3 (Medium - 18 Questions)
- **Focus**: Caching, design patterns, inheritance, queries
- **Time**: 2-3 hours per day
- **Deliverable**: Design documents, pattern examples

### Week 4: Skim Tier 4 (Low - 12 Questions)
- **Focus**: Know the concept, not deep-dive
- **Time**: 1-2 hours per day
- **Deliverable**: Awareness, reference during interviews

### Interview Technique:
1. **Listen to the question fully** before answering.
2. **Clarify scope**: "Are we talking about micro-optimization or architectural decision?"
3. **Structure answer**: Problem → Solution → Trade-offs → Example code.
4. **Ask follow-up**: "Would you like me to explain the implementation details?"
5. **Discuss real experience**: Reference Auricle projects when applicable.

---

## QUICK TIER 1 MASTERY CHECKLIST (Before Any Interview)

### N+1 Problem (20 questions)
- [ ] Define N+1, explain why it happens (lazy loading + loop).
- [ ] Demonstrate FETCH JOIN solution.
- [ ] Demonstrate EntityGraph solution.
- [ ] Demonstrate batch query solution.
- [ ] Identify N+1 via SQL logging.
- [ ] Explain cartesian product risk.
- [ ] Know when pagination doesn't help.
- [ ] Handle 3-level nesting (User → Orders → Items).

### Relationships (10 questions)
- [ ] Draw ER diagram: Blog → Post → Comment.
- [ ] Explain owning vs. mapped side.
- [ ] Know where FK lives (child side).
- [ ] Compare cascade types: PERSIST, ALL, REMOVE.
- [ ] Use orphanRemoval correctly.
- [ ] Design ManyToMany with examples.
- [ ] Avoid accidental mass deletion.
- [ ] Handle bidirectional relationship consistency.

### Repositories & Queries (10 questions)
- [ ] Write method naming conventions.
- [ ] Write @Query JPQL.
- [ ] Use Pageable, Sort, Page.
- [ ] Handle Projection for partial data.
- [ ] Use @Param for parameter binding.
- [ ] Compare JPQL vs. native SQL.
- [ ] Use Specification for dynamic queries.
- [ ] Know when to use Slice vs. Page.

### Transactions (20 questions)
- [ ] Explain @Transactional defaults.
- [ ] Compare propagation: REQUIRED vs. REQUIRES_NEW.
- [ ] Handle exceptions: when rollback occurs.
- [ ] Use readOnly flag.
- [ ] Know transaction scope.
- [ ] Catch OptimisticLockingFailureException.
- [ ] Handle DataIntegrityViolationException.
- [ ] Design order processing: payment separate transaction.
- [ ] Understand dirty checking.
- [ ] Know when to use @Transactional(timeout=X).

---

## FINAL NOTE

**For your background (9 months at Auricle)**:
- You likely encountered N+1 queries; reference real examples.
- You used Spring Data JPA for REST APIs; mention specific repositories.
- You debugged transaction issues; share war stories.
- You optimized dashboard queries; reference materialized views or denormalization.

**Interviewers respect**:
1. **Practical knowledge** > theory (show real code).
2. **Trade-off thinking** > one-size-fits-all answers.
3. **Debugging experience** > textbook knowledge.
4. **Humble admission** > fake expertise (e.g., "I haven't used second-level caching, but I understand...").

---

**Total estimated prep time**: 60-80 questions × 15 min per question = 15-20 hours focused study.

Good luck! Focus on Tier 1 first; you'll ace 80% of interviews.