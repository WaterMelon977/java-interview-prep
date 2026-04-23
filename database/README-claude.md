# JPA/Database Syllabus for Java Backend Interviews
## Junior Developer (0–2 YoE) → Associate Developer (2–4 YoE)

---

## 1. RELATIONAL DATABASE FUNDAMENTALS

### 1.1 Core Concepts
- **ACID Properties**: Atomicity, Consistency, Isolation, Durability
  - When each matters (write-heavy vs. read-heavy scenarios)
  - Trade-offs in distributed systems
- **Normalization**: 1NF → 5NF (practical focus on 3NF)
  - Denormalization trade-offs (why you denormalize: query performance, reporting)
  - Real-world example: user profiles vs. flattened materialized views
- **Primary & Foreign Keys**: Constraints, cascading behavior
- **Indexes**: B-tree, hash indexes; when to use; impact on INSERT/UPDATE/DELETE
- **Query Execution Plans**: Understanding EXPLAIN, identifying bottlenecks
- **Transactions**: ACID isolation levels (READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE)
  - Phantom reads, dirty reads, non-repeatable reads

### 1.2 SQL Fundamentals (required for all backend roles)
- **SELECT/INSERT/UPDATE/DELETE**: Basic CRUD with complex WHERE/JOIN conditions
- **JOINs**: INNER, LEFT, RIGHT, FULL OUTER; self-joins; multiple joins
- **Aggregates**: GROUP BY, HAVING, window functions (RANK, ROW_NUMBER, SUM OVER)
- **Subqueries**: Scalar subqueries, correlated subqueries, IN/EXISTS
- **CTEs (Common Table Expressions)**: WITH clauses, recursive CTEs
- **Set Operations**: UNION, UNION ALL, EXCEPT, INTERSECT
- **Performance**: Index hints, query optimization, avoiding N+1 queries

**Interview Question Examples**:
- "Write a query to find the top 3 users by purchase amount in each category without duplicates"
- "Explain the difference between UNION and UNION ALL; when does it matter?"
- "What happens if you use a correlated subquery in a large table? How would you optimize?"

---

## 2. SPRING DATA JPA ESSENTIALS

### 2.1 Entity Mapping & Annotations
- **@Entity, @Table**: Basic entity setup, naming strategies
- **@Id, @GeneratedValue**: Primary key generation strategies
  - IDENTITY vs. SEQUENCE vs. TABLE vs. AUTO
  - When to use each (database-specific considerations)
- **@Column**: Nullable, unique, length constraints, updatable/insertable flags
- **@Temporal, @CreationTimestamp, @UpdateTimestamp**: Audit fields
- **@Enumerated**: EnumType.STRING vs. ORDINAL (best practices)
- **@JsonIgnore, @JsonProperty**: DTO/Entity serialization concerns
- **@Transient**: Fields not persisted to DB

### 2.2 Relationships & Cardinality
- **@OneToOne**: Uni/bidirectional, cascade, fetch strategies
  - Common pitfall: infinite serialization loops (use DTOs or @JsonIgnore)
- **@OneToMany / @ManyToOne**: Owning side vs. mapped side
  - Why @ManyToOne is the owning side; foreign key placement
  - Cascade implications (DELETE, PERSIST, MERGE, REFRESH)
  - orphanRemoval: when and why
- **@ManyToMany**: Join table behavior, @JoinTable annotation
  - Managing the many-to-many relationship (add/remove items)
  - Cascade propagation; bidirectional pitfalls
  - Extra data in join table (consider separate entity)
- **Fetch Strategies**: LAZY vs. EAGER
  - N+1 query problem (define it, identify it, solve it)
  - How JPA loads related entities (session/persistence context)
  - @Fetch(FetchMode.JOIN), @QueryHints (@NamedQuery, FETCH GRAPH)

**Real-World Scenario**:
- Entity: `User` has many `Orders` (OneToMany). Entity: `Order` has many `OrderItems` (OneToMany). How do you avoid N+1 queries when fetching all users with their orders and order items?

### 2.3 Inheritance & Polymorphism
- **@Inheritance**: SINGLE_TABLE vs. JOINED vs. TABLE_PER_CLASS
  - Trade-offs: query complexity, storage overhead, migration impact
- **@DiscriminatorColumn, @DiscriminatorValue**: Type markers
- **@MappedSuperclass**: Shared audit fields (createdAt, updatedBy)
- **When to use each**: Vehicle (car, truck) vs. Payment (card, bank transfer)

### 2.4 Persistence Context & Lifecycle
- **Entity States**: New, Managed, Detached, Removed
- **Session/Persistence Context**: First-level cache, identity map
- **merge() vs. persist()**: When to use; differences with detached objects
- **flush() vs. commit()**: When JPA writes to DB
- **Lazy Loading & Session Closed**: LazyInitializationException causes and fixes
- **equals() & hashCode()**: Why they matter for collections of entities
  - Using `@NaturalId` for business key equality

---

## 3. SPRING DATA JPA REPOSITORIES & QUERIES

### 3.1 Repository Pattern
- **CrudRepository, PagingAndSortingRepository, JpaRepository**: Hierarchy and methods
- **Custom Queries**: @Query with JPQL, native SQL, stored procedures
- **Query Methods**: Naming conventions (findBy, countBy, existsBy, deleteBy)
  - Dynamic query generation from method names
  - Projection: `interface UserProjection { String getName(); int getAge(); }`

### 3.2 JPQL (Java Persistence Query Language)
- **Syntax vs. SQL**: Object-oriented query language
- **SELECT, FROM, WHERE, JOIN**: JPQL equivalents
- **Aggregate Functions**: COUNT, AVG, SUM, MIN, MAX
- **Parameter Binding**: Named parameters vs. positional
- **Pagination**: setFirstResult(), setMaxResults(), Pageable interface

### 3.3 Native SQL Queries
- **@Query(nativeQuery = true)**: When to use (complex queries, stored procedures)
- **ResultSet Mapping**: @SqlResultSetMapping, @ConstructorResult
- **Parameterized Queries**: Avoiding SQL injection with parameter binding
- **Performance**: Native queries don't benefit from JPA caching; use judiciously

### 3.4 Spring Data Query Features
- **@Param**: Named parameter binding
- **@Modifying**: UPDATE/DELETE queries; need @Transactional
- **Specifications**: Dynamic WHERE clauses without string concatenation
  ```
  Spring Data example: repository.findAll(Specification.where(...).and(...))
  ```
- **Projections**: Slice, Page, List, Stream for result handling
- **Sorting & Pagination**: Sort, Pageable, PaginationAndSortingRepository

**Interview Scenario**:
- "Write a repository method to fetch users aged 25–35 from city X, sorted by name, paginated. How would you do it with query methods vs. @Query?"

---

## 4. DATABASE PERFORMANCE & OPTIMIZATION

### 4.1 N+1 Query Problem
- **Definition**: 1 query + N additional queries for related data
- **Detection**: Enable SQL logging, check query count
- **Solutions**:
  - EAGER loading (careful; can cause cartesian products with multiple collections)
  - FETCH JOIN in JPQL: `SELECT u FROM User u LEFT JOIN FETCH u.orders`
  - EntityGraph / NamedEntityGraph for dynamic loading strategies
  - Projection: fetch only needed columns
  - Caching strategies (second-level cache)

### 4.2 Indexing Strategy
- **Single-column indexes**: On foreign keys, frequently filtered columns
- **Composite indexes**: Multi-column WHERE clauses; query plan impact
- **Covering indexes**: Include SELECT columns to avoid table lookups
- **Index overhead**: Write performance, storage; when NOT to index
- **EXPLAIN/EXPLAIN ANALYZE**: Reading execution plans

### 4.3 Query Optimization
- **Pagination**: Avoid OFFSET on large tables; use keyset pagination
- **Batch Processing**: Bulk inserts/updates vs. entity-by-entity
  - `batch-size` in JPA configuration
  - JDBC batch mode for high-throughput scenarios
- **Denormalization**: Calculated columns, materialized views vs. JPA queries
- **Caching Layers**: Query result caching (second-level cache), cache invalidation strategies

### 4.4 Connection Pooling & Resource Management
- **HikariCP**: Pool size, leak detection, connection timeout tuning
- **Idle Connection Handling**: Validation queries, maxLifetime
- **Statement Caching**: Prepared statement reuse

---

## 5. JPA ADVANCED TOPICS

### 5.1 Caching
- **First-Level Cache (Session Cache)**: Automatic, per-transaction
- **Second-Level Cache**: Across transactions, requires configuration
  - Ehcache, Hazelcast, Redis integration
  - Cache invalidation strategies (TTL, manual eviction)
  - Query result caching (@Cacheable)

### 5.2 Optimistic & Pessimistic Locking
- **Optimistic Locking**: @Version field, StaleObjectStateException
  - When concurrent updates are rare
- **Pessimistic Locking**: SELECT FOR UPDATE, lock() method
  - When conflicts are frequent; trade-off with throughput
- **Deadlock Avoidance**: Lock ordering, timeout handling

### 5.3 Auditing & Temporal Data
- **@EntityListeners, @PrePersist, @PreUpdate**: Lifecycle callbacks
- **Spring Data Auditing**: @CreatedDate, @LastModifiedDate, @CreatedBy, @LastModifiedBy
- **Soft Deletes**: @Where(clause = "deleted_at IS NULL")
- **Temporal Queries**: @Temporal, point-in-time versioning

### 5.4 Stored Procedures & Native Features
- **@Procedure**: Calling stored procedures from Java
- **Native Query Mapping**: Complex result types
- **When to use**: Legacy systems, high-performance analytics, database-specific logic

### 5.5 Lazy Loading Strategies
- **Proxy Objects**: JPA creates proxies for lazy relationships
- **LazyInitializationException**: Accessing lazy fields outside transaction
- **Solutions**: Open Session In View (anti-pattern), eager loading, DTOs, FETCH JOIN

---

## 6. TRANSACTION MANAGEMENT

### 6.1 @Transactional Behavior
- **propagation**: REQUIRED (default), REQUIRES_NEW, NESTED, SUPPORTS
  - When each applies (nested transactions, exception handling)
- **isolation**: Default (database-dependent), READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE
  - Trade-off: isolation vs. throughput
- **rollbackFor / noRollbackFor**: Exception-driven rollback
  - Checked vs. unchecked exceptions; why Spring rolls back only on RuntimeException by default
- **readOnly**: Optimization flag; tells JPA transaction is read-only

### 6.2 Transaction Scope
- **Method-level**: Where to place @Transactional (service layer, not controller)
- **Multiple database operations**: Atomicity guarantee
- **Timeout**: transactionTimeout to prevent hanging transactions
- **Implicit Commits**: Auto-commit mode; transaction boundaries

### 6.3 Exception Handling
- **DataIntegrityViolationException**: Constraint violations, unique key conflicts
- **OptimisticLockingFailureException**: Concurrent update conflict
- **TransactionSystemException**: Transaction infrastructure issues
- **Custom exception translation**: Spring's SqlExceptionTranslator

---

## 7. COMMON PITFALLS & SOLUTIONS

### 7.1 Serialization & DTO Anti-Patterns
- **Problem**: Lazy-loaded proxies in HTTP responses; circular references
- **Solution**: Use DTOs for API responses; @JsonIgnore for sensitive fields
- **Tool**: MapStruct, Lombok for DTO mapping

### 7.2 Cartesian Product Problem
- **Scenario**: JOIN multiple @OneToMany relationships; result set explodes
- **Detection**: Query count vs. entity count mismatch
- **Fix**: Multiple queries, Batch fetching, or restructure relationships

### 7.3 Detached Entity Issues
- **Problem**: Updating a detached entity; merge() complexity
- **Best Practice**: Fetch, modify, save within same transaction

### 7.4 Mass Insert/Update Performance
- **Naive Approach**: Loop and persist(); slow
- **Better**: Batch size in config, bulk operations, native SQL for ETL

### 7.5 Connection Leaks
- **Indicator**: "Timeout waiting for idle object" errors
- **Cause**: Not closing Connections, ResourceSet leaks in custom code
- **Prevention**: Use JpaRepository (managed by Spring), monitor pool metrics

---

## 8. DATABASE DESIGN PATTERNS

### 8.1 Soft Delete Pattern
```java
@Entity
public class User {
  @Id
  private Long id;
  private String email;
  
  @Temporal(TemporalType.TIMESTAMP)
  private LocalDateTime deletedAt; // null = active
  
  @Where(clause = "deleted_at IS NULL")
  // Filters automatically in queries
}
```

### 8.2 Audit Trail Pattern
```java
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class AuditedEntity {
  @CreatedDate
  private LocalDateTime createdAt;
  
  @LastModifiedDate
  private LocalDateTime updatedAt;
  
  @CreatedBy
  private String createdBy;
}
```

### 8.3 UUID Primary Keys
- **Pros**: Globally unique, good for distributed systems, no central sequence
- **Cons**: Larger storage, slower index performance than BIGINT, harder to debug
- **Best Practice**: Use BIGINT (SEQUENCE) for most OLTP systems; UUID for distributed microservices

### 8.4 Denormalization & Calculated Fields
- **Purpose**: Read performance; trade-off with write complexity
- **Example**: Store total_price on Order (redundant with SUM of OrderItems) for fast reporting
- **Maintenance**: Use triggers or application logic to keep in sync

### 8.5 Event Sourcing & CQRS (Advanced)
- **Event Sourcing**: Immutable event log; state computed from events
- **CQRS**: Separate read/write models; eventual consistency
- **When to use**: High audit requirements, complex business logic, event replay needs

---

## 9. TESTING JPA APPLICATIONS

### 9.1 Unit Testing Entities
- **Focus**: Entity logic, validation annotations, computed properties
- **Tool**: JUnit 5, no database needed

### 9.2 Integration Testing with DB
- **@DataJpaTest**: Lightweight test slice; in-memory H2 by default
- **TestContainers**: Real database (PostgreSQL, MySQL) in Docker
- **@Transactional**: Rollback after test (isolation)
- **Setup/Teardown**: @BeforeEach, SQL scripts, DBUnit

### 9.3 Repository Testing
```java
@DataJpaTest
class UserRepositoryTest {
  @Autowired
  private UserRepository repository;
  
  @Test
  void testFindByEmailReturnsUser() {
    User saved = repository.save(new User("test@example.com"));
    User found = repository.findByEmail("test@example.com");
    assertThat(found).isNotNull().extracting("email").isEqualTo("test@example.com");
  }
}
```

### 9.4 Query Testing
- **Test different filter combinations**: Ensure indexes used, no cartesian products
- **Test pagination**: Verify correct sorting, offset handling
- **Test joins**: Verify no N+1 queries (use query counting libraries)

---

## 10. DATABASE-SPECIFIC CONSIDERATIONS

### 10.1 PostgreSQL (Recommended for Java/Spring)
- **UUID type**: Native support, excellent for Spring Data
- **JSONB**: Store semi-structured data alongside relational
- **Array types**: @JdbcTypeCode(SqlTypes.JSON) for List columns
- **Window Functions**: PARTITION BY, ROW_NUMBER for advanced queries
- **Sequences**: Excellent support; use for BIGINT PKs

### 10.2 MySQL/MariaDB
- **Storage Engines**: InnoDB (default; ACID), MyISAM (non-transactional)
- **Fulltext Search**: Native FTS; JPA doesn't abstract this well
- **Generated Columns**: Virtual columns for computed values
- **Limitations**: No true window functions in older versions

### 10.3 SQL Server
- **Identity/Sequences**: Both supported; SQL Server specific
- **GETDATE()**: Server-side timestamp generation
- **Clustered Index**: Non-cluster vs. clustered; query optimizer behavior
- **Temporal Tables**: Built-in versioning (@Temporal not equivalent; use views/manual)

### 10.4 H2 (Development/Testing)
- **Mode**: H2 dialect differences from prod DB; use correct compatibility mode
- **In-memory**: Fast tests; doesn't catch DB-specific issues
- **Script mode**: CREATE TABLE scripts may differ from actual DB

---

## 11. INTERVIEW PREPARATION CHECKLIST

### Conceptual Questions (Expect These)
- Explain ACID properties and isolation levels; when does each matter?
- What is N+1 query problem? How do you identify and fix it?
- LAZY vs. EAGER loading; pros/cons?
- What's the difference between merge() and persist()?
- Explain the entity lifecycle (new, managed, detached, removed).
- When would you use native SQL over JPQL?
- How does JPA handle relationships? OneToMany vs. ManyToOne ownership.
- What's the difference between optimistic and pessimistic locking?
- Describe the first-level cache and second-level cache.
- Why would you denormalize a database? Example?

### Practical Coding Questions (Have Solutions Ready)
1. **N+1 Query Fix**: Write a repository method using FETCH JOIN to avoid N+1 when fetching users with orders.
2. **Complex Query**: "Fetch top 5 users by total order amount in the last 30 days, with pagination."
3. **Relationship Mapping**: Entity design for Product → Category (ManyToOne), Product → Reviews (OneToMany). Handle cascade, orphan removal.
4. **Performance Optimization**: Given slow query, identify bottleneck (missing index, N+1, wrong fetch strategy).
5. **Transaction Behavior**: "What happens if an exception occurs mid-transaction? How do you control rollback?"
6. **Batch Processing**: "Optimize bulk insert of 10,000 records into database."
7. **Soft Delete Implementation**: Design User entity with soft delete and ensure all queries filter deleted users.

### Hands-On Practice
- Build a small project: User → Orders → OrderItems (3-level nesting)
  - Write repositories with complex queries
  - Implement pagination, sorting
  - Use EntityGraph to solve N+1
  - Write integration tests with @DataJpaTest
- Debug a slow application: Enable SQL logging, analyze queries, add indexes
- Compare query plans: EXPLAIN on PostgreSQL/MySQL for same query, different indexes

---

## 12. RECOMMENDED LEARNING PATH

### Week 1–2: Fundamentals
- SQL: JOINS, aggregates, subqueries, window functions
- ACID properties, transactions, isolation levels
- Normalization, primary/foreign keys, indexing concepts

### Week 3–4: JPA Basics
- Entity mapping (@Entity, @Column, @Id, @GeneratedValue)
- Relationships (@OneToMany, @ManyToOne, @ManyToMany)
- CrudRepository, basic queries, @Query

### Week 5–6: Advanced JPA
- Entity lifecycle, persistence context, merge/persist
- Lazy loading, fetch strategies, N+1 problem
- Inheritance strategies (@Inheritance, @MappedSuperclass)

### Week 7–8: Performance & Transactions
- Query optimization, indexing, EXPLAIN
- Transaction management (@Transactional, propagation, isolation)
- Caching strategies, optimistic/pessimistic locking

### Week 9–10: Real-World Scenarios
- Build multi-entity project with complex queries
- Integration testing with TestContainers
- Profile a slow application; optimize queries
- Soft delete, audit trail, temporal data patterns

### Week 11–12: Mock Interviews & Edge Cases
- Practice coding questions on paper
- Whiteboard entity designs for new domains
- Discuss trade-offs: performance vs. maintainability
- Review real interview questions from company-specific sources

---

## 13. RESOURCES

### Books
- **"High-Performance Java Persistence"** by Vlad Mihalcea (comprehensive, practical)
- **"Java Persistence with Hibernate"** by Christian Bauer & Gavin King (deep dive)
- **Spring Data JPA Official Documentation** (up-to-date, examples)

### Online
- **Vlad Mihalcea's Blog** (hibernatetips.com): Real-world JPA/Hibernate patterns
- **Baeldung Tutorials** (baeldung.com): Spring Data JPA, HibernateExamples
- **LeetCode/HackerRank Database Problems**: SQL optimization practice
- **Java Design Patterns**: Entity design, repository pattern, DAO

### Practice
- **GitHub**: Clone open-source Spring Boot projects; study their JPA usage
- **Local Projects**: Build CRUD app (User → Blog → Comment), optimize queries
- **TestContainers**: Practice integration testing with real databases

---

## 14. QUICK REFERENCE: COMMON INTERVIEW PATTERNS

| Pattern | When to Use | Example |
|---------|------------|---------|
| EAGER Fetch | Small, always-needed relationships | User → Address (1:1) |
| LAZY Fetch | Large collections, optional navigation | User → Orders (1:N, hundreds per user) |
| FETCH JOIN | Solve N+1 in single query | `SELECT u FROM User u LEFT JOIN FETCH u.orders` |
| EntityGraph | Multiple lazy paths, dynamic loading | @NamedEntityGraph(attributeNodes = @NamedAttributeNode("orders")) |
| Specification | Dynamic WHERE without string concat | `repository.findAll(userSpec)` |
| Projection | Partial data, DTO mapping | `interface UserDTO { String getName(); }` |
| Native SQL | Complex DB-specific logic | Stored procedures, window functions |
| Pessimistic Lock | High-contention updates | SELECT … FOR UPDATE |
| Optimistic Lock | Rare conflicts | @Version field + retry logic |
| Soft Delete | Data retention, audit | @Where(clause = "deleted_at IS NULL") |

---

## 15. FINAL TIPS FOR INTERVIEWS

1. **Always ask questions**: Clarify requirements before coding. "Pagination size? Performance targets?"
2. **Think aloud**: Explain your indexing choices, fetch strategy decisions.
3. **Discuss trade-offs**: "We could eager-load, but it slows writes. Better to FETCH JOIN in read queries."
4. **Test your code**: Write a simple test case to validate your query/mapping.
5. **Consider edge cases**: NULL handling, empty results, concurrent updates.
6. **Use the RDBMS effectively**: Don't move all logic to Java; let DB handle aggregation, filtering.
7. **Be honest about gaps**: "I haven't used second-level caching, but I understand the concept…"
8. **Prepare examples from your work**: Reference Auricle project patterns if relevant.

---

## Interview Timeline for Your Background

**Your Edge**: 9 months of production experience at Auricle (Spring Boot, microservices, React). Leverage this:
- Mention N+1 issues you've debugged in production
- Discuss how you optimized queries for reports/dashboards
- Reference JWT/OAuth2 security patterns (less JPA-focused, but relevant for full-stack)
- Talk about Docker/orchestration in context of DB connection pooling

**Focus Areas for You** (given Java/Spring focus):
- Master N+1 problem; have a concrete Auricle example
- Practice complex multi-join queries from your project domain
- Know transaction propagation; spring from real concurrency issue
- Be confident on pagination/sorting for large datasets
- Understand how Redis (if you used it) interacts with JPA caching

**Study Timeline**: 4–5 weeks of focused prep should solidify this. Practice with LeetCode database problems 2–3x weekly.