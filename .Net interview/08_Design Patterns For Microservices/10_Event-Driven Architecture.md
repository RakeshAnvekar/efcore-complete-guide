# Module 10.2 – Event-Driven Architecture (EDA)

> **Course:** ASP.NET Core Microservices with .NET

**Module:** Event-Driven Architecture (EDA)

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. What is Event-Driven Architecture?
3. Why Do We Need Event-Driven Architecture?
4. Traditional Request-Response Architecture
5. Problems with Synchronous Communication
6. Event-Driven Architecture Flow
7. Event Broker
8. Kafka vs Event-Driven Architecture
9. Azure Service Bus vs Kafka
10. Event Publisher
11. Event Consumer
12. Event Broker
13. Real-Time Example
14. Enterprise Architecture
15. Advantages
16. Disadvantages
17. Best Practices
18. Interview Questions
19. Summary
20. Key Takeaways

---

# Learning Objectives

By the end of this module, you will understand

- What is Event-Driven Architecture
- Why Event-Driven Architecture is needed
- Difference between Event-Driven Architecture and Kafka
- Difference between Kafka and Azure Service Bus
- Event Publisher
- Event Consumer
- Event Broker
- Enterprise implementation
- Interview concepts

---

# What is Event-Driven Architecture?

Event-Driven Architecture (EDA) is an architectural style where Microservices communicate by publishing and consuming events instead of directly calling each other.

Instead of

```
Service A

↓

HTTP Call

↓

Service B
```

we use

```
Service A

↓

Publish Event

↓

Event Broker

↓

Service B
```

Services communicate through events instead of direct communication.

---

# Why Do We Need Event-Driven Architecture?

Suppose we have

```
Order Service

↓

Payment Service

↓

Inventory Service

↓

Shipping Service
```

Using HTTP,

Order Service must wait until Payment Service responds.

Payment Service waits for Inventory.

Inventory waits for Shipping.

This creates

- Tight Coupling
- Long Response Time
- Cascading Failures
- Difficult Scaling

---

# Traditional Request-Response Architecture

```
Customer

↓

Order Service

↓

HTTP

↓

Payment Service

↓

HTTP

↓

Inventory Service

↓

HTTP

↓

Shipping Service
```

Every service depends on another service.

If one service becomes slow,

the entire chain becomes slow.

---

# Problems with Synchronous Communication

Suppose Payment Service is unavailable.

```
Order Service

↓

Payment Service

↓

Timeout
```

Order Service also becomes slow.

Eventually

```
Customer

↓

Timeout
```

This is known as

```
Cascading Failure
```

---

# Event-Driven Architecture Flow

Instead of calling services directly,

Order Service publishes an event.

```
Order Service

↓

Publish OrderCreated

↓

Event Broker

↓

Payment Service

↓

Inventory Service

↓

Email Service

↓

Analytics Service
```

The publisher does not know who consumes the event.

---

# What is an Event?

An event represents something that has already happened.

Examples

```
OrderCreated

PaymentCompleted

InventoryReserved

ShipmentCreated

CustomerRegistered

OrderCancelled
```

Events describe completed business actions.

---

# Event Publisher

The Publisher creates and publishes events.

Example

```
Order Service

↓

Create Order

↓

Publish OrderCreated
```

Publisher does not know who will consume the event.

---

# Event Consumer

Consumers subscribe to events.

Example

```
Payment Service

↓

Listen to OrderCreated

↓

Process Payment
```

Another consumer

```
Email Service

↓

Listen to OrderCreated

↓

Send Email
```

Multiple consumers can process the same event independently.

---

# What is an Event Broker?

The Event Broker receives events from publishers and delivers them to consumers.

Examples

- Kafka
- Azure Service Bus
- RabbitMQ
- AWS SNS/SQS
- Google Pub/Sub

Architecture

```
Publisher

↓

Event Broker

↓

Consumer
```

---

# Kafka vs Event-Driven Architecture

Many beginners think

```
Kafka == Event-Driven Architecture
```

This is incorrect.

Event-Driven Architecture is the architectural pattern.

Kafka is one technology used to implement that pattern.

Relationship

```
Event-Driven Architecture

↓

Kafka

RabbitMQ

Azure Service Bus

AWS SNS/SQS

Google Pub/Sub
```

---

# Azure Service Bus vs Kafka

Both implement Event-Driven Architecture.

Example using Kafka

```
Order Service

↓

Kafka

↓

Payment Service
```

Example using Azure Service Bus

```
Order Service

↓

Azure Service Bus

↓

Payment Service
```

Architecture remains exactly the same.

Only the messaging technology changes.

---

# Real-Time Example

Customer places an order.

```
Customer

↓

Order Service

↓

Save Order

↓

Publish OrderCreated
```

Event Broker

```
Kafka
```

Consumers

```
Payment Service

Inventory Service

Notification Service

Analytics Service
```

Every service processes the same event independently.

---

# Enterprise Architecture

```
                      Customer

                          │

                          ▼

                    API Gateway

                          │

                    Order Service

                          │

                 Publish OrderCreated

                          │

                    Event Broker

       ┌──────────┬──────────┬──────────┬──────────┐

       ▼          ▼          ▼          ▼

 Payment     Inventory    Email    Analytics

 Service       Service    Service    Service
```

Notice

Order Service knows nothing about the consumers.

---

# Event-Driven Architecture with Kafka

```
Order Service

↓

Kafka Topic

↓

Payment Service

↓

Inventory Service

↓

Shipping Service

↓

Notification Service
```

Kafka stores the events and delivers them to subscribers.

---

# Event-Driven Architecture with Azure Service Bus

```
Order Service

↓

Azure Service Bus Queue/Topic

↓

Payment Service

↓

Inventory Service

↓

Shipping Service
```

Same architecture.

Different messaging platform.

---

# Event-Driven Architecture with Saga

```
Order Service

↓

OrderCreated

↓

Kafka

↓

Payment Service

↓

PaymentCompleted

↓

Kafka

↓

Inventory Service

↓

InventoryReserved

↓

Kafka

↓

Shipping Service
```

Saga coordinates business transactions through events.

---

# Event-Driven Architecture with Outbox

```
Order Service

↓

Orders Table

↓

Outbox Table

↓

Background Publisher

↓

Kafka

↓

Consumers
```

Outbox guarantees reliable event publishing.

---

# Advantages

✔ Loose Coupling

✔ Independent Deployment

✔ High Scalability

✔ Asynchronous Communication

✔ Better Fault Isolation

✔ Easy to Add New Consumers

✔ Supports Event Replay (Kafka)

✔ Enterprise Friendly

---

# Disadvantages

✖ Eventual Consistency

✖ More Infrastructure

✖ Harder Debugging

✖ Duplicate Event Handling

✖ Message Ordering Considerations

---

# Best Practices

✔ Publish Business Events

✔ Keep Events Immutable

✔ Include CorrelationId

✔ Use Idempotent Consumers

✔ Use Outbox Pattern

✔ Use Saga for Distributed Transactions

✔ Monitor Dead Letter Queues

✔ Version Event Contracts Carefully

---

# Interview Questions

## What is Event-Driven Architecture?

Event-Driven Architecture is an architectural style where services communicate by publishing and consuming events rather than making direct synchronous calls.

---

## Is Kafka Event-Driven Architecture?

No.

Kafka is a distributed event streaming platform.

It is one technology used to implement Event-Driven Architecture.

---

## Can Azure Service Bus be used for Event-Driven Architecture?

Yes.

Azure Service Bus is another messaging platform that supports Event-Driven Architecture.

---

## What is an Event Broker?

An Event Broker receives events from publishers and delivers them to consumers.

Examples include Kafka, Azure Service Bus, RabbitMQ, and AWS SNS/SQS.

---

## What is the difference between Publisher and Consumer?

Publisher

Creates and publishes events.

Consumer

Subscribes to events and processes them.

---

## Why use Event-Driven Architecture?

It provides loose coupling, scalability, asynchronous communication, and independent deployments.

---

# Summary

Event-Driven Architecture is a software architecture where Microservices communicate through business events instead of direct HTTP calls.

An Event Broker such as Kafka or Azure Service Bus acts as the intermediary between publishers and consumers.

Kafka and Azure Service Bus are technologies used to implement Event-Driven Architecture, but they are not the architecture itself.

Event-Driven Architecture works naturally with Database per Service, Saga Pattern, Outbox Pattern, and Circuit Breaker to build scalable and resilient enterprise Microservices.

---

# Key Takeaways

✔ Event-Driven Architecture is an architectural pattern.

✔ Kafka, Azure Service Bus, RabbitMQ, and AWS SNS/SQS are messaging technologies.

✔ Publishers create events.

✔ Consumers process events.

✔ Event Brokers deliver events.

✔ Services remain loosely coupled.

✔ Event-Driven Architecture is widely used in enterprise Microservices.

✔ It integrates naturally with Saga, Outbox, and Database per Service.

---

# Next Module

## Module 10.3 – Event Broker Deep Dive (Kafka vs RabbitMQ vs Azure Service Bus)

In the next module, you will learn

- Event Broker internals
- Kafka Architecture
- Topics
- Partitions
- Offsets
- Producers
- Consumers
- Consumer Groups
- Azure Service Bus Queues
- Topics & Subscriptions
- RabbitMQ Exchanges
- When to use Kafka vs RabbitMQ vs Azure Service Bus
- Enterprise best practices