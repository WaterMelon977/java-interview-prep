# High-ROI Microservices Topics: Code Examples & Approaches
## Master These 10 Topics and You'll Ace 80% of Interviews

---

## Topic 1: Saga Pattern (Choreography vs. Orchestration)
**Asked in 95% of interviews | Most differentiating topic**

### The Problem
Traditional database transactions (ACID) don't work across microservices. You can't use a distributed two-phase commit because services are autonomous.

### Scenario
Order creation triggers:
1. Create order in Order Service
2. Process payment in Payment Service
3. Reserve inventory in Inventory Service
4. Send notification in Notification Service

If payment fails, orders and inventory are left in an inconsistent state.

### Solution 1: Choreography (Event-Driven)

```
Order Service: Creates order, publishes "OrderCreated" event
Inventory Service: Listens to "OrderCreated", reserves items, publishes "InventoryReserved"
Payment Service: Listens to "OrderCreated", charges user, publishes "PaymentProcessed"
Notification Service: Listens to all events, sends notifications
```

**Code: Order Service (publishes event)**
```java
@Service
public class OrderService {
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private ApplicationEventPublisher eventPublisher;
    
    public OrderDTO createOrder(CreateOrderRequest request) {
        // Validate
        if (request.getItems().isEmpty()) {
            throw new ValidationException("Order must have items");
        }
        
        // Create order
        Order order = new Order();
        order.setStatus(OrderStatus.PENDING);
        order.setCustomerId(request.getCustomerId());
        order.setItems(request.getItems());
        order.setAmount(request.getAmount());
        
        // Save to database
        Order savedOrder = orderRepository.save(order);
        
        // Publish event (others will listen)
        eventPublisher.publishEvent(new OrderCreatedEvent(
            savedOrder.getId(),
            savedOrder.getCustomerId(),
            savedOrder.getItems(),
            savedOrder.getAmount()
        ));
        
        return mapToDTO(savedOrder);
    }
}
```

**Code: Inventory Service (listens to event)**
```java
@Service
public class InventoryService {
    @Autowired
    private InventoryRepository inventoryRepository;
    
    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;
    
    @KafkaListener(topics = "order-events", groupId = "inventory-group")
    public void handleOrderCreated(OrderCreatedEvent event) {
        try {
            // Check if items are available
            boolean available = inventoryRepository.checkAvailability(event.getItems());
            
            if (!available) {
                // Reserve failed - publish compensating event
                kafkaTemplate.send("inventory-events", new InventoryReservationFailed(
                    event.getOrderId(),
                    "Insufficient inventory"
                ));
                return;
            }
            
            // Reserve inventory
            inventoryRepository.reserve(event.getOrderId(), event.getItems());
            
            // Publish success event
            kafkaTemplate.send("inventory-events", new InventoryReserved(
                event.getOrderId(),
                event.getItems()
            ));
            
        } catch (Exception e) {
            // Publish failure event
            kafkaTemplate.send("inventory-events", new InventoryReservationFailed(
                event.getOrderId(),
                e.getMessage()
            ));
        }
    }
}
```

**Code: Payment Service (listens to event)**
```java
@Service
public class PaymentService {
    @Autowired
    private PaymentRepository paymentRepository;
    
    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;
    
    @KafkaListener(topics = "order-events", groupId = "payment-group")
    public void handleOrderCreated(OrderCreatedEvent event) {
        try {
            // Charge user
            Payment payment = new Payment();
            payment.setOrderId(event.getOrderId());
            payment.setAmount(event.getAmount());
            payment.setStatus(PaymentStatus.PROCESSING);
            
            Payment savedPayment = paymentRepository.save(payment);
            
            // Call external payment gateway (with circuit breaker, retries)
            PaymentResponse response = processPaymentWithGateway(
                event.getCustomerId(),
                event.getAmount()
            );
            
            if (response.isSuccessful()) {
                savedPayment.setStatus(PaymentStatus.COMPLETED);
                savedPayment.setTransactionId(response.getTransactionId());
                paymentRepository.save(savedPayment);
                
                kafkaTemplate.send("payment-events", new PaymentProcessed(
                    event.getOrderId(),
                    event.getAmount(),
                    response.getTransactionId()
                ));
            } else {
                savedPayment.setStatus(PaymentStatus.FAILED);
                paymentRepository.save(savedPayment);
                
                kafkaTemplate.send("payment-events", new PaymentFailed(
                    event.getOrderId(),
                    response.getErrorMessage()
                ));
            }
            
        } catch (Exception e) {
            kafkaTemplate.send("payment-events", new PaymentFailed(
                event.getOrderId(),
                e.getMessage()
            ));
        }
    }
}
```

**Code: Order Service (listens to compensation events)**
```java
@Service
public class OrderCompensationService {
    @Autowired
    private OrderRepository orderRepository;
    
    // If payment fails, revert the order
    @KafkaListener(topics = "payment-events", groupId = "order-group")
    public void handlePaymentFailed(PaymentFailed event) {
        Order order = orderRepository.findById(event.getOrderId()).orElse(null);
        if (order != null) {
            order.setStatus(OrderStatus.FAILED);
            order.setFailureReason(event.getReason());
            orderRepository.save(order);
            
            // Publish OrderCancelled so inventory releases the reservation
            // ...
        }
    }
}
```

**Choreography Pros:**
- Simple: Services are loosely coupled
- Scales well: No central coordinator bottleneck

**Choreography Cons:**
- Hard to understand: Event flow across services is implicit
- Testing is difficult: Must simulate all event paths
- Circular dependencies possible: A → B → C → A

---

### Solution 2: Orchestration (Centralized Coordinator)

```
Saga Orchestrator: Central service that coordinates the entire flow
1. Order Service: Creates order, calls Orchestrator
2. Orchestrator: Calls Payment Service
3. Orchestrator: Calls Inventory Service
4. Orchestrator: Calls Notification Service
On failure, Orchestrator calls compensation steps in reverse order
```

**Code: Saga Orchestrator (coordinates the flow)**
```java
@Service
public class OrderSagaOrchestrator {
    @Autowired
    private OrderServiceClient orderClient;
    
    @Autowired
    private PaymentServiceClient paymentClient;
    
    @Autowired
    private InventoryServiceClient inventoryClient;
    
    @Autowired
    private NotificationServiceClient notificationClient;
    
    public OrderDTO executeOrderSaga(CreateOrderRequest request) {
        String orderId = null;
        String paymentId = null;
        String inventoryReservationId = null;
        
        try {
            // Step 1: Create order
            OrderDTO order = orderClient.createOrder(request);
            orderId = order.getId();
            
            // Step 2: Process payment
            PaymentDTO payment = paymentClient.processPayment(
                orderId,
                request.getAmount()
            );
            paymentId = payment.getId();
            
            // Step 3: Reserve inventory
            InventoryReservationDTO reservation = inventoryClient.reserve(
                orderId,
                request.getItems()
            );
            inventoryReservationId = reservation.getId();
            
            // Step 4: Send notification
            notificationClient.sendOrderConfirmation(orderId);
            
            // All steps succeeded
            orderClient.markOrderConfirmed(orderId);
            return order;
            
        } catch (PaymentException e) {
            // Compensation: Revert order
            if (orderId != null) {
                orderClient.cancelOrder(orderId);
            }
            throw new OrderCreationException("Payment failed: " + e.getMessage());
            
        } catch (InventoryException e) {
            // Compensation: Refund payment and cancel order
            if (paymentId != null) {
                paymentClient.refund(paymentId);
            }
            if (orderId != null) {
                orderClient.cancelOrder(orderId);
            }
            throw new OrderCreationException("Inventory reservation failed: " + e.getMessage());
            
        } catch (Exception e) {
            // Compensation: Refund, release inventory, cancel order
            if (paymentId != null) {
                paymentClient.refund(paymentId);
            }
            if (inventoryReservationId != null) {
                inventoryClient.release(inventoryReservationId);
            }
            if (orderId != null) {
                orderClient.cancelOrder(orderId);
            }
            throw new OrderCreationException("Order creation failed: " + e.getMessage());
        }
    }
}
```

**Orchestration Pros:**
- Clear control flow: Coordinator defines the sequence
- Easy to test: Orchestrator can be tested in isolation
- Simple compensation logic: Coordinator knows what to undo

**Orchestration Cons:**
- Coordinator is a bottleneck: Can become complex and centralized
- Service coupling: Services depend on coordinator

---

### Interview Answer Framework
```
Q: "Design a distributed transaction for order creation."

A:
1. Problem: ACID transactions don't span services
2. Solution: Saga pattern (two variants)
3. Choreography: Event-driven, implicit flow
   - Pros: Loose coupling, scalable
   - Cons: Hard to test, implicit dependencies
4. Orchestration: Central coordinator
   - Pros: Clear control flow, easy to test
   - Cons: Single point of failure, coupling
5. My choice: [Based on constraints]
   - For order system: Orchestration (clear business flow, strong consistency)
   - Implementation: Steps with fallback/compensation for each
6. Handle failure: Timeouts, retries, idempotency keys to prevent duplicates
```

---

## Topic 2: Circuit Breaker Pattern
**Asked in 80% of interviews | Core resilience pattern**

### The Problem
Your order service calls payment service. If payment service is slow or down:
- Requests pile up (threads exhaust)
- Order service becomes slow too (cascading failure)
- System grinds to a halt

### Solution: Circuit Breaker

**Three States:**
```
CLOSED (normal): Requests pass through
  → If failure rate > threshold (e.g., 50%) → Open

OPEN (failing): All requests fail fast without calling service
  → After wait duration (e.g., 30s) → HalfOpen

HALF_OPEN (testing): Allow one request through
  → If succeeds → Close (reset counters)
  → If fails → Open (try again later)
```

**Code: Resilience4j Configuration**
```yaml
resilience4j:
  circuitbreaker:
    instances:
      payment-service:
        registerHealthIndicator: true
        slidingWindowSize: 10           # last 10 calls
        failureRateThreshold: 50        # if 50% fail, open
        slowCallRateThreshold: 50       # if 50% are slow, open
        slowCallDurationThreshold: 2000 # calls > 2s are slow
        waitDurationInOpenState: 30000  # wait 30s before half-open
        permittedNumberOfCallsInHalfOpenState: 3  # allow 3 test calls
        automaticTransitionFromOpenToHalfOpenEnabled: true
  retry:
    instances:
      payment-service:
        maxAttempts: 3
        waitDuration: 1000
        intervalFunction: exponential
        exponentialRandomizationFactor: 0.5
  timeout:
    instances:
      payment-service:
        timeoutDuration: 5000
```

**Code: Using Circuit Breaker**
```java
@Service
public class PaymentService {
    @Autowired
    private PaymentClient paymentClient;
    
    @CircuitBreaker(
        name = "payment-service",
        fallbackMethod = "paymentFallback"
    )
    @Retry(
        name = "payment-service",
        fallbackMethod = "paymentFallback"
    )
    @Timeout(name = "payment-service")
    public PaymentResponse processPayment(PaymentRequest request) {
        // This method is guarded by circuit breaker
        // If payment-service is down, circuit opens after 50% failures
        // Further calls fail fast without trying the service
        return paymentClient.charge(request);
    }
    
    // Fallback: Called when circuit is open or call fails
    public PaymentResponse paymentFallback(PaymentRequest request, Exception e) {
        logger.warn("Payment service unavailable, queueing for retry", e);
        
        // Queue for async retry
        paymentRetryQueue.enqueue(request);
        
        // Return pending response
        return new PaymentResponse(
            PaymentStatus.PENDING,
            request.getOrderId(),
            "Payment queued for retry. Will be processed when service recovers."
        );
    }
}
```

**Code: Manual Fallback Example**
```java
@Service
public class InventoryService {
    @Autowired
    private InventoryRepository inventoryRepository;
    
    public boolean checkInventory(List<String> itemIds) {
        try {
            // Try to call external inventory service
            return externalInventoryClient.checkAvailability(itemIds);
        } catch (CircuitBreakerOpenException e) {
            logger.info("Inventory service circuit open, using cache");
            // Fallback: Check cache or assume available (risky)
            return inventoryRepository.checkCachedAvailability(itemIds);
        } catch (TimeoutException e) {
            logger.info("Inventory service timeout, defaulting to unavailable");
            // Conservative fallback: Assume not available
            return false;
        }
    }
}
```

### When to Use
- Calling external services (payment gateways, third-party APIs)
- Database connections
- Cache hits

### Interview Answer
```
Q: "Your payment service times out. How do you prevent cascading failures?"

A:
1. Circuit breaker opens after threshold (e.g., 50% failures in 10 calls)
2. Further requests fail fast (no retry)
3. After 30s, try one request to see if service recovered
4. If recovered, close circuit and resume normal flow
5. Fallback: Queue for async retry, return pending status

Combine with:
- Timeout: Don't wait forever
- Retry with backoff: Transient failures may recover
- Bulkhead: Isolate thread pools
```

---

## Topic 3: Eventual Consistency & Idempotency
**Asked in 75% of interviews | Critical for distributed systems**

### The Problem
With eventual consistency, the same request might be retried. You must ensure the outcome is the same.

Example: Charge a user $100 for an order
- First attempt: Succeeds, charge completes, response lost
- Retry: Charges again (duplicate charge!)

### Solution: Idempotency Keys

**Code: Idempotent Payment Endpoint**
```java
@RestController
@RequestMapping("/api/v1/payments")
public class PaymentController {
    @Autowired
    private PaymentService paymentService;
    
    @PostMapping
    public ResponseEntity<PaymentResponse> processPayment(
            @RequestBody PaymentRequest request,
            @RequestHeader("Idempotency-Key") String idempotencyKey) {
        
        // Check if this request was already processed
        PaymentResponse existing = paymentService.findByIdempotencyKey(idempotencyKey);
        if (existing != null) {
            // Same request, return previous response
            return ResponseEntity.ok(existing);
        }
        
        // New request, process it
        try {
            PaymentResponse response = paymentService.charge(request, idempotencyKey);
            return ResponseEntity.status(HttpStatus.CREATED).body(response);
        } catch (PaymentException e) {
            PaymentResponse failedResponse = new PaymentResponse(
                PaymentStatus.FAILED,
                request.getOrderId(),
                e.getMessage()
            );
            // Store failed response too (so retry returns same error)
            paymentService.storeResponse(idempotencyKey, failedResponse);
            return ResponseEntity.status(HttpStatus.PAYMENT_REQUIRED).body(failedResponse);
        }
    }
}
```

**Code: Service Layer**
```java
@Service
@Transactional
public class PaymentService {
    @Autowired
    private PaymentRepository paymentRepository;
    
    @Autowired
    private IdempotencyKeyRepository idempotencyKeyRepository;
    
    public PaymentResponse charge(PaymentRequest request, String idempotencyKey) {
        // Start transaction
        try {
            // Step 1: Save idempotency key (with status = IN_PROGRESS)
            IdempotencyKeyRecord keyRecord = new IdempotencyKeyRecord();
            keyRecord.setKey(idempotencyKey);
            keyRecord.setStatus(IdempotencyStatus.IN_PROGRESS);
            idempotencyKeyRepository.save(keyRecord);
            
            // Step 2: Charge the user
            String transactionId = externalPaymentGateway.charge(
                request.getCustomerId(),
                request.getAmount()
            );
            
            // Step 3: Save payment record
            Payment payment = new Payment();
            payment.setOrderId(request.getOrderId());
            payment.setAmount(request.getAmount());
            payment.setStatus(PaymentStatus.COMPLETED);
            payment.setTransactionId(transactionId);
            Payment savedPayment = paymentRepository.save(payment);
            
            // Step 4: Update idempotency key (status = COMPLETED, result = response)
            keyRecord.setStatus(IdempotencyStatus.COMPLETED);
            keyRecord.setResult(serializeResponse(new PaymentResponse(
                PaymentStatus.COMPLETED,
                request.getOrderId(),
                transactionId
            )));
            idempotencyKeyRepository.save(keyRecord);
            
            return new PaymentResponse(
                PaymentStatus.COMPLETED,
                request.getOrderId(),
                transactionId
            );
            
        } catch (Exception e) {
            // Step 5: On failure, still save idempotency key
            IdempotencyKeyRecord keyRecord = idempotencyKeyRepository.findByKey(idempotencyKey);
            keyRecord.setStatus(IdempotencyStatus.FAILED);
            keyRecord.setError(e.getMessage());
            idempotencyKeyRepository.save(keyRecord);
            
            throw new PaymentException("Charge failed: " + e.getMessage());
        }
        // Transaction commits atomically: both payment and idempotency key saved together
    }
    
    public PaymentResponse findByIdempotencyKey(String idempotencyKey) {
        IdempotencyKeyRecord record = idempotencyKeyRepository.findByKey(idempotencyKey);
        if (record == null) return null;
        
        if (record.getStatus() == IdempotencyStatus.IN_PROGRESS) {
            // Still processing, return pending
            return new PaymentResponse(
                PaymentStatus.PROCESSING,
                null,
                "Payment is being processed. Please retry."
            );
        }
        
        if (record.getStatus() == IdempotencyStatus.COMPLETED) {
            // Return stored response
            return deserializeResponse(record.getResult());
        }
        
        if (record.getStatus() == IdempotencyStatus.FAILED) {
            // Return stored error
            return new PaymentResponse(
                PaymentStatus.FAILED,
                null,
                record.getError()
            );
        }
        
        return null;
    }
}
```

**Database Schema**
```sql
CREATE TABLE idempotency_keys (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    key VARCHAR(255) UNIQUE NOT NULL,
    status ENUM('IN_PROGRESS', 'COMPLETED', 'FAILED') NOT NULL,
    result LONGTEXT,
    error TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP + INTERVAL 24 HOUR
);

CREATE INDEX idx_key ON idempotency_keys(key);
CREATE INDEX idx_expires ON idempotency_keys(expires_at);
```

### Idempotency Key Strategy
- **Format**: UUID or `<service>_<timestamp>_<user>_<operation>`
- **Lifetime**: 24 hours (for eventual consistency recovery)
- **Storage**: In-memory cache (L1) + database (L2)
- **Cleanup**: Batch job to delete expired keys

### Interview Answer
```
Q: "Design an idempotent payment endpoint. What if the network fails after charging?"

A:
1. Client generates Idempotency-Key (UUID)
2. Payment Service stores key in DB with status=IN_PROGRESS
3. Charge external gateway
4. Store result (success/failure) with key, update status
5. Return response
6. If client retries same key, return cached response immediately
7. No duplicate charge

Key insight: Idempotency key + result storage = same outcome for retries
```

---

## Topic 4: Database Per Service Pattern
**Asked in 70% of interviews | Forces you to think about data consistency**

### The Problem
One shared database across services:
- Tight coupling: Can't scale independently
- Can't use best tool per service (SQL vs NoSQL)
- Schema changes affect all services
- Transactions across services are complex

### Solution: Database Per Service

```
Order Service (PostgreSQL)
  ├─ orders table
  └─ order_items table

Payment Service (PostgreSQL)
  └─ payments table

Inventory Service (MongoDB)
  └─ inventory_items collection

Notification Service (PostgreSQL)
  └─ notifications table

NO foreign keys across databases!
```

### Challenge 1: Queries Across Services

**Problem**: How do you find all orders with a specific customer and their payments?

**Approach 1: API Calls (N+1 Risk)**
```java
@Service
public class OrderService {
    @Autowired
    private PaymentClient paymentClient;
    
    public OrderWithPaymentDTO getOrderWithPayment(String orderId) {
        // Query local database
        Order order = orderRepository.findById(orderId);
        
        // Call payment service API
        PaymentDTO payment = paymentClient.getPaymentByOrderId(orderId);
        
        return new OrderWithPaymentDTO(order, payment);
    }
}

// N+1 Problem: If you fetch 100 orders, you call payment service 100 times!
// This is slow and puts load on payment service.
```

**Approach 2: Denormalization (Read Model)**
```
Order Service writes to Orders table
Payment Service writes to Payments table

Query Service (separate database with denormalized view):
  ├─ orders_view (denormalized copy of orders + payment status)
  └─ Updated asynchronously via events
```

**Code: Event-Driven Denormalization**
```java
// Payment Service publishes an event
@Service
public class PaymentService {
    @Autowired
    private KafkaTemplate kafkaTemplate;
    
    public void processPayment(PaymentRequest request) {
        Payment payment = new Payment();
        // ... save payment ...
        
        // Publish event
        kafkaTemplate.send("payment-events", new PaymentProcessed(
            request.getOrderId(),
            payment.getAmount(),
            payment.getStatus()
        ));
    }
}

// Query Service listens and updates its denormalized view
@Service
public class QueryService {
    @Autowired
    private OrderViewRepository orderViewRepository;
    
    @KafkaListener(topics = "payment-events", groupId = "query-service")
    public void handlePaymentProcessed(PaymentProcessed event) {
        // Update denormalized view
        OrderView view = orderViewRepository.findByOrderId(event.getOrderId());
        if (view != null) {
            view.setPaymentStatus(event.getStatus());
            view.setPaymentAmount(event.getAmount());
            orderViewRepository.save(view);
        }
    }
}

// Read: Fast query on denormalized view
@Service
public class OrderQueryService {
    @Autowired
    private OrderViewRepository orderViewRepository;
    
    public OrderWithPaymentDTO getOrderWithPayment(String orderId) {
        // Single query on denormalized view (fast!)
        OrderView view = orderViewRepository.findById(orderId);
        return new OrderWithPaymentDTO(
            view.getOrderId(),
            view.getAmount(),
            view.getPaymentStatus()
        );
    }
}
```

### Challenge 2: Data Consistency

**Problem**: Order created, but payment service crashes before processing. Now order exists without payment.

**Approach: Eventual Consistency + Reconciliation**

```java
@Service
public class DataConsistencyService {
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private PaymentClient paymentClient;
    
    // Run periodically (every 5 minutes)
    @Scheduled(fixedRate = 300000)
    public void reconcileOrdersAndPayments() {
        // Find orders created > 5 minutes ago without payments
        List<Order> ordersWithoutPayment = orderRepository.findStaleOrdersWithoutPayment(
            LocalDateTime.now().minusMinutes(5)
        );
        
        for (Order order : ordersWithoutPayment) {
            try {
                // Check if payment exists in payment service
                PaymentDTO payment = paymentClient.getPayment(order.getId());
                
                if (payment == null) {
                    // Payment doesn't exist: Either:
                    // 1. Payment service didn't process the event
                    // 2. Payment failed
                    
                    // Retry payment processing
                    retryPaymentProcessing(order);
                } else {
                    // Payment exists locally, update order
                    order.setPaymentStatus(payment.getStatus());
                    orderRepository.save(order);
                }
            } catch (Exception e) {
                logger.error("Reconciliation failed for order: " + order.getId(), e);
                // Alert: Manual investigation needed
            }
        }
    }
    
    private void retryPaymentProcessing(Order order) {
        // Re-publish payment event
        kafkaTemplate.send("order-events", new OrderCreatedEvent(
            order.getId(),
            order.getCustomerId(),
            order.getItems(),
            order.getAmount()
        ));
    }
}
```

### Challenge 3: Historical Queries

**Problem**: "Give me all orders for customer X over the past year"

**Solution: Event Sourcing or Data Lake**

```
Event Stream (immutable log of all events):
  - OrderCreated(orderId=1, customerId=X, amount=100, timestamp=2024-01)
  - PaymentProcessed(orderId=1, amount=100, timestamp=2024-01-02)
  - OrderShipped(orderId=1, timestamp=2024-01-05)

Data Lake / Analytics Database:
  - Periodically consume events
  - Build aggregated views for analytics
  - Historical queries run on this, not production DB
```

---

## Topic 5: REST vs. Async Messaging
**Asked in 70% of interviews | Forces you to choose the right tool**

### Decision Matrix

| Aspect | REST (Sync) | Kafka (Async) | RabbitMQ (Async) |
|--------|-----------|---------------|------------------|
| Latency | Low (100ms) | Variable (1s+) | Variable (100ms+) |
| Consistency | Strong | Eventual | Eventual |
| Complexity | Low | High | Medium |
| Best For | Real-time APIs | Event streaming, decoupling | Task queues, pub-sub |
| Coupling | Tight | Loose | Medium |
| Order | N/A | Maintained per partition | FIFO per queue |

### Scenario 1: Real-Time Order Checkout (Use REST)

```
User clicks "Buy"
  → Order Service (REST) → Payment Service (REST) → Inventory Service (REST)
  → Response in < 5 seconds

Why sync:
- User waits for confirmation
- If any step fails, fail fast and show error
- Strong consistency needed (can't sell out-of-stock items)
```

### Scenario 2: Sending Notifications (Use Async)

```
Order Service: Order created
  → Publishes "OrderCreated" to Kafka
  → Response: Accepted (202)

Notification Service: Listens to "OrderCreated"
  → Sends email (slow, may fail, can retry)

Why async:
- Order completion shouldn't wait for email
- If email fails, queued for retry
- Notification Service can be slow without affecting checkout
```

### Scenario 3: High-Volume Event Processing (Use Kafka)

```
E-commerce: Millions of orders/sec
  → Order Service publishes to Kafka
  → Analytics Service reads and aggregates
  → Search Index Service updates Elasticsearch
  → Notification Service sends emails

Why Kafka:
- Multiple consumers need same events
- Can replay events for reprocessing
- Partitioned by order ID for parallelism
- Ordered processing per partition
```

### Code: Choosing the Right Pattern

**REST for Order Checkout**
```java
@RestController
@RequestMapping("/api/v1/orders")
public class OrderController {
    @Autowired
    private OrderService orderService;
    
    @PostMapping("/checkout")
    public ResponseEntity<OrderDTO> checkout(@RequestBody CheckoutRequest request) {
        // Synchronous: User waits for response
        try {
            OrderDTO order = orderService.processCheckout(request);
            return ResponseEntity.status(HttpStatus.CREATED).body(order);
        } catch (PaymentException e) {
            return ResponseEntity.status(HttpStatus.PAYMENT_REQUIRED)
                .body(new OrderDTO("FAILED", e.getMessage()));
        } catch (InventoryException e) {
            return ResponseEntity.status(HttpStatus.CONFLICT)
                .body(new OrderDTO("FAILED", e.getMessage()));
        }
    }
}

@Service
public class OrderService {
    @Autowired
    private PaymentClient paymentClient;
    
    @Autowired
    private InventoryClient inventoryClient;
    
    @CircuitBreaker(name = "payment", fallbackMethod = "paymentFallback")
    @Timeout(name = "payment")
    public OrderDTO processCheckout(CheckoutRequest request) {
        // Step 1: Check inventory (REST call with timeout)
        inventoryClient.checkAvailability(request.getItems());
        
        // Step 2: Process payment (REST call with circuit breaker)
        PaymentResponse payment = paymentClient.charge(request.getAmount());
        
        // Step 3: Create order
        Order order = new Order();
        // ... save and return ...
        
        return mapToDTO(order);
    }
    
    public OrderDTO paymentFallback(CheckoutRequest request, Exception e) {
        throw new PaymentException("Payment service unavailable");
    }
}
```

**Async Messaging for Notifications**
```java
@Service
public class OrderService {
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private KafkaTemplate<String, OrderCreatedEvent> kafkaTemplate;
    
    public OrderDTO createOrder(CreateOrderRequest request) {
        // Save order to database
        Order order = new Order();
        Order savedOrder = orderRepository.save(order);
        
        // Publish event asynchronously (fire and forget)
        kafkaTemplate.send(
            "order-events",
            savedOrder.getCustomerId(),  // Key: partition by customer
            new OrderCreatedEvent(
                savedOrder.getId(),
                savedOrder.getCustomerId(),
                savedOrder.getAmount()
            )
        );
        
        // Return immediately (user doesn't wait for email)
        return mapToDTO(savedOrder);
    }
}

@Service
public class NotificationService {
    @Autowired
    private EmailService emailService;
    
    @KafkaListener(
        topics = "order-events",
        groupId = "notification-service",
        concurrency = "5"  // 5 parallel consumers
    )
    public void handleOrderCreated(OrderCreatedEvent event) {
        try {
            // Send email (slow operation, may fail)
            emailService.sendOrderConfirmation(event.getCustomerId(), event.getOrderId());
        } catch (Exception e) {
            // Kafka retries automatically
            logger.error("Failed to send email", e);
            throw e;  // Rethrow so Kafka knows to retry
        }
    }
}
```

---

## Topic 6: Caching Strategies (Local vs. Redis)
**Asked in 60% of interviews | Performance differentiator**

### Local Cache (Caffeine)
```
Service 1 (L1 Cache) ─┐
                      ├─→ Database
Service 2 (L1 Cache) ─┘

Each service has its own cache. No coordination.
```

**Code: Local Cache**
```java
@Configuration
public class CacheConfig {
    @Bean
    public CacheManager cacheManager() {
        CaffeineCacheManager cacheManager = new CaffeineCacheManager("users", "orders", "products");
        cacheManager.setCaffeine(Caffeine.newBuilder()
            .expireAfterWrite(10, TimeUnit.MINUTES)
            .maximumSize(1000));
        return cacheManager;
    }
}

@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
    
    @Cacheable(value = "users", key = "#userId")
    public UserDTO getUser(String userId) {
        // Only called if cache miss
        User user = userRepository.findById(userId);
        return mapToDTO(user);
    }
    
    @CacheEvict(value = "users", key = "#userId")
    public void updateUser(String userId, UpdateUserRequest request) {
        // Update database
        User user = userRepository.findById(userId);
        user.setName(request.getName());
        userRepository.save(user);
        
        // Cache is evicted automatically
    }
}
```

### Redis Cache (Distributed)
```
Service 1 ──┐
Service 2 ──┼─→ Redis ──→ Database
Service 3 ──┘

Shared cache across services. One source of truth.
```

**Code: Redis Cache**
```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String, UserDTO> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, UserDTO> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new Jackson2JsonRedisSerializer<>(UserDTO.class));
        return template;
    }
}

@Service
public class UserService {
    @Autowired
    private RedisTemplate<String, UserDTO> redisTemplate;
    
    @Autowired
    private UserRepository userRepository;
    
    public UserDTO getUser(String userId) {
        String cacheKey = "user:" + userId;
        
        // Try Redis first
        UserDTO cached = redisTemplate.opsForValue().get(cacheKey);
        if (cached != null) {
            return cached;
        }
        
        // Cache miss: Fetch from DB
        User user = userRepository.findById(userId);
        UserDTO userDTO = mapToDTO(user);
        
        // Store in Redis (10 minute TTL)
        redisTemplate.opsForValue().set(cacheKey, userDTO, Duration.ofMinutes(10));
        
        return userDTO;
    }
    
    public void updateUser(String userId, UpdateUserRequest request) {
        // Update database
        User user = userRepository.findById(userId);
        user.setName(request.getName());
        userRepository.save(user);
        
        // Invalidate Redis
        String cacheKey = "user:" + userId;
        redisTemplate.delete(cacheKey);
    }
}
```

### Invalidation Strategies

**1. TTL-Based (Passive)**
```
Cache expires after 10 minutes
- Pro: Simple, no extra logic
- Con: Stale data for up to 10 minutes
```

**2. Event-Driven (Immediate)**
```
On update, publish event
  → Consumers invalidate cache immediately
- Pro: Stale data minimized
- Con: Complex, event coordination needed
```

**Code: Event-Driven Invalidation**
```java
@Service
public class UserService {
    @Autowired
    private RedisTemplate<String, UserDTO> redisTemplate;
    
    @Autowired
    private KafkaTemplate kafkaTemplate;
    
    public void updateUser(String userId, UpdateUserRequest request) {
        // Update database
        User user = userRepository.findById(userId);
        user.setName(request.getName());
        userRepository.save(user);
        
        // Publish cache invalidation event
        kafkaTemplate.send("cache-events", new CacheInvalidationEvent(
            "user:" + userId,
            CacheInvalidationEvent.Type.DELETE
        ));
    }
}

// All services listen and invalidate their own cache
@Service
public class CacheInvalidationListener {
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    
    @KafkaListener(topics = "cache-events", groupId = "cache-invalidation")
    public void handleCacheInvalidation(CacheInvalidationEvent event) {
        redisTemplate.delete(event.getKey());
        logger.info("Cache invalidated: {}", event.getKey());
    }
}
```

### When to Use

**Local Cache (Caffeine):**
- Single service with heavy reads
- Small dataset (< 1GB)
- Tolerance for eventual consistency (different values across instances)
- Example: User service caching user profile

**Redis Cache:**
- Multiple services reading same data
- Consistency across instances needed
- Large dataset or high concurrency
- Example: Product catalog read by multiple services

---

## Topic 7: API Gateway Pattern
**Asked in 60% of interviews | Entry point to your system**

### Responsibilities

```
Client Request
  ↓
[API Gateway]
  ├─ Authentication (JWT validation)
  ├─ Rate Limiting (per user/IP)
  ├─ Request Transformation (headers, versioning)
  ├─ Routing (to correct service)
  └─ Response Aggregation (combine results)
  ↓
Microservices
```

### Code: Spring Cloud Gateway with Authentication

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: order-service
        uri: lb://order-service
        predicates:
        - Path=/api/v1/orders/**
        filters:
        - AuthenticationFilter
        - RateLimitFilter
        - name: RewritePath
          args:
            regexp: /api/v1/orders/(?<segment>.*)
            replacement: /orders/${segment}
      
      - id: payment-service
        uri: lb://payment-service
        predicates:
        - Path=/api/v1/payments/**
        filters:
        - AuthenticationFilter
```

**Code: Authentication Filter**
```java
@Component
public class AuthenticationFilter extends AbstractGatewayFilterFactory<AuthenticationFilter.Config> {
    @Autowired
    private JwtUtil jwtUtil;
    
    @Override
    public GatewayFilter apply(Config config) {
        return (exchange, chain) -> {
            ServerHttpRequest request = exchange.getRequest();
            
            // Extract token from header
            String token = extractToken(request);
            
            if (token == null) {
                return onError(exchange, "Missing authorization token", HttpStatus.UNAUTHORIZED);
            }
            
            if (!jwtUtil.isValidToken(token)) {
                return onError(exchange, "Invalid token", HttpStatus.UNAUTHORIZED);
            }
            
            // Extract user info and pass to downstream services
            String userId = jwtUtil.extractUserId(token);
            String[] roles = jwtUtil.extractRoles(token);
            
            // Add user context to request header
            ServerHttpRequest modifiedRequest = request.mutate()
                .header("X-User-Id", userId)
                .header("X-User-Roles", String.join(",", roles))
                .build();
            
            return chain.filter(exchange.mutate().request(modifiedRequest).build());
        };
    }
    
    private String extractToken(ServerHttpRequest request) {
        String authHeader = request.getHeaders().getFirst("Authorization");
        if (authHeader != null && authHeader.startsWith("Bearer ")) {
            return authHeader.substring(7);
        }
        return null;
    }
    
    private Mono<Void> onError(ServerWebExchange exchange, String message, HttpStatus status) {
        ServerHttpResponse response = exchange.getResponse();
        response.setStatusCode(status);
        byte[] bytes = message.getBytes();
        DataBuffer buffer = response.bufferFactory().wrap(bytes);
        return response.writeWith(Mono.just(buffer));
    }
    
    public static class Config {}
}
```

**Code: Rate Limiting Filter**
```java
@Component
public class RateLimitFilter extends AbstractGatewayFilterFactory<RateLimitFilter.Config> {
    @Autowired
    private RedisTemplate<String, Integer> redisTemplate;
    
    private static final int REQUESTS_PER_MINUTE = 100;
    
    @Override
    public GatewayFilter apply(Config config) {
        return (exchange, chain) -> {
            ServerHttpRequest request = exchange.getRequest();
            
            // Extract user ID from header (added by AuthenticationFilter)
            String userId = request.getHeaders().getFirst("X-User-Id");
            if (userId == null) {
                userId = request.getRemoteAddress().getHostName();  // Fallback to IP
            }
            
            String rateLimitKey = "ratelimit:" + userId;
            
            // Check rate limit
            Integer requestCount = redisTemplate.opsForValue().get(rateLimitKey);
            if (requestCount == null) {
                requestCount = 0;
            }
            
            if (requestCount >= REQUESTS_PER_MINUTE) {
                return onError(exchange, "Rate limit exceeded", HttpStatus.TOO_MANY_REQUESTS);
            }
            
            // Increment and set expiry
            redisTemplate.opsForValue().increment(rateLimitKey);
            redisTemplate.expire(rateLimitKey, Duration.ofMinutes(1));
            
            return chain.filter(exchange);
        };
    }
    
    private Mono<Void> onError(ServerWebExchange exchange, String message, HttpStatus status) {
        // Same as AuthenticationFilter
        ServerHttpResponse response = exchange.getResponse();
        response.setStatusCode(status);
        byte[] bytes = message.getBytes();
        DataBuffer buffer = response.bufferFactory().wrap(bytes);
        return response.writeWith(Mono.just(buffer));
    }
    
    public static class Config {}
}
```

---

## Topic 8: Kubernetes Basics for Microservices
**Asked in 60% of interviews | Deployment knowledge**

### Basic Concepts

```
Cluster
  └─ Nodes (VMs)
      └─ Pods (container wrapper)
          └─ Containers (Docker image)
```

### Code: Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
  labels:
    app: order-service
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
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
        image: myregistry/order-service:1.0.0
        ports:
        - containerPort: 8080
        env:
        - name: SPRING_DATASOURCE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
        - name: SPRING_DATASOURCE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 20
          periodSeconds: 5
      imagePullSecrets:
      - name: regcred

---
apiVersion: v1
kind: Service
metadata:
  name: order-service
  labels:
    app: order-service
spec:
  selector:
    app: order-service
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
  type: LoadBalancer

---
apiVersion: v1
kind: ConfigMap
metadata:
  name: order-service-config
data:
  application.yml: |
    spring:
      jpa:
        hibernate:
          ddl-auto: validate
      kafka:
        bootstrap-servers: kafka:9092
```

### Key Concepts

**Replicas**: 3 instances of order-service running
**Rolling Update**: Gradually replace old pods with new ones
**Liveness Probe**: If unhealthy, Kubernetes kills and restarts the pod
**Readiness Probe**: If not ready, remove from load balancer
**Resource Limits**: Prevent one service from consuming all resources

---

## Topic 9: Monitoring & Distributed Tracing
**Asked in 60% of interviews | Production readiness**

### Code: Spring Boot Actuator + Micrometer + Prometheus

```yaml
management:
  endpoints:
    web:
      exposure:
        include: prometheus,health,metrics,info
  metrics:
    export:
      prometheus:
        enabled: true
    distribution:
      percentiles-histogram:
        http.server.requests: true
```

**Code: Custom Metrics**
```java
@Service
public class OrderService {
    @Autowired
    private MeterRegistry meterRegistry;
    
    public OrderDTO createOrder(CreateOrderRequest request) {
        Timer timer = Timer.start(meterRegistry);
        
        try {
            // Create order
            OrderDTO order = // ...
            
            // Metrics
            meterRegistry.counter("orders.created", "region", "us-west").increment();
            meterRegistry.gauge("pending.orders", 
                orderRepository.countByStatus(OrderStatus.PENDING));
            
            return order;
        } catch (Exception e) {
            meterRegistry.counter("orders.creation.failed", "reason", e.getClass().getName()).increment();
            throw e;
        } finally {
            timer.stop(meterRegistry.timer("orders.creation.duration"));
        }
    }
}
```

**Code: Distributed Tracing with Sleuth + Jaeger**
```yaml
spring:
  sleuth:
    sampler:
      probability: 0.1  # Sample 10% of requests
  zipkin:
    baseUrl: http://jaeger:14268/api/v2/spans
```

Sleuth automatically adds trace ID and span ID to logs:
```
2024-01-15 10:30:45 [order-service,a1b2c3d4e5f6,x7y8z9] INFO Order created
2024-01-15 10:30:46 [payment-service,a1b2c3d4e5f6,p1q2r3] INFO Payment processed
2024-01-15 10:30:47 [inventory-service,a1b2c3d4e5f6,i1j2k3] INFO Inventory reserved
```

Same trace ID (a1b2c3d4e5f6) across all services = single request flow.

---

## Topic 10: Testing Microservices
**Asked in 65% of interviews | Quality mindset**

### Unit Testing
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
        // Arrange
        CreateOrderRequest request = new CreateOrderRequest("cust1", List.of("item1"));
        Order savedOrder = new Order("ord1", OrderStatus.PENDING);
        
        when(orderRepository.save(any())).thenReturn(savedOrder);
        when(paymentClient.charge(anyString(), anyDouble()))
            .thenReturn(new PaymentResponse("SUCCESS"));
        
        // Act
        OrderDTO result = orderService.createOrder(request);
        
        // Assert
        assertThat(result.getId()).isEqualTo("ord1");
        verify(paymentClient).charge("ord1", request.getAmount());
    }
}
```

### Integration Testing
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
        mockMvc.perform(post("/api/v1/orders")
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                    {
                        "customerId": "cust1",
                        "items": ["item1"],
                        "amount": 100.0
                    }
                    """))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.id").exists());
    }
}
```

### Contract Testing
```groovy
Contract.make {
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

## Study Plan for These 10 Topics

**Week 1:** Topics 1–2 (Saga, Circuit Breaker)
**Week 2:** Topics 3–4 (Idempotency, Database per Service)
**Week 3:** Topics 5–6 (REST vs. Async, Caching)
**Week 4:** Topics 7–8 (API Gateway, Kubernetes)
**Week 5:** Topics 9–10 (Monitoring, Testing)

**Daily practice:**
- Implement each pattern from scratch
- Write code for 2 of the 10 topics per week
- Read production code from open-source projects (Spring Cloud, Netflix)
- Answer 5–10 interview questions daily