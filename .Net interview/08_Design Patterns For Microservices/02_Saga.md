# Module 8.2 – Saga Pattern

> **Course:** Microservices Architecture Master Roadmap

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. Introduction
3. What Problem Does Saga Solve?
4. Why Database Transactions Don't Work
5. What is Saga Pattern?
6. Local Transactions
7. Compensation Transactions
8. Real-Time Example (E-Commerce)
9. Kafka-Based Saga Flow
10. Choreography Saga
11. Orchestration Saga
12. Kafka Topics
13. Database Changes
14. Advantages
15. Disadvantages
16. Best Practices
17. Interview Questions
18. Interview Answers
19. Summary
20. Key Takeaways

---

# Learning Objectives

By the end of this module, you will understand:

- What the Saga Pattern is
- Why Saga is required in Microservices
- Why Distributed Transactions are avoided
- Local Transactions
- Compensation Transactions
- Kafka-based communication
- Choreography Saga
- Orchestration Saga
- Enterprise implementation

---

# Introduction

In a Monolithic application,

multiple operations can be wrapped inside a single database transaction.

```text
BEGIN TRANSACTION

Create Order

Take Payment

Reserve Inventory

Create Shipment

COMMIT
```

If any operation fails,

```text
ROLLBACK
```

Everything is restored automatically.

---

# Why Doesn't This Work in Microservices?

In Microservices,

every service owns its own database.

```
Order Service
    │
    ▼
Order Database

Payment Service
    │
    ▼
Payment Database

Inventory Service
    │
    ▼
Inventory Database

Shipping Service
    │
    ▼
Shipping Database
```

Since every service has its own database,

a single SQL transaction cannot span all databases.

---

# What Problem Does Saga Solve?

Suppose

Customer places an order.

Steps

```
Create Order

↓

Take Payment

↓

Reserve Inventory

↓

Create Shipment
```

Now suppose

Shipping fails.

Current state

```
Order

✔ Created

Payment

✔ Completed

Inventory

✔ Reserved

Shipping

❌ Failed
```

Customer has already paid.

Inventory has already been reduced.

Shipment failed.

The system is inconsistent.

Saga Pattern solves this problem.

---

# What is Saga Pattern?

Saga Pattern is a sequence of **local transactions**.

Each service performs its own transaction.

If every transaction succeeds,

the business process completes successfully.

If one transaction fails,

previous successful transactions are compensated using business actions.

---

# Local Transaction

Each microservice performs its own database transaction.

Example

Order Service

```
Insert Order

Commit
```

Payment Service

```
Debit Customer

Commit
```

Inventory Service

```
Reserve Product

Commit
```

Every service commits independently.

---

# Compensation Transaction

A Compensation Transaction reverses the effect of a previously completed local transaction.

Examples

```
Payment Success

↓

Refund Payment
```

```
Inventory Reserved

↓

Release Inventory
```

```
Order Created

↓

Cancel Order
```

Instead of SQL Rollback,

Saga performs Business Rollback.

---

# Real-Time Example

Suppose customer buys

```
Apple iPhone

₹100,000
```

Services involved

```
Order Service

Payment Service

Inventory Service

Shipping Service

Notification Service
```

Communication happens through Kafka.

---

# Step 1 – Order Service

Customer clicks

```
Buy Now
```

Flow

```
Customer

↓

API Gateway

↓

Order Service
```

Order Service creates

```
OrderId =1001

Status = Pending
```

Database

```
OrderDB

Order Created
```

Order Service publishes

Kafka Topic

```
order-created
```

Message

```json
{
    "orderId":1001,
    "customerId":25,
    "amount":100000
}
```

---

# Step 2 – Payment Service

Payment Service subscribes to

```
order-created
```

Kafka delivers the message.

Payment succeeds.

Database

```
PaymentDB

Payment Status

SUCCESS
```

Publish Event

Topic

```
payment-success
```

---

# Step 3 – Inventory Service

Inventory Service consumes

```
payment-success
```

Stock available

```
20

↓

19
```

Database updated.

Publish

```
inventory-reserved
```

---

# Step 4 – Shipping Service

Shipping consumes

```
inventory-reserved
```

Shipment creation fails.

Publish

```
shipping-failed
```

---

# Compensation Begins

Shipping failure event

```
shipping-failed
```

is published into Kafka.

---

# Inventory Compensation

Inventory Service consumes

```
shipping-failed
```

Release stock

```
19

↓

20
```

Publish

```
inventory-released
```

---

# Payment Compensation

Payment Service consumes

```
shipping-failed
```

Refund customer.

Database

```
SUCCESS

↓

REFUNDED
```

Publish

```
payment-refunded
```

---

# Order Compensation

Order Service consumes

```
payment-refunded
```

Update Order

```
Pending

↓

Cancelled
```

Database

```
Order Cancelled
```

Publish

```
order-cancelled
```

---

# Notification Service

Consumes

```
order-cancelled
```

Sends Email

```
Dear Customer,

Your payment has been refunded because shipment creation failed.
```

Saga Completed.

---

# Complete Kafka Flow

```
                 Customer
                     │
                     ▼
              Order Service
                     │
                     ▼
        Kafka : order-created
                     │
                     ▼
            Payment Service
                     │
                     ▼
      Kafka : payment-success
                     │
                     ▼
          Inventory Service
                     │
                     ▼
     Kafka : inventory-reserved
                     │
                     ▼
          Shipping Service
                     │
              Shipment Failed
                     │
                     ▼
      Kafka : shipping-failed
            ┌────────────┴─────────────┐
            ▼                          ▼
 Inventory Service             Payment Service
 Release Stock                 Refund Payment
            │                          │
            └────────────┬─────────────┘
                         ▼
                  Order Service
                  Cancel Order
                         │
                         ▼
              Notification Service
                 Send Email
```

---

# Kafka Topics

| Topic | Published By | Consumed By |
|---------|--------------|-------------|
| order-created | Order Service | Payment Service |
| payment-success | Payment Service | Inventory Service |
| inventory-reserved | Inventory Service | Shipping Service |
| shipping-failed | Shipping Service | Payment & Inventory |
| payment-refunded | Payment Service | Order Service |
| order-cancelled | Order Service | Notification Service |

---

# Choreography Saga

No central coordinator.

Every service publishes and consumes Kafka events.

```
Order

↓

Kafka

↓

Payment

↓

Kafka

↓

Inventory

↓

Kafka

↓

Shipping
```

Advantages

- Loosely coupled
- Highly scalable
- Easy to add new services

Disadvantages

- Difficult debugging
- Complex event chains
- Harder monitoring

---

# Orchestration Saga

Uses a Saga Orchestrator.

```
Customer

↓

Saga Orchestrator

↓

Order

↓

Payment

↓

Inventory

↓

Shipping
```

If Shipping fails

```
Saga Orchestrator

↓

Refund Payment

↓

Release Inventory

↓

Cancel Order
```

Advantages

- Centralized workflow
- Easier monitoring
- Easier debugging

Disadvantages

- Additional service
- Orchestrator maintenance

---

# Advantages

✔ No Distributed Transactions

✔ Better Scalability

✔ Independent Databases

✔ Loose Coupling

✔ Fault Tolerance

✔ Event Driven

✔ Business Rollback

✔ Highly Scalable

---

# Disadvantages

✖ Eventual Consistency

✖ Complex Compensation Logic

✖ More Infrastructure

✖ More Monitoring Required

✖ Harder Debugging

---

# Best Practices

✔ Keep Local Transactions Small

✔ Make Compensation Idempotent

✔ Use Kafka for Reliable Messaging

✔ Log Every Saga Step

✔ Use Correlation IDs

✔ Implement Retry Policies

✔ Monitor Saga Execution

✔ Store Saga State

---

# Interview Questions

## What is Saga Pattern?

## Why is Saga needed?

## Why can't we use SQL Transactions?

## What is Compensation Transaction?

## Difference between Choreography and Orchestration?

## Why is Kafka used?

## Is Kafka implementing Saga?

## Can Saga work without Kafka?

---

# Interview Answers

## What is Saga Pattern?

Saga Pattern manages distributed business transactions by breaking them into multiple local transactions and executing compensation transactions when failures occur.

---

## Why can't SQL Transaction be used?

Because each Microservice owns its own database and distributed database transactions are not practical in scalable Microservices.

---

## What is Compensation Transaction?

A business operation that reverses a previously completed local transaction.

Example

```
Payment Success

↓

Refund
```

---

## Why Kafka?

Kafka provides reliable asynchronous communication between Microservices.

It stores and delivers events.

Kafka does **not** execute business logic.

---

## Does Kafka implement Saga?

No.

Kafka is only a Message Broker.

Saga logic is implemented by the Microservices or by a Saga Orchestrator.

---

## Difference between Choreography and Orchestration?

| Choreography | Orchestration |
|--------------|---------------|
| Event Driven | Central Coordinator |
| No Orchestrator | Uses Orchestrator |
| Loose Coupling | Centralized Workflow |
| Harder Debugging | Easier Debugging |

---

# Summary

Saga Pattern is one of the most important Microservices design patterns.

Instead of using one distributed database transaction, Saga breaks a business process into multiple local transactions.

When a failure occurs, Compensation Transactions restore the business to a consistent state.

Kafka acts as the communication backbone, enabling Microservices to exchange events asynchronously while remaining loosely coupled.

---

# Key Takeaways

✔ Saga replaces Distributed Transactions.

✔ Every Microservice commits its own Local Transaction.

✔ Compensation Transactions perform business rollback.

✔ Kafka is a Message Broker, not the Saga itself.

✔ Choreography uses events.

✔ Orchestration uses a central coordinator.

✔ Saga provides Eventual Consistency instead of ACID consistency.

✔ Saga is one of the most frequently asked Microservices interview topics.