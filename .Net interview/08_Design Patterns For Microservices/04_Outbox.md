# Module 8.5 – Outbox Pattern

> **Course:** ASP.NET Core Microservices with Kafka

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. Introduction
3. Why Do We Need Outbox?
4. The Dual Write Problem
5. Example Without Outbox
6. Problems Without Outbox
7. What is Outbox Pattern?
8. How Outbox Solves the Problem
9. Entity Framework Core Flow
10. Database Design
11. Outbox Entity
12. Order Service Flow
13. Background Publisher
14. Kafka Publishing
15. Failure Recovery
16. Advantages
17. Disadvantages
18. Best Practices
19. Interview Questions
20. Interview Answers
21. Summary
22. Key Takeaways

---

# Learning Objectives

By the end of this module you will understand

- What is Outbox Pattern
- Why Saga alone is not enough
- What is the Dual Write Problem
- How Entity Framework Core is used
- How Outbox guarantees reliable Kafka publishing
- Why BackgroundService is required
- Enterprise implementation

---

# Introduction

Suppose we have an Order Service.

Customer places an order.

```
POST /api/orders
```

Order Service

- Saves Order into SQL Server
- Publishes OrderCreated event to Kafka

Simple enough.

But what happens if Kafka is unavailable?

---

# Why Do We Need Outbox?

Current implementation

```csharp
_context.Orders.Add(order);

await _context.SaveChangesAsync();

await kafkaProducer.PublishAsync(orderCreatedEvent);
```

Looks correct.

But it has a serious problem.

---

# The Dual Write Problem

There are two different systems.

```
SQL Server

↓

Kafka
```

Saving data and publishing an event are two completely separate operations.

They are NOT part of one transaction.

---

# Example Without Outbox

Customer places an order.

```
Customer

↓

Order Service
```

Order Service

```
Save Order

↓

SQL Server

✔ Success
```

Immediately after

```
Publish Event

↓

Kafka

❌ Failure
```

---

# Database State

OrderDB

| Id | Customer | Amount |
|----|----------|---------|
|1|Rakesh|100000|

Order is saved successfully.

---

Kafka

```
No Message
```

Payment Service never receives

```
OrderCreated
```

Saga never starts.

The system becomes inconsistent.

---

# Why Does This Happen?

Because

```
SQL Transaction

↓

Commit
```

and

```
Kafka Publish
```

are independent.

SQL Server knows nothing about Kafka.

Kafka knows nothing about SQL Server.

---

# Can We Wrap Both in One Transaction?

Many developers ask

```
BEGIN TRANSACTION

↓

Save Order

↓

Publish Kafka

↓

COMMIT
```

Answer

No.

SQL Server and Kafka are different systems.

There is no shared transaction.

---

# What is Outbox Pattern?

Instead of publishing directly to Kafka,

save the event into another database table.

```
Orders Table

+

Outbox Table

↓

Single SQL Transaction

↓

Commit

↓

Background Service

↓

Kafka
```

This pattern is called

```
Outbox Pattern
```

---

# How Outbox Solves the Problem

Instead of

```
Save Order

↓

Publish Kafka
```

We do

```
Save Order

↓

Save Outbox Event

↓

Commit SQL

↓

Background Publisher

↓

Publish Kafka

↓

Mark Event Processed
```

Now the Order and Event are always saved together.

---

# Entity Framework Core Flow

Entity Framework tracks multiple entities.

```csharp
_context.Orders.Add(order);

_context.OutboxMessages.Add(outboxMessage);

await _context.SaveChangesAsync();
```

When SaveChangesAsync() executes,

EF Core generates

```
INSERT INTO Orders

INSERT INTO OutboxMessages
```

inside the same SQL transaction.

Either

```
Both succeed
```

or

```
Both fail
```

There is no inconsistent state.

---

# Database Design

Two tables.

## Orders

| Id | Customer | Amount | Status |
|----|----------|---------|--------|
|1|Rakesh|100000|Pending|

---

## OutboxMessages

| Id | EventType | Payload | Processed |
|----|-----------|----------|-----------|
|101|OrderCreated|JSON|False|

Notice

Kafka has not yet been called.

---

# Outbox Entity

```csharp
public class OutboxMessage
{
    public Guid Id { get; set; }

    public string EventType { get; set; }

    public string Payload { get; set; }

    public bool Processed { get; set; }

    public DateTime CreatedOn { get; set; }
}
```

---

# Order Service Flow

Customer

↓

Controller

↓

OrderService

↓

Create Order Entity

↓

Create OutboxMessage

↓

SaveChangesAsync()

↓

Transaction Commit

↓

Return Response

Order Service never waits for Kafka.

---

# Background Publisher

A BackgroundService continuously runs.

```
Application Starts

↓

BackgroundService Starts

↓

Read Outbox Table

↓

Find Processed = False

↓

Publish Kafka

↓

Update Processed = True

↓

Repeat Forever
```

---

# Kafka Publishing

Suppose

Outbox contains

| Processed |
|-----------|
|False|

Background Service

↓

Publish

↓

Kafka

↓

Success

↓

Update

| Processed |
|-----------|
|True|

Finished.

---

# What If Kafka Is Down?

Background Service

tries

```
Publish

↓

Failed

↓

Wait

↓

Retry

↓

Retry

↓

Retry

↓

Kafka Available

↓

Success
```

No data is lost.

---

# Failure Recovery

Suppose server crashes.

Before crash

```
Orders Table

✔ Saved

Outbox Table

✔ Saved

Kafka

❌ Not Published
```

Server restarts.

BackgroundService starts again.

Reads

```
Processed = False
```

Publishes to Kafka.

Updates

```
Processed = True
```

System recovers automatically.

---

# Complete Architecture

```
                    Customer

                        │

                        ▼

                 Order Service

             ┌──────────┴──────────┐

             ▼                     ▼

        Orders Table         Outbox Table

             │                     │

             └──────────┬──────────┘

                        ▼

                 SQL Transaction

                        │

                     Commit

                        │

                        ▼

            OutboxPublisherService

                        │

                        ▼

                    Kafka Topic

                        │

                        ▼

                 Payment Service
```

---

# Gmail Analogy

Suppose your internet is disconnected.

You click

```
Send Email
```

Does Gmail lose your email?

No.

It stores it in

```
Outbox
```

When internet becomes available

```
Outbox

↓

Send Email

↓

Move to Sent
```

Outbox Pattern works exactly the same way.

---

# Advantages

✔ Reliable Event Publishing

✔ No Lost Kafka Messages

✔ Automatic Retry

✔ Crash Recovery

✔ Eventual Consistency

✔ Perfect for Saga Pattern

---

# Disadvantages

✖ Extra Database Table

✖ Additional BackgroundService

✖ More Database Writes

✖ Requires Monitoring

---

# Best Practices

✔ Use BackgroundService

✔ Store Events as JSON

✔ Retry Failed Events

✔ Mark Processed Only After Kafka Success

✔ Add CorrelationId

✔ Make Consumers Idempotent

✔ Periodically Clean Processed Events

---

# Interview Questions

## Why do we need Outbox Pattern?

Because saving data and publishing Kafka events are two separate operations.

If Kafka fails after SaveChangesAsync(),

the event is lost.

Outbox prevents this.

---

## What problem does Outbox solve?

It solves the

```
Dual Write Problem
```

---

## What is stored in Outbox?

Typically

- EventId
- EventType
- Payload
- CorrelationId
- CreatedOn
- Processed

---

## Why do we use BackgroundService?

BackgroundService continuously checks the Outbox table and publishes pending events to Kafka.

---

## Does Outbox replace Saga?

No.

Saga

↓

Manages distributed business transactions.

Outbox

↓

Guarantees reliable event publishing.

They solve different problems.

---

## Can Outbox retry failed events?

Yes.

If Kafka is unavailable,

the event remains

```
Processed = False
```

BackgroundService retries until publishing succeeds.

---

# Interview Answer

> The Outbox Pattern solves the Dual Write Problem. Instead of saving business data and immediately publishing a Kafka event, the application stores both the business entity and an OutboxMessage entity in the same SQL transaction using Entity Framework Core. A BackgroundService later reads unprocessed Outbox records, publishes them to Kafka, and marks them as processed. This guarantees reliable event publishing even if Kafka or the application becomes temporarily unavailable.

---

# Summary

Outbox Pattern guarantees reliable communication between SQL Server and Kafka.

Instead of publishing directly to Kafka, events are first stored in an Outbox table within the same database transaction as the business data.

A BackgroundService later publishes those events to Kafka and marks them as processed.

Outbox and Saga are commonly used together in enterprise microservices.

---

# Key Takeaways

✔ Saga manages business workflow.

✔ Outbox guarantees reliable Kafka publishing.

✔ Save Order and OutboxMessage in one EF Core transaction.

✔ Publish Kafka messages from a BackgroundService.

✔ Mark events as Processed only after successful publishing.

✔ Prevent lost events even if Kafka is unavailable.

✔ Outbox + Saga is the enterprise standard for event-driven microservices.