# Module 5 – Kafka Offset Management (.NET)

## Overview

Offset Management is one of the most important Kafka concepts.

A Kafka Consumer must know:

```text
Which messages were already processed?

Which message should be read next?

How can processing continue after a restart?
```

Kafka solves all of these problems using Offsets.

---

# 5.1 What is an Offset?

An Offset is the unique position of a message inside a partition.

Example:

```text
OrderTopic

Partition 0

Offset 0 -> Order 100

Offset 1 -> Order 200

Offset 2 -> Order 300

Offset 3 -> Order 400
```

Think of an Offset as:

```text
Page Number in a Book
```

Example:

```text
Page 1
Page 2
Page 3
Page 4
```

Kafka uses:

```text
Offset 0
Offset 1
Offset 2
Offset 3
```

to identify message positions.

---

## Why Offsets Exist

Suppose a consumer processes:

```text
Offset 0
Offset 1
Offset 2
```

Application crashes.

After restart Kafka must know where to continue.

Kafka stores:

```text
Committed Offset = 3
```

Meaning:

```text
Start Reading From Offset 3
```

---

## Important Interview Question

### Does Kafka store the current offset or next offset?

Kafka stores:

```text
Next Offset To Read
```

Example:

Processed:

```text
Offset 0
```

Kafka stores:

```text
Offset 1
```

NOT:

```text
Offset 0
```

---

# 5.2 Offset Storage

Kafka stores offsets in a special internal topic.

Topic Name:

```text
__consumer_offsets
```

This topic is automatically created by Kafka.

---

## Example

Consumer Group:

```text
NotificationGroup
```

Kafka stores:

```text
Partition 0 -> Offset 250

Partition 1 -> Offset 300
```

After restart:

```text
Resume From Offset 250

Resume From Offset 300
```

---

## Visualization

```text
Kafka Internal Topic

__consumer_offsets

    |
    +--> NotificationGroup
    |
    +--> EmailGroup
    |
    +--> AnalyticsGroup
```

---

# 5.3 AutoOffsetReset

Used when Kafka cannot find a committed offset.

Example:

```csharp
AutoOffsetReset = AutoOffsetReset.Earliest;
```

or

```csharp
AutoOffsetReset = AutoOffsetReset.Latest;
```

---

## AutoOffsetReset.Earliest

Consumer starts from the beginning.

```csharp
AutoOffsetReset = AutoOffsetReset.Earliest;
```

Example:

```text
Offset 0
Offset 1
Offset 2
Offset 3
```

Consumer reads:

```text
0
1
2
3
```

All existing messages.

---

### Use Cases

```text
Order Processing

Payment Processing

Data Migration

Data Replay
```

---

## AutoOffsetReset.Latest

Consumer ignores old messages.

```csharp
AutoOffsetReset = AutoOffsetReset.Latest;
```

Example:

Current Topic:

```text
Offset 0
Offset 1
Offset 2
Offset 3
```

Consumer starts.

Kafka ignores:

```text
0
1
2
3
```

Consumer waits for:

```text
Offset 4
Offset 5
Offset 6
```

Only future messages.

---

### Use Cases

```text
Live Notifications

Monitoring

Real-Time Dashboards
```

---

# 5.4 Commit Strategies

Kafka needs to know when processing is complete.

Two approaches exist:

```text
Auto Commit

Manual Commit
```

---

# Auto Commit

Configuration:

```csharp
EnableAutoCommit = true;
```

Flow:

```text
Read Message

Commit Offset Automatically

Process Message
```

Advantages:

```text
Simple

Easy Setup
```

Disadvantages:

```text
Possible Message Loss
```

---

## Auto Commit Failure Scenario

```text
Read Offset 100

Commit Offset 101

Application Crashes ❌
```

Message was never processed.

Kafka thinks:

```text
Message Already Processed
```

Message lost forever.

---

# Manual Commit

Recommended for production systems.

Configuration:

```csharp
EnableAutoCommit = false;
```

Example:

```csharp
var result = consumer.Consume();

ProcessMessage(result.Message);

consumer.Commit(result);
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

```text
Read Offset 10

Process Message

Application Crashes ❌

Commit Never Happens
```

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

# 5.5 At Most Once

Guarantee:

```text
No Duplicate Messages

Message Loss Possible
```

Flow:

```text
Read Message

Commit Offset

Process Message
```

Example:

```text
Offset 10 Read

Offset 11 Committed

Application Crashes ❌
```

Message never processed.

Kafka believes:

```text
Already Done
```

Message lost.

---

## Summary

Advantages:

```text
No Duplicates
```

Disadvantages:

```text
Message Loss
```

Typical Use Cases:

```text
Logs

Metrics

Monitoring
```

---

# 5.6 At Least Once

Most common Kafka strategy.

Guarantee:

```text
No Message Loss

Duplicates Possible
```

Flow:

```text
Read Message

Process Message

Commit Offset
```

Example:

```text
Offset 10 Read

Message Processed

Application Crashes ❌

Commit Not Completed
```

Kafka thinks:

```text
Offset 10 Not Processed
```

After restart:

```text
Offset 10 Delivered Again
```

Duplicate processing occurs.

No data loss.

---

## Summary

Advantages:

```text
No Message Loss
```

Disadvantages:

```text
Duplicates Possible
```

Most enterprise Kafka applications use this approach.

---

# 5.7 Exactly Once

Goal:

```text
No Message Loss

No Duplicate Messages
```

Each message must be processed exactly one time.

---

## Challenge

Example:

```text
Process Payment

Application Crashes
```

Need to ensure:

```text
Customer Charged Once

Customer Not Missed
```

This is difficult in distributed systems.

---

## Kafka Solution

Uses:

```text
Idempotent Producer

Kafka Transactions

Transactional Consumers
```

Producer Example:

```csharp
EnableIdempotence = true;
```

Kafka assigns sequence numbers.

Duplicate messages are ignored.

---

# Delivery Guarantees Comparison

| Guarantee | Message Loss | Duplicates |
|------------|------------|------------|
| At Most Once | Possible | No |
| At Least Once | No | Possible |
| Exactly Once | No | No |

---

# Real World Usage

## At Most Once

Used for:

```text
Logs

Monitoring

Telemetry
```

---

## At Least Once

Used for:

```text
Orders

Notifications

Emails

Inventory Updates

Business Events
```

Most common production pattern.

---

## Exactly Once

Used for:

```text
Banking

Payments

Financial Systems

Money Transfers
```

Only where absolutely necessary.

---

# Recommended Production Pattern

```csharp
while(true)
{
    var result = consumer.Consume();

    try
    {
        ProcessMessage(result.Message);

        consumer.Commit(result);
    }
    catch(Exception ex)
    {
        LogError(ex);
    }
}
```

Benefits:

```text
At Least Once Delivery

No Message Loss

Reliable Processing
```

---

# Interview Questions

## What is an Offset?

Position of a message inside a partition.

---

## Where are offsets stored?

```text
__consumer_offsets
```

Kafka internal topic.

---

## What does AutoOffsetReset do?

Determines where Kafka starts reading when no committed offset exists.

---

## Difference Between Earliest and Latest?

```text
Earliest -> Read From Beginning

Latest -> Read Only New Messages
```

---

## What is At Most Once?

Commit before processing.

Possible message loss.

---

## What is At Least Once?

Process before commit.

Possible duplicate processing.

---

## What is Exactly Once?

No duplicates and no message loss using Kafka transactions and idempotence.

---

# Key Takeaways

1. Offset represents the position of a message in a partition.
2. Kafka stores offsets in `__consumer_offsets`.
3. Kafka stores the next offset to read.
4. `AutoOffsetReset.Earliest` reads old messages.
5. `AutoOffsetReset.Latest` reads only new messages.
6. Manual Commit is preferred in production.
7. At Most Once can lose messages.
8. At Least Once prevents message loss but can create duplicates.
9. Exactly Once prevents both duplicates and message loss.
10. Most enterprise .NET Kafka applications use At Least Once delivery with Manual Commit.