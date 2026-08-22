# Module 3.10 – Kafka Producer Performance Tuning (.NET)

## Overview

Kafka Producers can send thousands or even millions of messages per second.

However, poor configuration can lead to:

- High Latency
- Low Throughput
- Excessive Network Usage
- Increased CPU Consumption
- Message Backlogs

Producer Performance Tuning helps optimize:

```text
Throughput
Latency
CPU Usage
Network Usage
Reliability
```

---

# Performance vs Reliability

Before tuning, understand the tradeoff.

```text
Higher Reliability
       ↓
Lower Performance

Higher Performance
       ↓
Lower Reliability
```

Example:

```csharp
Acks = Acks.All
```

Higher reliability but slightly slower.

```csharp
Acks = Acks.None
```

Fastest but risky.

---

# Important Producer Performance Settings

## 1. BatchSize

Controls how many messages Kafka collects before sending.

Default:

```csharp
BatchSize
```

Example:

```csharp
var config = new ProducerConfig
{
    BatchSize = 100000
};
```

Flow:

Without batching:

```text
Message1 -> Send

Message2 -> Send

Message3 -> Send
```

With batching:

```text
Message1
Message2
Message3
     |
     v
Single Batch
     |
     v
Send Once
```

Benefits:

```text
Fewer Network Calls
Higher Throughput
```

---

## 2. LingerMs

Controls how long Kafka waits before sending a batch.

Example:

```csharp
LingerMs = 5
```

Meaning:

```text
Wait 5 milliseconds

Collect More Messages

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

Benefits:

```text
Larger Batches
Higher Throughput
```

Tradeoff:

```text
Slightly Higher Latency
```

---

## 3. Compression

Compress messages before sending.

Example:

```csharp
CompressionType = CompressionType.Lz4
```

Without Compression:

```text
10 MB Sent
```

With Compression:

```text
2 MB Sent
```

Benefits:

```text
Lower Network Usage
Lower Storage Usage
Faster Broker Writes
```

---

### Compression Options

```text
None
Gzip
Snappy
Lz4
Zstd
```

Most Common:

```text
Lz4
```

Best balance between:

```text
Speed
CPU Usage
Compression Ratio
```

---

## 4. Acknowledgements

ACKs directly affect performance.

### Acks.None

```csharp
Acks = Acks.None
```

Fastest.

```text
Send

Return Immediately
```

---

### Acks.Leader

```csharp
Acks = Acks.Leader
```

Leader must save first.

Balanced performance.

---

### Acks.All

```csharp
Acks = Acks.All
```

Leader and replicas must save.

Highest reliability.

Slightly slower.

---

# Throughput Comparison

```text
Acks.None   -> Highest Throughput

Acks.Leader -> High Throughput

Acks.All    -> Lower Throughput
```

---

## 5. Enable Idempotence

Configuration:

```csharp
EnableIdempotence = true
```

Purpose:

```text
Prevent Duplicate Messages
```

Benefits:

```text
Safer Retries
Higher Reliability
```

Small performance cost.

Usually worth enabling.

---

## 6. Producer Retry

Configuration:

```csharp
MessageSendMaxRetries = 5
```

Example:

```text
Attempt 1 ❌

Attempt 2 ❌

Attempt 3 ✅
```

Benefits:

```text
Automatic Recovery
Higher Reliability
```

---

## 7. Max In Flight Requests

Controls how many requests can be sent before ACKs arrive.

Example:

```csharp
MaxInFlight = 5
```

Flow:

```text
Send Batch 1

Send Batch 2

Send Batch 3

Wait For ACKs
```

Benefits:

```text
Higher Throughput
```

Too high may affect ordering.

Common production value:

```text
5
```

---

## 8. Buffer Memory

Producer keeps messages in memory before sending.

Example:

```text
Producer Buffer
```

If producer generates messages faster than Kafka can send:

```text
Messages Stored In Buffer
```

Larger buffers help during traffic spikes.

---

# High Throughput Configuration

Example:

```csharp
var config = new ProducerConfig
{
    BootstrapServers = "localhost:9092",

    BatchSize = 100000,

    LingerMs = 10,

    CompressionType = CompressionType.Lz4,

    Acks = Acks.Leader
};
```

Goal:

```text
Maximum Throughput
```

Use Cases:

```text
Analytics
Logging
Metrics
Telemetry
```

---

# High Reliability Configuration

Example:

```csharp
var config = new ProducerConfig
{
    BootstrapServers = "localhost:9092",

    Acks = Acks.All,

    EnableIdempotence = true,

    MessageSendMaxRetries = 5,

    CompressionType = CompressionType.Lz4,

    BatchSize = 100000,

    LingerMs = 5
};
```

Goal:

```text
Maximum Reliability
```

Use Cases:

```text
Orders
Payments
Notifications
Banking
Insurance
```

---

# Real Notification System Example

Suppose:

```text
100,000 Emails Per Hour
```

Without tuning:

```text
One Message

One Network Call
```

Poor performance.

---

With batching:

```text
100 Messages

Single Network Call
```

Result:

```text
Higher Throughput
Lower Network Cost
```

---

# Performance Tuning Strategy

## For Maximum Throughput

```csharp
BatchSize = Large

LingerMs = 10

Compression = Lz4

Acks = Leader
```

---

## For Maximum Reliability

```csharp
Acks = All

EnableIdempotence = true

Retries = 5
```

---

## Balanced Production Setup

```csharp
var config = new ProducerConfig
{
    BootstrapServers = "localhost:9092",

    Acks = Acks.All,

    EnableIdempotence = true,

    CompressionType = CompressionType.Lz4,

    BatchSize = 100000,

    LingerMs = 5,

    MessageSendMaxRetries = 5
};
```

Most enterprise systems use a configuration similar to this.

---

# Interview Questions

## What producer settings improve throughput?

```text
BatchSize
LingerMs
Compression
MaxInFlight
```

---

## Why use batching?

To reduce the number of network calls and improve throughput.

---

## Why use compression?

To reduce network traffic and storage usage.

---

## Which compression algorithm is most common?

```text
Lz4
```

because it provides a good balance between speed and compression.

---

## What is the purpose of LingerMs?

Kafka waits briefly before sending messages so it can build larger batches.

---

## What is the purpose of BatchSize?

Controls the maximum size of a producer batch.

---

## Why use EnableIdempotence?

To prevent duplicate messages during retries.

---

# Key Takeaways

1. Producer tuning is about throughput vs latency vs reliability.
2. BatchSize controls batch size.
3. LingerMs controls batching delay.
4. Compression reduces network traffic.
5. Lz4 is the most common compression algorithm.
6. Acks impact both performance and durability.
7. Idempotence prevents duplicate messages.
8. Retries improve fault tolerance.
9. Batching is one of the biggest performance improvements.
10. Most production systems use:
    - Acks.All
    - EnableIdempotence
    - Lz4 Compression
    - Batching
    - Retries