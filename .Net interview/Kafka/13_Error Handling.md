# Module 10 – Kafka Error Handling (.NET)

## Overview

In real-world systems, message processing can fail due to:

- Network Issues
- Database Failures
- API Timeouts
- Invalid Data
- Corrupt Messages
- Service Outages

A production-grade Kafka consumer must handle failures gracefully without losing messages.

This module covers:

- Retry Strategy
- Dead Letter Queue (DLQ)
- Poison Messages
- Message Validation
- Circuit Breaker

---

# Why Error Handling is Important

Suppose a Kafka Consumer receives:

```json
{
  "OrderId": 1001,
  "Email": "user@test.com"
}
```

Consumer logic:

```text
Read Message
     ↓
Call Email Service
     ↓
Send Email
```

Email service fails:

```text
500 Internal Server Error
```

Questions:

```text
Should we lose the message?
Should we retry?
Should we move it elsewhere?
```

Kafka Error Handling answers these questions.

---

# 10.1 Retry Strategy

## What is Retry?

Retry means attempting message processing again after a failure.

Instead of:

```text
Fail Once
     ↓
Stop Processing
```

We do:

```text
Fail
 ↓
Retry
 ↓
Retry
 ↓
Success
```

---

## Example

```text
Attempt 1 ❌

Attempt 2 ❌

Attempt 3 ✅
```

Message processed successfully.

---

## When Should We Retry?

Retry temporary failures:

```text
Network Timeout

Database Connection Issue

HTTP 500 Error

Service Temporarily Unavailable
```

---

## When Should We NOT Retry?

Do not retry:

```text
Invalid Email

Missing OrderId

Bad JSON

Validation Failure
```

These are Poison Messages.

---

# Types of Retry

## Immediate Retry

```text
Fail
 ↓
Retry Immediately
```

---

## Delayed Retry

```text
Fail
 ↓
Wait 5 Seconds
 ↓
Retry
```

---

## Exponential Backoff (Recommended)

```text
Attempt 1 → Wait 1 Second

Attempt 2 → Wait 2 Seconds

Attempt 3 → Wait 4 Seconds

Attempt 4 → Wait 8 Seconds
```

Benefits:

```text
Prevents System Overload

Gives Services Time To Recover
```

---

## Polly Example

```csharp
var retryPolicy = Policy
    .Handle<Exception>()
    .WaitAndRetryAsync(
        3,
        retryAttempt => TimeSpan.FromSeconds(retryAttempt));
```

---

# 10.2 Dead Letter Queue (DLQ)

## What is DLQ?

DLQ stands for:

```text
Dead Letter Queue
```

In Kafka, a DLQ is simply another topic.

Example:

```text
orders-topic
```

Main Topic

```text
orders-dlq
```

Failed Messages Topic

---

## Why Do We Need DLQ?

Suppose processing fails:

```text
Retry 1 ❌

Retry 2 ❌

Retry 3 ❌
```

Still failing.

Instead of:

```text
Lose Message ❌
```

Move it to:

```text
orders-dlq ✅
```

---

## Flow

```text
orders-topic
      |
      v
Consumer
      |
      +---- Success
      |
      +---- Retry
              |
              v
          Failed
              |
              v
        orders-dlq
```

---

## Benefits

```text
No Message Loss

Continue Processing

Easy Investigation

Supports Reprocessing
```

---

## .NET Example

```csharp
await dlqProducer.ProduceAsync(
    "orders-dlq",
    new Message<string,string>
    {
        Key = order.OrderId.ToString(),
        Value = JsonSerializer.Serialize(order)
    });
```

---

# 10.3 Poison Messages

## What is a Poison Message?

A Poison Message is a message that will never process successfully.

Example:

```json
{
  "Email": "user@test.com"
}
```

Required field:

```text
OrderId Missing
```

Retrying:

```text
Retry 1 ❌

Retry 2 ❌

Retry 100 ❌
```

Will never succeed.

---

## Examples

### Invalid Email

```json
{
  "OrderId": 1001,
  "Email": "abc"
}
```

---

### Missing OrderId

```json
{
  "Email": "user@test.com"
}
```

---

### Corrupt JSON

```json
{OrderId:1001
```

---

## Solution

Do not retry.

Move directly to:

```text
DLQ
```

---

## Temporary Failure vs Poison Message

### Temporary Failure

```text
Database Timeout

Network Issue

API Unavailable
```

Retry helps.

---

### Poison Message

```text
Invalid Data

Missing Fields

Corrupt JSON
```

Retry never helps.

---

# 10.4 Message Validation

## Why Validate Messages?

Validate data before business processing.

This prevents:

```text
Bad Data

Wasted Retries

Unexpected Exceptions
```

---

## Example

Incoming Message

```json
{
  "OrderId": 1001,
  "Email": "user@test.com"
}
```

Validation:

```text
OrderId Exists

Email Exists

Format Correct
```

---

## Validation Example

```csharp
if(order.OrderId <= 0)
{
    throw new ValidationException();
}
```

---

## Recommended Validations

```text
Required Fields

Data Types

Email Format

Date Format

Business Rules
```

---

## Flow

```text
Message Arrives
      |
      v
Validate
      |
      +--- Invalid ---> DLQ
      |
      +--- Valid -----> Process
```

---

# 10.5 Circuit Breaker

## Problem

Consumer calls:

```text
Payment API
```

API is down.

Without Circuit Breaker:

```text
Call
Fail

Call
Fail

Call
Fail
```

Thousands of requests hit a failing service.

---

## What is Circuit Breaker?

Circuit Breaker stops requests temporarily when failures exceed a threshold.

Like an electrical circuit breaker.

---

# Circuit Breaker States

## Closed

Normal State.

```text
Requests Allowed
```

---

## Open

Too many failures.

```text
Requests Blocked
```

No calls made.

---

## Half Open

Testing recovery.

```text
Allow Few Requests
```

If successful:

```text
Closed
```

If failed:

```text
Open Again
```

---

## Flow

```text
Closed
   |
Failures
   |
   v
Open
   |
Wait
   |
   v
Half Open
```

---

## Polly Example

```csharp
var circuitBreaker = Policy
    .Handle<Exception>()
    .CircuitBreakerAsync(
        exceptionsAllowedBeforeBreaking: 5,
        durationOfBreak: TimeSpan.FromMinutes(1));
```

---

# Production Consumer Flow

```text
Kafka Message
      |
      v

Validate Message
      |
      v

Retry Policy
      |
      v

Circuit Breaker
      |
      v

Business Processing
      |
      +------ Success ------> Commit Offset
      |
      +------ Failed -------> DLQ
```

---

# Complete Architecture

```text
orders-topic
      |
      v
Consumer
      |
      v
Validation
      |
      v
Retry
      |
      v
Circuit Breaker
      |
      v
Business Logic
      |
      +---- Success ----> Commit Offset
      |
      +---- Failure ----> DLQ
```

---

# Interview Questions

## What is Retry Strategy?

Retrying processing after temporary failures.

---

## What is Exponential Backoff?

Increasing wait time between retries.

Example:

```text
1s

2s

4s

8s
```

---

## What is DLQ?

A Kafka topic that stores failed messages.

---

## What is a Poison Message?

A message that will never process successfully because the data is invalid.

---

## What is Message Validation?

Checking message correctness before processing.

---

## What is Circuit Breaker?

A pattern that temporarily blocks calls to failing services.

---

# Production Best Practices

Always:

```text
Validate Messages

Use Retry For Temporary Failures

Use Exponential Backoff

Use Circuit Breakers

Store Failed Messages In DLQ

Monitor DLQ Volume
```

Never:

```text
Infinite Retries

Ignore Poison Messages

Lose Failed Messages
```

---

# Key Takeaways

1. Retry handles temporary failures.
2. Exponential Backoff is the preferred retry strategy.
3. DLQ prevents message loss.
4. Poison Messages should be moved to DLQ.
5. Validation helps identify bad messages early.
6. Circuit Breaker protects downstream systems.
7. Polly is commonly used in .NET for Retry and Circuit Breaker.
8. A production Kafka consumer typically uses:

```text
Validation
    ↓
Retry
    ↓
Circuit Breaker
    ↓
Process
    ↓
Commit Offset
    ↓
DLQ (if failed)
```