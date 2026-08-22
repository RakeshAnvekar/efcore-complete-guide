# Kafka Manual Offset Commit - Real Examples for .NET Developers

## Why Do We Use consumer.Commit()?

Kafka does not automatically know whether your business logic completed successfully.

Example:

```text
Read Order Message
       |
       v
Save To Database
       |
       v
Send Email
       |
       v
Update Audit Table
```

Only after all processing succeeds should Kafka remember that the message is completed.

This is done using:

```csharp
consumer.Commit(result);
```

---

# Example 1: Commit After Successful Database Save

## Kafka Messages

```text
Partition 0

Offset 0 -> Order 100
Offset 1 -> Order 200
Offset 2 -> Order 300
```

## Consumer Code

```csharp
while (true)
{
    var result = consumer.Consume();

    SaveOrderToDatabase(result.Message);

    consumer.Commit(result);
}
```

---

## Execution Flow

### Step 1

Consumer reads:

```text
Offset 0 -> Order 100
```

### Step 2

Save order into SQL Server:

```text
Order Saved Successfully
```

### Step 3

Commit offset:

```csharp
consumer.Commit(result);
```

Kafka stores:

```text
Next Offset = 1
```

Meaning:

```text
Start from Offset 1 next time
```

---

## Why Is This Safe?

Suppose:

```text
Database Save Successful
```

Then:

```text
Service Crashes ❌
```

After restart:

```text
Kafka Starts From Offset 1
```

Because Offset 0 was already processed successfully.

---

# Example 2: Crash Before Commit

## Consumer Code

```csharp
while (true)
{
    var result = consumer.Consume();

    SaveOrderToDatabase(result.Message);

    throw new Exception("Application Crash");

    consumer.Commit(result);
}
```

---

## Execution Flow

### Step 1

Consumer reads:

```text
Offset 10
```

### Step 2

Database save succeeds.

### Step 3

Application crashes.

```text
Crash ❌
```

### Step 4

Commit never happens.

Kafka still has:

```text
Committed Offset = 10
```

### Step 5

Application restarts.

Kafka sends:

```text
Offset 10 Again
```

Result:

```text
Message Reprocessed
```

This is called:

```text
At-Least-Once Delivery
```

Kafka prefers duplicates rather than losing messages.

---

# Example 3: Notification Service

Imagine a Notification Processor.

Kafka Message:

```json
{
  "UserId": 1001,
  "Email": "user@test.com"
}
```

## Consumer Code

```csharp
while (true)
{
    var result = consumer.Consume();

    SendEmail(result.Message);

    consumer.Commit(result);
}
```

---

## Flow

```text
Read Message

Send Email

Commit Offset
```

Kafka stores:

```text
Message Successfully Processed
```

---

# Bad Example

```csharp
while (true)
{
    var result = consumer.Consume();

    consumer.Commit(result);

    SendEmail(result.Message);
}
```

---

## Problem

### Step 1

Read:

```text
Offset 100
```

### Step 2

Commit:

```text
Offset 101
```

### Step 3

Application crashes.

```text
Crash ❌
```

Before email is sent.

Kafka thinks:

```text
Message Processed
```

Email was never sent.

Message is permanently lost.

---

# Correct Pattern

```csharp
while (true)
{
    var result = consumer.Consume();

    SendEmail(result.Message);

    consumer.Commit(result);
}
```

Rule:

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

# Important Interview Question

## What Does consumer.Commit(result) Actually Store?

Suppose consumer reads:

```text
Offset 25
```

Then executes:

```csharp
consumer.Commit(result);
```

Kafka stores:

```text
Offset 26
```

NOT:

```text
Offset 25
```

Kafka always stores:

```text
Next Offset To Read
```

This is one of the most common Kafka interview questions.

---

# Best Practice

```csharp
while (true)
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

        // No Commit
    }
}
```

Benefits:

- No message loss
- Automatic retry after restart
- At-Least-Once delivery
- Most common enterprise pattern

---

# Key Takeaways

1. Commit only after successful processing.
2. Kafka stores the next offset to read.
3. If commit does not happen, Kafka re-delivers the message.
4. Commit before processing can cause message loss.
5. Manual commit is preferred for business-critical applications.
6. Process First → Commit Later is the golden rule.
7. Manual commits provide At-Least-Once delivery semantics.