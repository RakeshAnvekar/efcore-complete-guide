# Module 9.2 – Internal Working of Circuit Breaker

> **Course:** ASP.NET Core Microservices with .NET

**Module:** Internal Working of Circuit Breaker

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. Circuit Breaker States
3. Closed State
4. Failure Threshold
5. Open State
6. Recovery Timeout
7. Half-Open State
8. State Transition Flow
9. Internal Algorithm
10. Real-Time Example
11. Enterprise Implementation
12. Best Practices
13. Interview Questions
14. Summary
15. Key Takeaways

---

# Learning Objectives

By the end of this module, you will understand

- How Circuit Breaker works internally
- The three Circuit Breaker states
- State transitions
- Failure Threshold
- Recovery Timeout
- Half-Open testing
- Enterprise request flow
- Interview concepts

---

# How Does Circuit Breaker Work?

A Circuit Breaker is a state machine.

It constantly monitors whether a service is healthy or unhealthy.

It can be in one of three states.

```
             +----------------+
             |                |
             |    CLOSED      |
             |                |
             +-------+--------+
                     |
        Too many failures
                     |
                     ▼
             +----------------+
             |                |
             |      OPEN      |
             |                |
             +-------+--------+
                     |
           Recovery Timeout
                     |
                     ▼
             +----------------+
             |                |
             |   HALF-OPEN    |
             |                |
             +-------+--------+
                     |
          +----------+----------+
          |                     |
      Success               Failure
          |                     |
          ▼                     ▼
      CLOSED                OPEN
```

---

# State 1 – CLOSED

The Closed state is the normal operating state.

The target service is considered healthy.

Every request is allowed to pass.

```
Customer

↓

Order Service

↓

Payment Service

↓

Bank API

↓

Success
```

The Circuit Breaker simply forwards the request.

---

# Request Flow in Closed State

```
Incoming Request

↓

Circuit Breaker

↓

Closed

↓

Call Service

↓

Return Response
```

No requests are blocked.

---

# Example

Suppose 10 customers place orders.

```
Request 1

↓

Success
```

```
Request 2

↓

Success
```

```
Request 3

↓

Success
```

Everything works correctly.

The Circuit remains

```
CLOSED
```

---

# What Happens When One Request Fails?

Suppose one request times out.

```
Request 7

↓

Timeout
```

Does the Circuit immediately open?

No.

A single failure does not mean the service is unhealthy.

Possible reasons include

- Temporary network issue
- Short latency spike
- Temporary overload

The Circuit remains

```
CLOSED
```

---

# Failure Threshold

The Circuit Breaker opens only after a configured number of failures.

Example

```
Failure Threshold = 5
```

Meaning

```
If 5 consecutive requests fail

↓

Open the Circuit
```

---

# Example

```
Request 1

↓

Success
```

```
Request 2

↓

Success
```

```
Request 3

↓

Failed
```

```
Request 4

↓

Failed
```

```
Request 5

↓

Failed
```

```
Request 6

↓

Failed
```

```
Request 7

↓

Failed
```

Failure Count

```
5
```

Circuit changes to

```
OPEN
```

---

# State 2 – OPEN

The Open state protects the application.

No requests are sent to the target service.

```
Incoming Request

↓

Circuit Breaker

↓

OPEN

↓

Reject Request
```

The service is not called.

---

# Why is Open State Important?

Suppose the Bank API is unavailable.

Without Circuit Breaker

```
1000 Requests

↓

1000 HTTP Calls

↓

1000 Timeouts
```

Resources are wasted.

With Circuit Breaker

```
1000 Requests

↓

Circuit Breaker

↓

Rejected Immediately
```

Response time becomes milliseconds instead of waiting for long timeouts.

---

# Response During Open State

The application usually returns

```
503 Service Unavailable
```

or

```
Payment Service is temporarily unavailable.
Please try again later.
```

This is much faster than waiting for a timeout.

---

# Recovery Timeout

The Circuit cannot stay Open forever.

After a configured period,

it checks whether the service has recovered.

Example

```
Recovery Timeout = 30 Seconds
```

Flow

```
Circuit Opens

↓

Wait 30 Seconds

↓

Move to HALF-OPEN
```

---

# State 3 – HALF-OPEN

Half-Open is the testing state.

The Circuit allows only a few requests.

Example

```
100 Customers

↓

Only 1 Test Request
```

If the test succeeds,

the service is considered healthy.

If the test fails,

the service is still unhealthy.

---

# Success Scenario

```
Circuit

↓

HALF-OPEN

↓

Allow Test Request

↓

Bank API

↓

Success

↓

Circuit

↓

CLOSED
```

All requests are allowed again.

---

# Failure Scenario

```
Circuit

↓

HALF-OPEN

↓

Allow Test Request

↓

Bank API

↓

Timeout

↓

Circuit

↓

OPEN
```

The Circuit waits for another Recovery Timeout before testing again.

---

# Complete State Transition

```
Application Starts

↓

Circuit = CLOSED

↓

Requests Allowed

↓

Failures Increase

↓

Failure Threshold Reached

↓

Circuit = OPEN

↓

Reject Requests

↓

Wait Recovery Timeout

↓

Circuit = HALF-OPEN

↓

Allow Test Request

↓

Success?

↓

Yes

↓

Circuit = CLOSED

OR

↓

Failure?

↓

Circuit = OPEN
```

---

# Internal Algorithm

```
Request Received

↓

Is Circuit Closed?

↓

Yes

↓

Call Service

↓

Success?

↓

Return Response

↓

Failure?

↓

Increase Failure Count

↓

Failure Threshold Reached?

↓

No

↓

Remain Closed

↓

Yes

↓

Open Circuit

↓

Wait Recovery Timeout

↓

Half-Open

↓

Allow Test Request

↓

Success?

↓

Close Circuit

↓

Failure?

↓

Open Circuit Again
```

---

# Real-Time Example

Suppose

```
Customer

↓

Order Service

↓

Payment Service

↓

Bank API
```

Initially

```
Bank API

Healthy
```

Circuit

```
CLOSED
```

---

Suddenly

Bank API crashes.

Requests

```
1

↓

Timeout
```

```
2

↓

Timeout
```

```
3

↓

Timeout
```

```
4

↓

Timeout
```

```
5

↓

Timeout
```

Failure Threshold reached.

Circuit changes to

```
OPEN
```

---

Now

Customer places another order.

Instead of calling Bank API,

Circuit immediately returns

```
Payment Service Unavailable
```

No unnecessary HTTP calls are made.

---

Thirty seconds later

Circuit changes to

```
HALF-OPEN
```

One request is allowed.

If it succeeds,

```
Circuit

↓

CLOSED
```

Otherwise,

```
Circuit

↓

OPEN
```

---

# Enterprise Example

Most .NET applications use **Polly** to implement Circuit Breakers.

Typical configuration

```
Failure Threshold

↓

5 Failures
```

```
Recovery Timeout

↓

30 Seconds
```

```
Half-Open

↓

Allow Limited Trial Requests
```

These values are configurable depending on business requirements.

---

# Best Practices

✔ Combine Retry and Circuit Breaker

✔ Keep Recovery Timeout configurable

✔ Log every state transition

✔ Monitor Open Circuit events

✔ Use different Circuit Breakers for different external services

✔ Return meaningful fallback responses

---

# Interview Questions

## What are the three states of a Circuit Breaker?

- Closed
- Open
- Half-Open

---

## What happens in Closed state?

All requests are forwarded to the target service because it is considered healthy.

---

## What happens in Open state?

Requests are immediately rejected without calling the target service.

---

## What happens in Half-Open state?

Only a small number of requests are allowed to test whether the service has recovered.

---

## What is Failure Threshold?

The configured number of failures required before opening the Circuit.

---

## What is Recovery Timeout?

The waiting period before moving from Open to Half-Open.

---

## Why not move directly from Open to Closed?

Because the service may still be unavailable.

Half-Open safely verifies whether the service has recovered.

---

## Does Circuit Breaker prevent failures?

No.

It prevents repeated calls to an already failing service, protecting the calling application.

---

# Summary

Circuit Breaker works as a finite state machine with three states: Closed, Open, and Half-Open.

Initially, all requests are allowed.

When failures exceed a configured threshold, the Circuit opens and blocks further requests.

After the recovery timeout, the Circuit enters the Half-Open state and allows a limited number of test requests.

If the service responds successfully, the Circuit closes and normal operation resumes.

If the service is still unhealthy, the Circuit returns to the Open state.

---

# Key Takeaways

✔ Circuit Breaker has three states: Closed, Open, and Half-Open.

✔ Closed allows all requests.

✔ Open blocks all requests.

✔ Half-Open tests whether the service has recovered.

✔ Failure Threshold determines when the Circuit opens.

✔ Recovery Timeout determines when the Circuit enters Half-Open.

✔ Circuit Breaker improves resilience and prevents cascading failures.

---

# Next Module

## Module 9.3 – Circuit Breaker in ASP.NET Core using Polly

In the next module, you will learn

- Polly
- IHttpClientFactory
- Circuit Breaker Policy
- Retry Policy
- Timeout Policy
- Fallback Policy
- Complete ASP.NET Core implementation
- Real-world enterprise example