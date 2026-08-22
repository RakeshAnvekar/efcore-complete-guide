# Apache Kafka - Module 1.2: Why Kafka Was Created

## Overview

Before Kafka was introduced, most applications communicated using direct API calls, databases, or traditional message queues.

As systems grew larger and moved toward microservices, these approaches created significant challenges related to scalability, reliability, performance, and maintenance.

Kafka was created to solve these problems and provide a highly scalable event-streaming platform.

---

# The Problem Before Kafka

Imagine an e-commerce application.

When an order is placed, multiple systems must react:

* Payment Service
* Inventory Service
* Email Service
* Shipping Service
* Analytics Service

## Traditional Approach

```text
Order Service
     |
     +--> Payment Service
     |
     +--> Inventory Service
     |
     +--> Email Service
     |
     +--> Shipping Service
     |
     +--> Analytics Service
```

The Order Service directly calls every downstream service.

This creates several problems.

---

# Problem 1: Tight Coupling

## What is Tight Coupling?

When one service knows too much about another service.

```text
Order Service
      |
      +--> Payment Service
```

Order Service must know:

* Payment API URL
* Authentication details
* Request format
* Response format

If Payment Service changes, Order Service must also change.

---

## Example

```csharp
await paymentService.ProcessPayment(order);

await inventoryService.UpdateStock(order);

await emailService.SendEmail(order);
```

The Order Service becomes dependent on multiple services.

---

## How Kafka Solves It

```text
Order Service
      |
      v
    Kafka
      |
      +--> Payment Service
      |
      +--> Inventory Service
      |
      +--> Email Service
```

Order Service publishes an event.

Consumers decide whether to process it.

Services become independent.

---

# Problem 2: System Availability

Suppose Email Service is down.

Without Kafka:

```text
Order Service
      |
      +--> Email Service ❌
```

The entire operation may fail.

Customer may not receive confirmation.

Order processing may stop.

---

## With Kafka

```text
Order Service
      |
      v
    Kafka
      |
      +--> Email Service ❌
```

Kafka stores the message.

When Email Service comes back online:

```text
Email Service
      |
      v
Reads missed messages
```

No data loss occurs.

---

# Problem 3: Scalability

Imagine receiving:

```text
100 Orders / Day
```

Everything works fine.

Later:

```text
1,000,000 Orders / Day
```

Direct service calls become a bottleneck.

---

## Without Kafka

```text
Order Service
     |
     +--> Payment
     +--> Inventory
     +--> Email
```

Order Service becomes overloaded.

---

## With Kafka

```text
Order Service
      |
      v
    Kafka
      |
      +--> Multiple Consumers
```

Consumers can be increased horizontally.

```text
Consumer 1
Consumer 2
Consumer 3
Consumer 4
```

Kafka distributes the load.

---

# Problem 4: Slow Consumers

Email processing may take several seconds.

Without Kafka:

```text
Order Service
      |
      +--> Wait for Email
```

Customer experiences slow response time.

---

## With Kafka

```text
Order Created
      |
      v
    Kafka
```

Order Service immediately returns success.

Email processing happens later.

This is called:

### Asynchronous Processing

---

# Problem 5: Data Loss

Suppose Inventory Service crashes.

Without Kafka:

```text
Order Service
      |
      +--> Inventory Service ❌
```

The message may be lost forever.

---

## With Kafka

```text
Order Created
      |
      v
    Kafka
```

Kafka persists data on disk.

Messages remain available until consumed.

---

# Problem 6: Multiple Consumers Need Same Data

Suppose an order event is needed by:

* Payment Service
* Inventory Service
* Email Service
* Analytics Service

Without Kafka:

```text
Order Service
      |
      +--> Payment
      +--> Inventory
      +--> Email
      +--> Analytics
```

Many integrations are required.

---

## With Kafka

```text
                Kafka
                  |
                  |
      +-----------+-----------+
      |           |           |
      v           v           v
 Payment     Inventory     Email
 Service      Service      Service

                  |
                  v
            Analytics
```

One event serves many consumers.

---

# Problem 7: Event Replay

Sometimes businesses need to reprocess historical data.

Example:

```text
Last 30 days orders
```

Traditional systems often cannot replay old messages.

---

## Kafka Solution

Kafka stores events for a configurable retention period.

```text
Order Created
Order Updated
Order Shipped
```

Consumers can re-read old events.

This feature is called:

### Event Replay

---

# Problem 8: Audit Requirements

Banks often need complete transaction history.

Traditional systems:

```text
Process Data
Delete Data
```

History may be unavailable.

---

## Kafka Approach

Every event is stored.

```text
Transaction Created
Transaction Approved
Transaction Settled
```

Complete audit trail becomes available.

---

# Event-Driven Architecture

Kafka enables Event-Driven Architecture (EDA).

Instead of:

```text
Service A --> Service B
```

Applications communicate through events.

```text
Service A
     |
     v
   Kafka
     |
     v
Service B
```

Examples of events:

```text
OrderCreated
OrderCancelled
PaymentCompleted
UserRegistered
```

---

# Real-World Companies Using Kafka

## Netflix

Uses Kafka for:

* Streaming events
* User activity tracking
* Monitoring

---

## Uber

Uses Kafka for:

* Ride events
* Driver location updates
* Trip processing

---

## LinkedIn

Kafka was originally developed by LinkedIn.

Used for:

* Activity streams
* Analytics
* Messaging

---

## Amazon

Uses event-driven systems heavily for:

* Orders
* Inventory
* Payments
* Recommendations

---

# Why Kafka Became Popular

Kafka solved major distributed system challenges:

| Problem             | Kafka Solution            |
| ------------------- | ------------------------- |
| Tight Coupling      | Event-Based Communication |
| Service Failure     | Message Persistence       |
| Slow Consumers      | Asynchronous Processing   |
| Scaling Issues      | Partitioning              |
| Data Loss           | Replication               |
| Audit Requirements  | Event Retention           |
| Multiple Consumers  | Consumer Groups           |
| Replay Requirements | Offset-Based Reads        |

---

# .NET Perspective

In .NET microservices, Kafka is commonly used between services.

```text
Order API
     |
     v
Kafka Topic
     |
     +--> Payment Service
     |
     +--> Inventory Service
     |
     +--> Notification Service
```

The API publishes an event:

```csharp
await producer.ProduceAsync(
    "orders",
    new Message<string, string>
    {
        Key = order.Id.ToString(),
        Value = JsonSerializer.Serialize(order)
    });
```

Consumers process the event independently.

---

# Interview Questions

## Why was Kafka created?

Kafka was created to solve scalability, reliability, fault tolerance, and asynchronous communication problems in distributed systems.

---

## What problem does Kafka solve?

Kafka solves:

* Tight coupling
* Service failures
* Data loss
* Scaling issues
* Slow processing
* Event replay requirements

---

## Why use Kafka in Microservices?

Kafka allows services to communicate asynchronously without direct dependencies.

---

## What is Event-Driven Architecture?

A design where services communicate by publishing and consuming events instead of direct API calls.

---

# Key Takeaways

1. Kafka was created for distributed systems.
2. It reduces coupling between services.
3. It enables asynchronous communication.
4. It stores events durably.
5. It supports replaying old events.
6. It scales horizontally.
7. It improves system reliability.
8. It is a foundation for Event-Driven Architecture.
9. It allows multiple consumers to process the same event.
10. It is heavily used in modern microservices.

---

# Next Module

**Module 1.3 – Kafka vs Traditional Message Queues (Kafka vs RabbitMQ vs Azure Service Bus)**
