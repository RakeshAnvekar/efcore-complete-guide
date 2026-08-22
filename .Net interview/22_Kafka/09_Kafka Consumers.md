# Module 4 – Kafka Consumers (.NET)

## Overview

A Kafka Consumer is responsible for reading messages from Kafka Topics.

Producer sends messages to Kafka and Consumer reads those messages for processing.

Basic Flow:

```text
Producer
    |
    v
Kafka Topic
    |
    v
Consumer
```

Example:

```text
Order Created Event
        |
        v
Email Service Consumer

Notification Service Consumer

Analytics Service Consumer
```

Consumers are the heart of event-driven microservices because they process business events.

---

# 4.1 Kafka Consumer

A Consumer reads messages from Kafka Topics.

Example:

```csharp
var config = new ConsumerConfig
{
    BootstrapServers = "localhost:9092",
    GroupId = "email-service",
    AutoOffsetReset = AutoOffsetReset.Earliest
};

using var consumer =
    new ConsumerBuilder<string, string>(config)
        .Build();

consumer.Subscribe("orders-topic");
```

Reading messages:

```csharp
while (true)
{
    var result = consumer.Consume();

    Console.WriteLine(result.Message.Value);
}
```

Flow:

```text
Kafka Topic
      |
      v
Consumer
```

---

# 4.2 Consumer Group

Consumer Group is a collection of consumers working together.

Purpose:

```text
Load Distribution
Parallel Processing
Horizontal Scaling
Fault Tolerance
```

Example:

Topic:

```text
orders-topic

Partition 0
Partition 1
Partition 2
Partition 3
```

Consumers:

```text
Consumer A
Consumer B
```

Kafka assigns partitions:

```text
Consumer A

P0
P1
```

```text
Consumer B

P2
P3
```

Each consumer processes different partitions.

---

## Important Rule

Within the same Consumer Group:

```text
One Partition

One Consumer
```

Example:

```text
2 Partitions

10 Consumers
```

Result:

```text
2 Consumers Active

8 Consumers Idle
```

Because only two partitions exist.

---

# 4.3 Consumer Configuration

Common Configuration:

```csharp
var config = new ConsumerConfig
{
    BootstrapServers = "localhost:9092",

    GroupId = "email-service",

    AutoOffsetReset = AutoOffsetReset.Earliest,

    EnableAutoCommit = false
};
```

Important Properties:

| Property | Purpose |
|-----------|----------|
| BootstrapServers | Kafka Brokers |
| GroupId | Consumer Group |
| AutoOffsetReset | Start Position |
| EnableAutoCommit | Offset Commit Strategy |

---

# AutoOffsetReset

Determines where consumption starts when no committed offset exists.

## Earliest

```csharp
AutoOffsetReset = AutoOffsetReset.Earliest
```

Consumer starts from:

```text
Offset 0
```

Example:

```text
Offset 0
Offset 1
Offset 2
Offset 3
```

Consumer reads everything.

---

## Latest

```csharp
AutoOffsetReset = AutoOffsetReset.Latest
```

Consumer ignores old messages.

Waits only for new messages.

Useful for:

```text
Live Notifications
Live Monitoring
```

---

# 4.4 Offset Management

Offset is the position of a message inside a partition.

Example:

```text
Partition 0

Offset 0 -> Order 100

Offset 1 -> Order 200

Offset 2 -> Order 300

Offset 3 -> Order 400
```

Consumer reads:

```text
Offset 0
Offset 1
```

Current Position:

```text
Offset 2
```

Kafka uses offsets to remember consumer progress.

---

# Why Offset Management?

Suppose:

```text
Consumer Processes

Offset 0
Offset 1
Offset 2
```

Application crashes.

After restart:

```text
Resume From Offset 3
```

This is possible because Kafka stores committed offsets.

---

# 4.5 Poll Loop

Consumers continuously ask Kafka for new messages.

This process is called Polling.

Example:

```csharp
while (true)
{
    var result = consumer.Consume();

    Process(result.Message);
}
```

Flow:

```text
Poll Kafka

Get Message

Process Message

Poll Again
```

Kafka consumers always run inside a Poll Loop.

---

# 4.6 Auto Commit

Kafka can automatically save offsets.

Configuration:

```csharp
EnableAutoCommit = true
```

Flow:

```text
Read Message

Kafka Commits Offset Automatically
```

Advantages:

```text
Easy Setup
Less Code
```

Disadvantages:

```text
Possible Message Loss
```

---

## Auto Commit Problem

Example:

```text
Read Offset 100
```

Kafka commits:

```text
Offset 101
```

Before processing:

```text
Application Crashes ❌
```

Message is lost because Kafka thinks:

```text
Already Processed
```

---

# 4.7 Manual Commit

Recommended for production applications.

Configuration:

```csharp
EnableAutoCommit = false
```

Example:

```csharp
while (true)
{
    var result = consumer.Consume();

    ProcessMessage(result.Message);

    consumer.Commit(result);
}
```

Flow:

```text
Read Message

Process Message

Commit Offset
```

---

## Golden Rule

Always:

```text
Process First

Commit Later
```

Never:

```text
Commit First

Process Later
```

---

## Manual Commit Failure Scenario

Suppose:

```text
Offset 10
```

Processing fails:

```text
Exception ❌
```

Commit never occurs.

Kafka still stores:

```text
Offset 10
```

After restart:

```text
Offset 10 Delivered Again
```

No message loss.

---

# 4.8 Offset Storage

Kafka stores committed offsets inside an internal topic.

Topic Name:

```text
__consumer_offsets
```

Example:

```text
Consumer Group

email-service
```

Kafka stores:

```text
Partition 0

Current Offset = 250
```

After restart:

```text
Resume From Offset 250
```

Developers rarely interact with this topic directly.

---

# 4.9 Rebalancing

Rebalancing happens whenever consumer membership changes.

Events:

```text
Consumer Added

Consumer Removed

Consumer Crashed
```

Kafka automatically redistributes partitions.

---

## Example

Topic:

```text
4 Partitions
```

Consumers:

```text
Consumer A

Consumer B
```

Assignment:

```text
A -> P0 P1

B -> P2 P3
```

---

New Consumer Joins:

```text
Consumer C
```

Kafka Rebalances:

```text
A -> P0 P1

B -> P2

C -> P3
```

This redistribution process is called Rebalancing.

---

## Drawback

During rebalance:

```text
Consumption Pauses Temporarily
```

Flow:

```text
Stop Processing

Reassign Partitions

Resume Processing
```

---

# 4.10 Consumer Lag

Consumer Lag measures how far behind consumers are.

Formula:

```text
Latest Offset

-

Committed Offset
```

---

## Example

Latest Offset:

```text
1000
```

Consumer Offset:

```text
800
```

Lag:

```text
1000 - 800

= 200
```

Consumer is behind by:

```text
200 Messages
```

---

# Why Consumer Lag Matters

High Lag indicates:

```text
Consumers Too Slow
```

Possible Causes:

```text
Slow Database

Slow API Calls

Heavy Business Logic

Too Few Consumers
```

---

# Healthy Lag

```text
0

1

5

10
```

Usually acceptable.

---

# Dangerous Lag

```text
50,000

100,000

1,000,000
```

Indicates serious performance issues.

---

# How To Reduce Consumer Lag

## Add More Consumers

```text
2 Consumers

↓

4 Consumers
```

---

## Increase Partitions

```text
2 Partitions

↓

8 Partitions
```

Allows more parallel processing.

---

## Improve Processing Speed

Examples:

```text
Optimize SQL Queries

Optimize API Calls

Optimize Business Logic
```

---

# Real Production Pattern

```csharp
while (true)
{
    var result = consumer.Consume();

    try
    {
        ProcessMessage(result.Message);

        consumer.Commit(result);
    }
    catch (Exception ex)
    {
        LogError(ex);

        // No Commit
    }
}
```

Benefits:

```text
Reliable

Retry Friendly

No Message Loss
```

---

# Interview Questions

## What is a Kafka Consumer?

A component that reads messages from Kafka Topics.

---

## What is a Consumer Group?

A collection of consumers sharing partitions and workload.

---

## What is Offset Management?

Tracking consumer progress using offsets.

---

## Why use Manual Commit?

To commit offsets only after successful processing.

---

## What is Rebalancing?

Redistribution of partitions when consumers join or leave a group.

---

## What is Consumer Lag?

Difference between latest offset and committed offset.

---

## Where are offsets stored?

```text
__consumer_offsets
```

Kafka internal topic.

---

# Key Takeaways

1. Consumer reads messages from Kafka Topics.
2. Consumer Groups enable horizontal scaling.
3. One partition can be consumed by only one consumer within a group.
4. Offsets track consumer progress.
5. Poll Loop continuously fetches messages.
6. Auto Commit is easy but risky.
7. Manual Commit is recommended for production systems.
8. Offsets are stored in `__consumer_offsets`.
9. Rebalancing automatically redistributes partitions.
10. Consumer Lag measures how far consumers are behind producers.

---

# Recommended Production Consumer Configuration

```csharp
var config = new ConsumerConfig
{
    BootstrapServers = "localhost:9092",

    GroupId = "email-service",

    AutoOffsetReset = AutoOffsetReset.Earliest,

    EnableAutoCommit = false
};
```

This is the most commonly used configuration in enterprise .NET Kafka applications.