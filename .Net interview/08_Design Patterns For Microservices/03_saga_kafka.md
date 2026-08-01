# Kafka Producer Configuration (Order Service)

Before understanding the code, let's understand what the Order Service is trying to do.

Customer sends a request

```
POST /api/orders
```

Order Service

- Saves the order into SQL Server
- Publishes an event into Kafka

The event tells other microservices

```
"A new order has been created."
```

Payment Service doesn't receive an HTTP request.

Instead, it listens to Kafka.

---

# Architecture

```
                Customer

                    │

                    ▼

              Order Service

          ┌─────────┴──────────┐

          ▼                    ▼

      SQL Server             Kafka

     (OrderDB)        order-created Topic

                               │

                               ▼

                    Payment Service
```

---

# Step 1 – Kafka Configuration

```csharp
var config = new ProducerConfig
{
    BootstrapServers = "localhost:9092"
};
```

## What is ProducerConfig?

`ProducerConfig` contains all the settings required for connecting to Kafka.

Think of it as a configuration object.

```
ProducerConfig

↓

BootstrapServers

↓

Authentication

↓

Retries

↓

Timeout

↓

Compression
```

---

# BootstrapServers

```csharp
BootstrapServers = "localhost:9092";
```

This tells the producer

```
"Where is Kafka running?"
```

Example

```
localhost

Port

9092
```

If Kafka is running on another machine

```csharp
BootstrapServers = "192.168.1.20:9092";
```

If using multiple brokers

```csharp
BootstrapServers =
"broker1:9092,broker2:9092,broker3:9092";
```

Kafka automatically discovers the cluster.

---

# Step 2 – Create Producer

```csharp
using var producer =
new ProducerBuilder<Null, string>(config)
.Build();
```

Let's understand this line.

---

## ProducerBuilder

```csharp
ProducerBuilder<Null, string>
```

This means

```
Key Type

↓

Null
```

```
Value Type

↓

string
```

Kafka messages have

```
Key

+

Value
```

Example

```
Key

1001

Value

{
   Order Details
}
```

Sometimes we don't need a key.

So

```
Key

↓

Null
```

---

## Build()

```csharp
.Build()
```

Creates the Kafka Producer.

Think of it like

```
ProducerBuilder

↓

Build()

↓

Ready to Send Messages
```

---

# Step 3 – Create Event

```csharp
var orderEvent =
new OrderCreatedEvent
{
    OrderId = order.Id,

    CustomerName = order.CustomerName,

    Amount = order.Amount
};
```

This object will be sent to Kafka.

Currently

It's a C# object.

Kafka cannot understand C# objects.

---

# Step 4 – Convert Object into JSON

```csharp
var json =
JsonSerializer.Serialize(orderEvent);
```

Suppose

```csharp
OrderId = 1

CustomerName = "Rakesh"

Amount = 100000
```

After serialization

```
{
    "orderId":1,
    "customerName":"Rakesh",
    "amount":100000
}
```

Kafka stores bytes.

Usually JSON.

---

# Step 5 – Publish Message

```csharp
await producer.ProduceAsync(
    "order-created",
    new Message<Null,string>
    {
        Value = json
    });
```

This is the most important line.

Let's break it down.

---

## ProduceAsync()

```
ProduceAsync

↓

Send Message to Kafka
```

Parameters

```
Topic

+

Message
```

---

## Topic

```csharp
"order-created"
```

Think of Topic as a folder.

```
Kafka

↓

order-created

↓

Messages
```

Every OrderCreated event goes into this topic.

Payment Service subscribes to

```
order-created
```

---

## Message

```csharp
new Message<Null,string>()
```

Message contains

```
Key

↓

Null
```

```
Value

↓

JSON
```

Example

```csharp
new Message<Null,string>
{
    Value=json
}
```

Kafka receives

```
Topic

↓

order-created

↓

Message

↓

{
   "orderId":1,
   "customerName":"Rakesh",
   "amount":100000
}
```

---

# Internally What Happens?

Suppose customer places an order.

```
POST /api/orders
```

Controller calls

```
OrderService
```

OrderService

```
↓

Save Order

↓

SQL Server

↓

Commit
```

Then

```
Create Event

↓

Serialize

↓

ProduceAsync()

↓

Kafka
```

Kafka stores

```
Topic

order-created

↓

Message
```

Later

Payment Service consumes it.

---

# Kafka Flow

```
Customer

↓

OrdersController

↓

OrderService

↓

OrderDB

↓

Create Event

↓

Serialize JSON

↓

Kafka Producer

↓

order-created Topic

↓

Kafka

↓

Payment Service
```

---

# Why Do We Use Topics?

Without Topics

```
Kafka

↓

All Messages Mixed Together
```

Impossible to know

- Which message belongs to Order
- Which belongs to Payment
- Which belongs to Inventory

Instead

```
Kafka

├── order-created

├── payment-success

├── payment-failed

├── inventory-reserved

├── shipping-created
```

Each topic stores only one type of event.

---

# Why Use JSON?

Every microservice may be written in a different language.

Example

```
Order Service

↓

C#
```

```
Payment Service

↓

Java
```

```
Inventory Service

↓

Python
```

JSON is language-independent.

Every language can understand JSON.

---

# Why ProduceAsync()?

Instead of

```
Order Service

↓

HTTP Call

↓

Payment Service
```

We use

```
Order Service

↓

Kafka

↓

Payment Service
```

Benefits

- Loose Coupling
- Better Scalability
- Better Performance
- Asynchronous Communication

---

# Interview Questions

## What is ProducerConfig?

ProducerConfig stores Kafka producer configuration such as Bootstrap Servers, retries, acknowledgements, and timeouts.

---

## What is BootstrapServers?

It specifies the Kafka broker addresses that the producer connects to.

Example

```
localhost:9092
```

---

## What is ProducerBuilder?

ProducerBuilder creates a Kafka Producer instance using the provided configuration.

---

## Why Serialize to JSON?

Kafka transfers bytes, not C# objects.

JSON is language-independent and can be consumed by applications written in C#, Java, Python, Go, etc.

---

## What is a Topic?

A Topic is a logical channel where messages of the same type are stored.

Example

```
order-created
```

---

## What does ProduceAsync() do?

ProduceAsync() publishes a message asynchronously to a Kafka topic.

---

# Key Takeaways

✔ Configure Kafka using ProducerConfig.

✔ BootstrapServers tells the producer where Kafka is running.

✔ ProducerBuilder creates the Kafka Producer.

✔ C# objects are serialized into JSON before sending.

✔ ProduceAsync() sends the JSON message to a Kafka Topic.

✔ Other microservices consume messages from the topic.

✔ Topics organize messages by business events.
-----------------------------------------------------------------------------------------------------

# Module 8.3 – Payment Service (Kafka Consumer)

> **Course:** ASP.NET Core Microservices with Kafka

**Module:** Payment Service

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. Responsibilities
3. Architecture
4. Folder Structure
5. Why Payment Service is Both Consumer and Producer
6. Consumer Flow
7. Business Flow
8. Kafka Consumer Configuration
9. Why BackgroundService?
10. Why Consumer and Service are Separate?
11. Payment Processing Flow
12. Publish Success Event
13. Publish Failure Event
14. Complete Sequence Diagram
15. Naming Convention
16. Interview Questions
17. Summary
18. Key Takeaways

---

# Learning Objectives

By the end of this module you will understand

- How Payment Service consumes Kafka events
- Why Payment Service acts as both Consumer and Producer
- Why we use BackgroundService
- Why Consumer only receives messages
- Why Business Logic belongs in PaymentService
- Why another event is published after payment

---

# Responsibilities of Payment Service

Payment Service is responsible for

- Listening to Kafka
- Receiving OrderCreated events
- Processing payment
- Saving payment into PaymentDB
- Publishing PaymentSucceeded or PaymentFailed

It does NOT

- Create Orders
- Reserve Inventory
- Create Shipment

---

# Architecture

```
                    Kafka

          Topic : order-created

                  │

                  ▼

           Payment Service

     ┌────────────┴─────────────┐

     ▼                          ▼

PaymentDB                 Kafka Producer

                               │

                               ▼

      payment-succeeded / payment-failed
```

---

# Payment Service is Both Consumer and Producer

Unlike Order Service,

Payment Service performs two jobs.

## Consumer

Receives

```
OrderCreated
```

## Producer

Publishes

```
PaymentSucceeded
```

or

```
PaymentFailed
```

---

# Folder Structure

```
PaymentService

│

├── Consumers
│      OrderCreatedConsumer.cs
│
├── Services
│      PaymentService.cs
│
├── Kafka
│      KafkaProducer.cs
│
├── Data
│      PaymentDbContext.cs
│
├── Entities
│      Payment.cs
│
├── Events
│      PaymentSucceededEvent.cs
│      PaymentFailedEvent.cs
│
├── Interfaces
│      IKafkaProducer.cs
│      IPaymentService.cs
│
├── Program.cs
│
└── appsettings.json
```

---

# Why is the Consumer called OrderCreatedConsumer?

This is one of the most common beginner questions.

The class

```
OrderCreatedConsumer
```

belongs inside

```
Payment Service
```

because Payment Service consumes the

```
OrderCreated
```

event.

Think of it like this

```
Order Service

↓

Publishes

↓

OrderCreated Event

↓

Kafka

↓

Payment Service

↓

Consumes

↓

OrderCreatedConsumer
```

The Consumer is always named after the event it consumes.

NOT after the service.

---

# Naming Convention

Consumer Name

↓

Event Name

Examples

```
Payment Service

↓

OrderCreatedConsumer
```

Consumes

```
OrderCreated
```

---

```
Inventory Service

↓

PaymentSucceededConsumer
```

Consumes

```
PaymentSucceeded
```

---

```
Shipping Service

↓

InventoryReservedConsumer
```

Consumes

```
InventoryReserved
```

---

```
Notification Service

↓

ShippingCreatedConsumer
```

Consumes

```
ShippingCreated
```

This naming convention is widely used in enterprise applications.

---

# Consumer Flow

Consumer only performs four steps.

```
Read Message

↓

Deserialize JSON

↓

Call PaymentService

↓

Wait for Next Message
```

Notice

There is NO business logic here.

---

# Why BackgroundService?

Kafka Consumer never stops.

Unlike Controller,

which waits for HTTP Requests,

Consumer waits for Kafka messages.

```
Application Starts

↓

Consumer Starts

↓

Wait Forever

↓

Receive Kafka Event

↓

Process

↓

Wait Again
```

This is why we inherit from

```csharp
BackgroundService
```

---

# Kafka Consumer Configuration

Consumer connects to Kafka.

```csharp
var config = new ConsumerConfig
{
    BootstrapServers = "localhost:9092",

    GroupId = "payment-group",

    AutoOffsetReset = AutoOffsetReset.Earliest
};
```

---

## BootstrapServers

Tells Consumer

```
Where Kafka is running.
```

Example

```
localhost:9092
```

---

## GroupId

Represents

```
Consumer Group
```

Example

```
payment-group
```

Suppose

```
Payment Service 1

Payment Service 2

Payment Service 3
```

All belong to

```
payment-group
```

Kafka distributes messages among them.

---

## AutoOffsetReset

```
Earliest
```

means

```
Read all existing messages
```

instead of

```
Read only new messages.
```

---

# Subscribe

Consumer subscribes to

```
order-created
```

```csharp
consumer.Subscribe("order-created");
```

Now Payment Service starts listening.

---

# Consume Message

Consumer waits.

```csharp
var result = consumer.Consume();
```

Suppose Kafka sends

```json
{
   "CorrelationId":"ABC123",

   "OrderId":1,

   "Amount":100000
}
```

Consumer receives this JSON.

---

# Deserialize

Kafka returns

```
String
```

Convert JSON into C# Object.

```csharp
var order =
JsonSerializer.Deserialize<OrderCreatedEvent>(
result.Message.Value);
```

Now

```
order.OrderId

order.Amount

order.CorrelationId
```

are available.

---

# Why Consumer Doesn't Contain Business Logic?

Bad Design

```
Consumer

↓

Read Kafka

↓

Validate

↓

Call Bank

↓

Save Database

↓

Publish Kafka
```

Huge class.

Hard to maintain.

---

Good Design

```
Consumer

↓

PaymentService

↓

Repository

↓

Kafka Producer
```

Consumer only receives messages.

Business logic belongs in PaymentService.

This follows

```
Single Responsibility Principle
```

---

# PaymentService Responsibilities

PaymentService performs

```
Validate

↓

Call Bank API

↓

Save Payment

↓

Publish Event
```

This is the Business Layer.

---

# Save Payment

Payment is stored in

```
PaymentDB
```

Example

| PaymentId | OrderId | Amount | Status |
|-----------|----------|--------|---------|
|501|1|100000|Success|

Transaction is committed.

---

# Publish Success Event

Create

```
PaymentSucceededEvent
```

Example

```json
{
   "CorrelationId":"ABC123",

   "OrderId":1,

   "Amount":100000
}
```

Publish

```
payment-succeeded
```

Inventory Service will consume it.

---

# Publish Failure Event

Suppose Bank API returns

```
Insufficient Balance
```

Save

```
Status = Failed
```

Publish

```
payment-failed
```

Message

```json
{
   "CorrelationId":"ABC123",

   "OrderId":1,

   "Reason":"Insufficient Balance"
}
```

Order Service consumes it.

Saga Compensation starts.

---

# Complete Flow

```
Order Service

↓

Kafka

↓

OrderCreatedConsumer

↓

Deserialize JSON

↓

PaymentService

↓

Save PaymentDB

↓

Publish PaymentSucceeded

↓

Kafka

↓

Inventory Service
```

---

# Why CorrelationId is Passed?

The same

```
CorrelationId
```

travels through every service.

```
Order

↓

Payment

↓

Inventory

↓

Shipping
```

All logs can be traced using

```
ABC123
```

Very useful for

- Logging
- Monitoring
- Debugging
- Distributed Tracing

---

# Sequence Diagram

```
Order Service

↓

Publish OrderCreated

↓

Kafka

↓

OrderCreatedConsumer

↓

Deserialize JSON

↓

PaymentService

↓

PaymentDB

↓

Create PaymentSucceededEvent

↓

Kafka Producer

↓

payment-succeeded Topic

↓

Inventory Service
```

---

# Advantages

✔ Loose Coupling

✔ Independent Database

✔ Asynchronous Communication

✔ Highly Scalable

✔ Event Driven

✔ Easy to Add More Services

✔ Easy to Maintain

---

# Interview Questions

## Why is Payment Service both Consumer and Producer?

Because it receives OrderCreated events from Kafka and publishes PaymentSucceeded or PaymentFailed events after processing.

---

## Why is the class named OrderCreatedConsumer?

Because it consumes the OrderCreated event.

Consumers are named after the event they consume.

---

## Why BackgroundService?

Kafka Consumers continuously listen for messages.

BackgroundService allows the consumer to run independently of HTTP requests.

---

## Why separate Consumer and PaymentService?

Consumer is responsible only for receiving messages.

PaymentService contains business logic.

This follows the Single Responsibility Principle.

---

## Why publish another event?

After payment is processed, the next service (Inventory Service) must continue the Saga.

Publishing another event keeps services loosely coupled.

---

# Summary

Payment Service is both a Kafka Consumer and Producer.

It consumes the OrderCreated event, processes payment, stores the payment in PaymentDB, and publishes PaymentSucceeded or PaymentFailed.

The Consumer only receives Kafka messages, while PaymentService contains the business logic.

This separation makes the code easier to maintain, test, and extend.

---

# Key Takeaways

✔ Payment Service consumes Kafka messages.

✔ Consumer classes are named after the event they consume.

✔ Consumer only receives messages.

✔ Business logic belongs inside PaymentService.

✔ Payment Service saves data into PaymentDB.

✔ Payment Service publishes PaymentSucceeded or PaymentFailed.

✔ Same CorrelationId travels through the entire Saga.

✔ Payment Service acts as both Consumer and Producer.