# Module 4.2 – Kafka Offsets – Complete Guide for .NET Developers

## Overview

Offset is one of the most important concepts in Kafka.

Every message written to a Kafka partition gets a unique sequential number called an Offset.

Think of Offset as:

```text
Message Position Number
```

Just like page numbers in a book.

Kafka uses offsets to track:

- Which messages have been consumed
- Where a consumer should resume after a restart
- Which messages still need processing

---

# What is an Offset?

An Offset is a unique sequential number assigned to a message within a partition.

Example:

```text
Orders Topic

Partition 0

Offset 0 -> Order 100 Created

Offset 1 -> Order 200 Created

Offset 2 -> Order 300 Created

Offset 3 -> Order 400 Created

Offset 4 -> Order 500 Created
```

Kafka automatically assigns offsets.

Producer never sets offsets.

---

# Why Do We Need Offsets?

Imagine a partition contains:

```text
Offset 0
Offset 1
Offset 2
Offset 3
Offset 4
Offset 5
Offset 6
```

A consumer reads:

```text
Offset 0
Offset 1
Offset 2
```

Question:

```text
How does Kafka know where the consumer stopped?
```

Answer:

```text
Offset Tracking
```

Kafka remembers the consumer's position using offsets.

---

# Offset is Like a Bookmark

Suppose you are reading a book.

```text
Page 1
Page 2
Page 3
Page 4
Page 5
```

You stop at:

```text
Page 3
```

Next day:

```text
Continue From Page 4
```

Offset works exactly the same way.

---

# Real Example

Producer sends:

```text
Order 100 Created

Order 200 Created

Order 300 Created

Order 400 Created
```

Kafka stores:

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

Current position becomes:

```text
Offset 2
```

Next read starts from:

```text
Offset 2
```

---

# Offsets Are Per Partition

Topic:

```text
Orders Topic
```

Partitions:

```text
Partition 0

Offset 0
Offset 1
Offset 2
```

```text
Partition 1

Offset 0
Offset 1
Offset 2
```

Notice:

```text
Partition 0 has Offset 0

Partition 1 also has Offset 0
```

Offsets are unique only inside a partition.

They are not globally unique across a topic.

---

# Consumer Position

Partition:

```text
Offset 0
Offset 1
Offset 2
Offset 3
Offset 4
Offset 5
```

Consumer already processed:

```text
Offset 0
Offset 1
Offset 2
```

Kafka says:

```text
Consumer Position = 3
```

Meaning:

```text
Next Message = Offset 3
```

---

# What is Offset Commit?

The most important offset concept.

After processing messages, Kafka must remember where the consumer stopped.

This process is called:

```text
Offset Commit
```

---

# Example

Partition:

```text
Offset 0
Offset 1
Offset 2
Offset 3
Offset 4
```

Consumer processes:

```text
Offset 0
Offset 1
Offset 2
```

Consumer commits:

```text
Committed Offset = 3
```

Kafka stores:

```text
Next Message To Read = Offset 3
```

Important:

Kafka stores the next offset to read, not the last processed offset.

---

# Why Commit Offsets?

Suppose:

```text
Consumer Processes

Offset 0
Offset 1
Offset 2
```

Then:

```text
Application Crashes ❌
```

Without committed offsets:

```text
Consumer Must Start Again
```

With committed offsets:

```text
Resume From Offset 3
```

---

# Kafka Internal Offset Storage

Kafka stores consumer offsets in a special internal topic.

```text
__consumer_offsets
```

Kafka automatically manages this topic.

Developers usually don't interact with it directly.

---

# Auto Commit

Kafka can automatically commit offsets.

Configuration:

```csharp
var config = new ConsumerConfig
{
    EnableAutoCommit = true
};
```

Flow:

```text
Read Messages

Auto Commit

Continue Reading
```

Advantages:

```text
Simple
Easy Setup
```

Disadvantages:

```text
Less Control
Possible Message Loss
```

---

# Manual Commit

Recommended for enterprise applications.

Example:

```csharp
consumer.Commit();
```

Flow:

```text
Read Message

Process Message

Save To Database

Commit Offset
```

Advantages:

```text
More Reliable
Better Error Handling
No Data Loss During Processing
```

---

# Auto Commit Problem

Suppose:

```text
Read Offset 10
```

Kafka automatically commits:

```text
Offset 11
```

Before processing finishes:

```text
Application Crashes ❌
```

Result:

```text
Offset 10 Lost
```

Kafka believes it was already processed.

---

# Manual Commit Solution

Process message first:

```text
Offset 10
```

Save successfully:

```text
Database Updated
```

Then commit:

```text
Offset 11
```

Now crash safety is much better.

---

# Crash Scenario

Partition:

```text
Offset 0
Offset 1
Offset 2
Offset 3
```

Committed Offset:

```text
Offset 2
```

Consumer crashes.

After restart:

```text
Resume From Offset 2
```

Kafka continues from the last committed position.

---

# At-Least-Once Delivery

Suppose:

```text
Offset 10 Read
```

Consumer processes message.

Before commit:

```text
Crash ❌
```

Kafka still has:

```text
Committed Offset = 10
```

After restart:

```text
Offset 10 Read Again
```

Result:

```text
Message Processed Twice
```

This is called:

```text
At-Least-Once Delivery
```

Kafka guarantees messages are not lost, but duplicates are possible.

---

# Reprocessing Messages

Kafka allows consumers to go back and re-read messages.

Example:

```csharp
consumer.Seek(
    new TopicPartitionOffset(
        partition,
        0));
```

Now consumer starts again from:

```text
Offset 0
```

Useful when:

```text
Bug Fixed

Need To Reprocess Historical Data
```

---

# Earliest Offset

Start from beginning:

```csharp
AutoOffsetReset = AutoOffsetReset.Earliest
```

Example:

```text
Offset 0
Offset 1
Offset 2
Offset 3
```

Consumer starts at:

```text
Offset 0
```

---

# Latest Offset

Start from newest messages only:

```csharp
AutoOffsetReset = AutoOffsetReset.Latest
```

Consumer ignores old messages and waits for new ones.

---

# Visual Example

```text
Partition 0

Offset 0 -> Order 100

Offset 1 -> Order 200

Offset 2 -> Order 300

Offset 3 -> Order 400

Offset 4 -> Order 500
```

Consumer processed:

```text
Offset 0
Offset 1
Offset 2
```

Committed:

```text
Offset 3
```

After restart:

```text
Start Reading From Offset 3
```

---

# Real .NET Consumer Example

```csharp
while(true)
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

This is the most common enterprise pattern.

---

# Interview Questions

## What is an Offset?

An Offset is a sequential number assigned to a message within a partition that identifies its position.

---

## Are Offsets Unique Across a Topic?

No.

Offsets are unique only within a partition.

---

## What is Offset Commit?

Offset Commit is the process of storing the consumer's progress so it can resume from the correct location after restart.

---

## Why Is Manual Commit Preferred?

Because offsets are committed only after successful processing, reducing message loss risk.

---

## What Happens If Consumer Crashes Before Commit?

Kafka re-delivers the message after restart.

---

## What Delivery Guarantee Does Kafka Provide By Default?

```text
At-Least-Once Delivery
```

Messages are not lost but may be processed more than once.

---

# Key Takeaways

1. Offset is the position of a message inside a partition.
2. Offsets start at 0.
3. Offsets are unique per partition.
4. Consumers use offsets to track progress.
5. Kafka stores committed offsets.
6. Offset Commit allows restart recovery.
7. Auto Commit is simple but risky.
8. Manual Commit is preferred for business applications.
9. Kafka supports replaying messages using offsets.
10. Offset management is the foundation of Kafka consumer reliability.