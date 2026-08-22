# Module 3 - Kafka Producers (.NET)

## Overview

A Kafka Producer is responsible for sending messages to Kafka Topics.

In a microservices architecture, Producers are used to publish events such as:

```text
Order Created
Order Updated
Payment Completed
User Registered
Email Requested
Notification Requested
```

Basic Flow:

```text
.NET API
    |
    v
Kafka Producer
    |
    v
Kafka Topic
    |
    v
Kafka Consumer
```

---

# 3.1 Kafka Producer

A Producer publishes messages to Kafka Topics.

Example:

```csharp
var config = new ProducerConfig
{
    BootstrapServers = "localhost:9092"
};

using var producer =
    new ProducerBuilder<string, string>(config)
        .Build();
```

Sending a message:

```csharp
await producer.ProduceAsync(
    "orders-topic",
    new Message<string, string>
    {
        Key = "1001",
        Value = "Order Created"
    });
```

Flow:

```text
Producer
   |
   v
orders-topic
```

---

# 3.2 Producer Configuration

Producer behavior is controlled through configuration.

Example:

```csharp
var config = new ProducerConfig
{
    BootstrapServers = "localhost:9092",

    Acks = Acks.All,

    EnableIdempotence = true,

    MessageSendMaxRetries = 5,

    CompressionType = CompressionType.Lz4,

    LingerMs = 5,

    BatchSize = 100000
};
```

Common Properties:

| Property | Purpose |
|-----------|----------|
| BootstrapServers | Kafka Brokers |
| Acks | Reliability |
| EnableIdempotence | Prevent Duplicates |
| MessageSendMaxRetries | Retry Failed Messages |
| CompressionType | Compress Messages |
| LingerMs | Wait Time For Batching |
| BatchSize | Batch Size |

---

# 3.3 ProduceAsync

ProduceAsync is the most commonly used API in .NET Kafka applications.

Example:

```csharp
await producer.ProduceAsync(
    "orders-topic",
    new Message<string, string>
    {
        Key = "1001",
        Value = "Order Created"
    });
```

What happens internally:

```text
Create Message
      |
      v
Send To Kafka
      |
      v
Wait For ACK
      |
      v
Return Result
```

Advantages:

- Non-blocking
- High Performance
- Asynchronous
- Scalable

---

# 3.4 Delivery Report

ProduceAsync returns a Delivery Report.

Example:

```csharp
var result = await producer.ProduceAsync(
    "orders-topic",
    new Message<string, string>
    {
        Key = "1001",
        Value = "Order Created"
    });

Console.WriteLine(result.Topic);
Console.WriteLine(result.Partition);
Console.WriteLine(result.Offset);
```

Sample Output:

```text
Topic: orders-topic

Partition: 1

Offset: 150
```

Meaning:

```text
Message Stored Successfully
```

Delivery Report Contains:

- Topic
- Partition
- Offset
- Status

---

# 3.5 Producer Acknowledgements (ACKs)

ACK determines when Kafka should confirm successful message delivery.

Kafka supports three ACK modes.

```text
Acks.None
Acks.Leader
Acks.All
```

---

## Acks.None

Configuration:

```csharp
Acks = Acks.None
```

Flow:

```text
Send Message

Return Immediately
```

Advantages:

```text
Fastest Performance
Lowest Latency
```

Disadvantages:

```text
Possible Message Loss
```

Use Cases:

```text
Logs
Metrics
Telemetry
```

---

## Acks.Leader

Configuration:

```csharp
Acks = Acks.Leader
```

Flow:

```text
Send Message
      |
Leader Stores
      |
ACK
```

Advantages:

```text
Good Performance
Reasonable Reliability
```

Disadvantages:

```text
Data Loss Possible During Leader Failure
```

---

## Acks.All

Configuration:

```csharp
Acks = Acks.All
```

Flow:

```text
Send Message
      |
Leader Stores
      |
Replicas Store
      |
ACK
```

Advantages:

```text
Highest Reliability
Highest Durability
```

Recommended For:

```text
Orders
Payments
Notifications
Banking
Insurance
```

---

# 3.6 Idempotent Producer

## Problem

Producer sends:

```text
Order Created
```

Network issue occurs.

Producer retries.

Result:

```text
Order Created
Order Created
```

Duplicate Messages.

---

## Solution

Enable Idempotence.

```csharp
EnableIdempotence = true
```

Example:

```csharp
var config = new ProducerConfig
{
    EnableIdempotence = true
};
```

Kafka assigns sequence numbers to messages.

Duplicate messages are automatically ignored.

Benefits:

```text
No Duplicate Messages
Safer Retries
Higher Reliability
```

Production Recommendation:

```csharp
Acks = Acks.All;
EnableIdempotence = true;
```

---

# 3.7 Producer Retry

Temporary failures happen frequently.

Examples:

```text
Network Glitch
Broker Restart
Short Outage
```

Without Retry:

```text
Message Failed
```

With Retry:

```text
Attempt 1 ❌

Attempt 2 ❌

Attempt 3 ✅
```

Configuration:

```csharp
MessageSendMaxRetries = 5
```

Example:

```csharp
var config = new ProducerConfig
{
    MessageSendMaxRetries = 5
};
```

Benefits:

```text
Higher Reliability
Automatic Recovery
```

---

# 3.8 Compression

Kafka can compress messages before sending them.

Without Compression:

```text
10 MB
```

With Compression:

```text
2 MB
```

Benefits:

```text
Lower Network Usage
Lower Storage Usage
Higher Throughput
```

Configuration:

```csharp
CompressionType = CompressionType.Lz4
```

Available Compression Types:

```text
None
Gzip
Snappy
Lz4
Zstd
```

Most Common Choice:

```csharp
CompressionType = CompressionType.Lz4
```

Reason:

```text
Good Speed
Low CPU Usage
Good Compression Ratio
```

---

# 3.9 Batching

Kafka does not send every message immediately.

Instead it groups multiple messages together.

Without Batching:

```text
Message 1 -> Network Call

Message 2 -> Network Call

Message 3 -> Network Call
```

Many expensive network requests.

---

With Batching:

```text
Message 1
Message 2
Message 3
Message 4
      |
      v
Single Batch
      |
      v
Single Network Call
```

Benefits:

```text
Higher Throughput
Lower Network Cost
Better Performance
```

---

## BatchSize

Controls maximum batch size.

```csharp
BatchSize = 100000
```

Meaning:

```text
100 KB Batch
```

before Kafka sends messages.

---

## LingerMs

Kafka waits briefly before sending messages.

Configuration:

```csharp
LingerMs = 5
```

Meaning:

```text
Wait 5 milliseconds
Collect More Messages
Create Batch
Send Batch
```

Without Linger:

```text
Message Arrives

Send Immediately
```

With Linger:

```text
Message Arrives

Wait 5ms

Collect More Messages

Send Batch
```

Result:

```text
Higher Throughput
```

---

# Recommended Production Configuration

```csharp
var config = new ProducerConfig
{
    BootstrapServers = "broker1:9092",

    Acks = Acks.All,

    EnableIdempotence = true,

    MessageSendMaxRetries = 5,

    CompressionType = CompressionType.Lz4,

    LingerMs = 5,

    BatchSize = 100000
};
```

Benefits:

```text
High Reliability
High Throughput
No Duplicates
Efficient Network Usage
```

---

# Interview Questions

## What is a Kafka Producer?

A component that publishes messages to Kafka Topics.

---

## What does ProduceAsync do?

It asynchronously sends a message to Kafka and returns a Delivery Report.

---

## What information does Delivery Report contain?

- Topic
- Partition
- Offset
- Status

---

## Why use Acks.All?

To ensure data is replicated before Kafka confirms success.

---

## Why use EnableIdempotence?

To prevent duplicate messages during retries.

---

## Why use Producer Retry?

To automatically recover from temporary failures.

---

## Why use Compression?

To reduce network traffic and storage consumption.

---

## Why use Batching?

To improve throughput by reducing the number of network calls.

---

# Key Takeaways

1. Producer sends messages to Kafka Topics.
2. ProduceAsync is the primary API used in .NET.
3. Delivery Reports confirm successful writes.
4. ACKs control message durability.
5. Acks.All provides the highest reliability.
6. Idempotence prevents duplicate messages.
7. Retries improve fault tolerance.
8. Compression improves network efficiency.
9. Batching improves throughput.
10. Production systems commonly use:

```csharp
Acks = Acks.All;
EnableIdempotence = true;
CompressionType = CompressionType.Lz4;
MessageSendMaxRetries = 5;
LingerMs = 5;
BatchSize = 100000;
```