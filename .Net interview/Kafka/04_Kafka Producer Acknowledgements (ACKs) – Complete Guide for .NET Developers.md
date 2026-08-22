# Module 4.1 – Kafka Producer Acknowledgements (ACKs) – Complete Guide for .NET Developers

## Overview

Producer Acknowledgements (ACKs) define when Kafka should confirm to the Producer that a message has been successfully written.

Simple flow:

```text
Producer
    |
Send Message
    |
    v
Kafka Broker
    |
Send ACK
    |
    v
Producer
```

ACK means:

```text
"I have successfully received and stored your message."
```

ACK configuration directly affects:

- Reliability
- Performance
- Durability
- Throughput

---

# Why Do We Need ACKs?

Consider a .NET API publishing an order event.

```csharp
await producer.ProduceAsync(
    "orders-topic",
    new Message<string, string>
    {
        Key = "1001",
        Value = "Order Created"
    });
```

Question:

```text
How does ProduceAsync know the message was actually stored?
```

Answer:

```text
Kafka sends an ACK (Acknowledgement).
```

Without ACKs, the producer would never know whether the message was successfully written or lost.

---

# Kafka Cluster Example

Assume we have a Kafka cluster:

```text
Broker1
Broker2
Broker3
```

Topic:

```text
orders-topic
```

Partition:

```text
Partition 0
```

Producer:

```text
Order API
```

When the producer publishes a message, Kafka decides when to return success based on the configured ACK mode.

---

# ACK Modes

Kafka supports three acknowledgement modes:

```text
Acks.None
Acks.Leader
Acks.All
```

---

# Acks.None (acks=0)

## Configuration

```csharp
var config = new ProducerConfig
{
    Acks = Acks.None
};
```

## How It Works

```text
Producer
   |
   | Send Message
   |
   +-------------> Broker
```

Producer does not wait for any confirmation.

Immediately:

```text
ProduceAsync returns Success
```

### Timeline

```text
Producer ---> Broker

Producer assumes success immediately
```

No ACK is received.

### Failure Scenario

```text
Producer ---> Broker
```

Before the broker stores the message:

```text
Broker crashes ❌
```

Result:

```text
Message Lost
```

Producer still thinks:

```text
Success
```

### Advantages

- Fastest
- Lowest Latency
- Highest Throughput

### Disadvantages

- Possible Message Loss
- No Delivery Guarantee

### Use Cases

```text
Application Logs
Metrics
Telemetry
Monitoring Events
```

Losing some messages is acceptable.

---

# Acks.Leader (acks=1)

## Configuration

```csharp
var config = new ProducerConfig
{
    Acks = Acks.Leader
};
```

## How It Works

Producer waits until the Leader Broker stores the message.

```text
Producer
    |
    v
Leader Broker
```

### Timeline

```text
Producer
    |
    v
Leader Broker

Leader Stores Message

Leader ---> ACK

Producer Receives Success
```

### Example

Cluster:

```text
Broker1 (Leader)
Broker2 (Replica)
Broker3 (Replica)
```

Producer sends:

```text
Order Created
```

Leader stores:

```text
Offset 100
```

Leader replies:

```text
ACK
```

Producer returns success.

### Failure Scenario

Suppose:

```text
Leader stores message

Leader sends ACK
```

Immediately after:

```text
Leader crashes ❌
```

Before replicas receive the message.

Result:

```text
Message May Be Lost
```

Because replicas never got a copy.

### Advantages

- Good Performance
- Low Latency
- Better Reliability than Acks.None

### Disadvantages

- Data Loss Possible During Leader Failure

### Common Use Cases

```text
Notifications
Analytics Events
High Throughput Systems
```

---

# Acks.All (acks=-1)

## Configuration

```csharp
var config = new ProducerConfig
{
    Acks = Acks.All
};
```

## How It Works

Producer waits until all required replicas store the message.

```text
Producer
     |
     v
Leader Broker
     |
     +--> Replica Broker
     |
     +--> Replica Broker
```

### Timeline

Step 1

```text
Producer
    |
    v
Leader
```

Step 2

```text
Leader Stores Message
```

Step 3

```text
Leader Replicates Message
```

to:

```text
Follower 1
Follower 2
```

Step 4

```text
Followers Send ACK
```

Step 5

```text
Leader Sends ACK To Producer
```

Producer finally gets:

```text
Success
```

### Why Is Acks.All Safer?

Suppose:

```text
Leader crashes ❌
```

after the producer receives success.

Replicas already contain the message:

```text
Follower1 ✅
Follower2 ✅
```

Kafka elects a new leader.

Result:

```text
No Data Loss
```

### Advantages

- Highest Reliability
- Highest Durability
- Best Data Protection

### Disadvantages

- Slightly Higher Latency
- Slightly Lower Throughput

### Common Use Cases

```text
Order Processing
Payment Processing
Banking
Insurance
Financial Systems
```

---

# What Does ProduceAsync Wait For?

## Acks.None

```text
Send Message
      |
      v
Return Immediately
```

## Acks.Leader

```text
Send Message
      |
Leader Stores
      |
ACK
      |
Return
```

## Acks.All

```text
Send Message
      |
Leader Stores
      |
Replicas Store
      |
ACK
      |
Return
```

---

# Reliability Comparison

| ACK Mode | Waits For | Reliability | Performance |
|-----------|------------|-------------|-------------|
| None | Nobody | Lowest | Highest |
| Leader | Leader Broker | Medium | High |
| All | All Required Replicas | Highest | Lower |

---

# Production Recommendations

## Logging Systems

```csharp
Acks = Acks.None;
```

Reason:

```text
Speed is more important than reliability.
```

---

## Notification Systems

```csharp
Acks = Acks.Leader;
```

Reason:

```text
Balanced reliability and performance.
```

---

## Order Processing Systems

```csharp
Acks = Acks.All;
```

Reason:

```text
Orders must never be lost.
```

---

## Payment Processing Systems

```csharp
Acks = Acks.All;
```

Reason:

```text
Financial transactions require maximum durability.
```

---

# Recommended Production Configuration

```csharp
var config = new ProducerConfig
{
    BootstrapServers = "localhost:9092",

    Acks = Acks.All,

    EnableIdempotence = true,

    CompressionType = CompressionType.Lz4,

    MessageSendMaxRetries = 5
};
```

---

# ACKs and Idempotence

Best practice:

```csharp
var config = new ProducerConfig
{
    Acks = Acks.All,
    EnableIdempotence = true
};
```

Benefits:

```text
No Message Loss
No Duplicate Messages
Maximum Reliability
```

---

# Interview Questions

## What is Kafka Producer ACK?

An ACK is a confirmation sent by Kafka indicating that the message was successfully written.

---

## Difference Between Acks.None, Acks.Leader and Acks.All?

| Setting | Waits For |
|----------|-----------|
| None | No confirmation |
| Leader | Leader broker only |
| All | All required replicas |

---

## Which ACK Mode Is Best?

For business-critical systems:

```csharp
Acks = Acks.All;
EnableIdempotence = true;
```

---

## Can Messages Be Lost With Acks.Leader?

Yes.

If the leader acknowledges the message and crashes before replication occurs, the message may be lost.

---

## Why Is Acks.All Safer?

Because Kafka waits until the message is replicated to all required replicas before confirming success.

---

# Key Takeaways

1. ACK determines when Kafka confirms a successful write.
2. Acks.None provides maximum performance but lowest reliability.
3. Acks.Leader waits only for the leader broker.
4. Acks.All waits for all required replicas.
5. Acks.All provides the highest durability.
6. Business-critical applications should use Acks.All.
7. Combine Acks.All with EnableIdempotence for production workloads.
8. ProduceAsync waits based on the configured ACK mode.
9. ACK settings directly affect performance and reliability.
10. Understanding ACKs is essential for designing reliable Kafka systems.