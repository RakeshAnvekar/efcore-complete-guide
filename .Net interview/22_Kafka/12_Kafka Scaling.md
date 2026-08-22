# Module 8 – Kafka Scaling (.NET)

## Overview

Kafka is built for high-throughput distributed systems.

It can handle:

- Millions of Users
- Millions of Events
- Billions of Messages
- Large Scale Microservices

Kafka achieves this through Horizontal Scaling.

---

# What is Scaling?

Scaling means increasing system capacity to handle:

```text
More Users
More Messages
More Traffic
More Producers
More Consumers
```

Example:

```text
Today:
10,000 Messages/Day

After Growth:
100 Million Messages/Day
```

Kafka infrastructure must grow to support the increase.

---

# 8.1 Horizontal Scaling

There are two types of scaling.

## Vertical Scaling

Increase machine size.

Example:

```text
4 CPU
16 GB RAM

↓

16 CPU
64 GB RAM
```

Limitations:

- Expensive
- Hardware Limits
- Single Point of Failure

---

## Horizontal Scaling

Add more machines.

Example:

```text
Broker1

↓

Broker1
Broker2
Broker3
Broker4
```

Benefits:

- Better Availability
- Better Performance
- Easier Growth
- Fault Tolerance

Kafka primarily uses Horizontal Scaling.

---

# 8.2 Partition Scaling

Partition is Kafka's primary scaling unit.

Important Rule:

```text
One Partition
=
One Consumer Thread
```

---

## Example

Topic:

```text
OrderTopic

P0
P1
```

Maximum Parallel Consumers:

```text
2
```

---

Traffic increases.

Increase partitions.

```text
OrderTopic

P0
P1
P2
P3
P4
P5
```

Now Kafka supports:

```text
6 Consumers
```

working simultaneously.

---

## Benefits

```text
More Parallel Reads

More Parallel Writes

Higher Throughput
```

---

## Real Example

Before:

```text
2 Partitions
2 Consumers

Capacity:
200 Messages/Sec
```

After:

```text
10 Partitions
10 Consumers

Capacity:
1000+ Messages/Sec
```

---

# 8.3 Broker Scaling

Broker = Kafka Server

Example:

```text
Broker1
```

All partitions are stored on one server.

Problem:

```text
Storage Limit

CPU Limit

Network Limit
```

---

## Scale Out

Add more brokers.

```text
Broker1
Broker2
Broker3
```

Kafka distributes partitions.

Example:

```text
Broker1

P0
P1

Broker2

P2
P3

Broker3

P4
P5
```

---

## Benefits

```text
More Storage

More CPU

More Network Capacity

High Availability
```

---

## Large Scale Example

```text
100 Partitions

10 Brokers
```

Traffic automatically spreads across brokers.

---

# 8.4 Consumer Scaling

Consumer scaling means adding more consumer instances.

---

## Example

Topic:

```text
6 Partitions
```

Consumer Group:

```text
Consumer1
Consumer2
Consumer3
```

Partition Assignment:

```text
Consumer1 → P0,P1

Consumer2 → P2,P3

Consumer3 → P4,P5
```

---

Traffic increases.

Add more consumers.

```text
Consumer1
Consumer2
Consumer3
Consumer4
Consumer5
Consumer6
```

Assignment:

```text
Consumer1 → P0

Consumer2 → P1

Consumer3 → P2

Consumer4 → P3

Consumer5 → P4

Consumer6 → P5
```

Maximum Parallelism.

---

## Important Rule

Consumer Count > Partition Count

Example:

```text
2 Partitions

10 Consumers
```

Result:

```text
2 Active

8 Idle
```

Extra consumers remain idle.

---

# 8.5 Producer Scaling

Kafka supports multiple producers simultaneously.

Example:

```text
Order Service

Payment Service

Inventory Service

Notification Service
```

All can publish events concurrently.

---

## Example

```text
Producer1
Producer2
Producer3
Producer4
```

Writing to:

```text
OrderTopic
```

Kafka automatically distributes writes across partitions.

---

## Scale Producers

Before:

```text
1 Producer
```

After:

```text
10 Producers
```

Kafka handles concurrent writes efficiently.

---

# 8.6 Throughput Optimization

Throughput means:

```text
Messages Processed Per Second
```

Goal:

```text
Increase Throughput
```

---

## Technique 1: Increase Partitions

Before:

```text
2 Partitions
```

After:

```text
20 Partitions
```

Result:

```text
Higher Parallelism
```

---

## Technique 2: Increase Consumers

Before:

```text
2 Consumers
```

After:

```text
20 Consumers
```

Result:

```text
Faster Processing
```

---

## Technique 3: Compression

```csharp
CompressionType = CompressionType.Lz4;
```

Benefits:

```text
Less Network Traffic

Faster Data Transfer
```

---

## Technique 4: Batching

```csharp
BatchSize = 100000;

LingerMs = 5;
```

Benefits:

```text
Fewer Network Calls

Higher Throughput
```

---

## Technique 5: Add Brokers

Before:

```text
Broker1
```

After:

```text
Broker1
Broker2
Broker3
Broker4
```

Benefits:

```text
More Capacity

Better Performance

Higher Availability
```

---

# 8.7 Load Distribution

Kafka automatically distributes workload.

---

## Producer Side

Messages spread across partitions.

```text
P0
P1
P2
P3
P4
P5
```

---

## Consumer Side

Partitions spread across consumers.

```text
Consumer1 → P0,P1

Consumer2 → P2,P3

Consumer3 → P4,P5
```

---

## Broker Side

Partitions spread across brokers.

```text
Broker1 → P0,P1

Broker2 → P2,P3

Broker3 → P4,P5
```

Storage and traffic distributed evenly.

---

# Real Production Example

Current System:

```text
1 Million Messages/Day
```

Target:

```text
50 Million Messages/Day
```

Scaling Strategy:

```text
Increase Partitions

Increase Consumer Instances

Add More Brokers

Enable Compression

Enable Batching

Scale Producers Horizontally

Monitor Consumer Lag
```

---

# Complete Scaling Example

Initial Setup:

```text
1 Broker

2 Partitions

2 Consumers

1 Producer
```

Capacity:

```text
1000 Messages/Sec
```

---

Scaled Setup:

```text
5 Brokers

20 Partitions

20 Consumers

10 Producers
```

Capacity:

```text
100,000+ Messages/Sec
```

---

# Interview Questions

## How does Kafka scale?

Kafka scales through:

```text
Partitions

Consumers

Producers

Brokers
```

---

## What is Kafka's primary scaling unit?

```text
Partition
```

---

## Why increase partitions?

To increase parallel processing.

---

## Why add brokers?

To increase:

```text
Storage

Network Capacity

Throughput

Availability
```

---

## Why add consumers?

To process messages faster.

---

## Can 10 consumers consume 2 partitions?

No.

```text
2 Active

8 Idle
```

---

## How do you improve Kafka throughput?

```text
More Partitions

More Consumers

More Brokers

Compression

Batching
```

---

# Key Takeaways

1. Kafka scales horizontally.
2. Partition is the primary scaling unit.
3. More partitions = more parallelism.
4. More brokers = more storage and throughput.
5. More consumers = faster processing.
6. More producers = higher write throughput.
7. Compression reduces network traffic.
8. Batching improves throughput.
9. Kafka automatically balances load.
10. Kafka scaling formula:

```text
More Traffic
      ↓
More Partitions
      ↓
More Consumers
      ↓
More Brokers
```

This is the standard Kafka scaling strategy used in enterprise systems.