# Module 9.1 – Circuit Breaker Foundation

> **Course:** ASP.NET Core Microservices with .NET

**Module:** Circuit Breaker – Foundation

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. What is a Circuit Breaker?
3. Why Do We Need Circuit Breaker?
4. Problems Without Circuit Breaker
5. Real-Life Analogy
6. Software Example
7. Cascading Failure
8. How Circuit Breaker Solves the Problem
9. Retry vs Circuit Breaker
10. Should We Use Both?
11. Real-Time Examples
12. Where Do We Use Circuit Breakers?
13. Benefits
14. Interview Questions
15. Summary
16. Key Takeaways

---

# Learning Objectives

By the end of this module, you will understand:

- What is a Circuit Breaker?
- Why do we need a Circuit Breaker?
- What problems it solves?
- Why Retry alone is not enough?
- How Circuit Breaker protects Microservices
- Where Circuit Breakers are used in enterprise applications

---

# What is a Circuit Breaker?

A Circuit Breaker is a design pattern that protects an application from repeatedly calling a service that is already failing.

Instead of continuously sending requests to an unhealthy service, the Circuit Breaker temporarily blocks those requests until the service has had time to recover.

Think of it as a safety switch for software.

---

# Why Do We Need Circuit Breaker?

Imagine your application depends on another service.

```
Customer

↓

Order Service

↓

Payment Service

↓

Bank API
```

Everything works correctly until the Bank API becomes unavailable.

Without protection, every customer request still attempts to call the Bank API.

This causes

- Long waiting times
- Timeouts
- Thread exhaustion
- High CPU usage
- Cascading failures

Eventually, your own application becomes slow even though the problem is actually in the Bank API.

---

# Problems Without Circuit Breaker

Suppose 1,000 users place orders.

Every request performs

```
Order Service

↓

Payment Service

↓

Bank API
```

Now the Bank API is down.

```
Request 1

↓

Timeout
```

```
Request 2

↓

Timeout
```

```
Request 3

↓

Timeout
```

```
Request 1000

↓

Timeout
```

Every request waits until it times out.

Resources are wasted while waiting.

---

# Real-Life Analogy – Electrical Circuit Breaker

Every house has a circuit breaker.

```
Power Supply

↓

Circuit Breaker

↓

Lights

↓

Fan

↓

Television
```

Suppose a short circuit occurs.

Without a breaker

```
Electric Current

↓

Short Circuit

↓

Wire Burns

↓

Fire
```

With a breaker

```
Short Circuit

↓

Circuit Breaker Trips

↓

Power Stops

↓

House Protected
```

A software Circuit Breaker works exactly the same way.

It protects your application.

---

# Software Example

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

The Bank API suddenly becomes unavailable.

Without a Circuit Breaker

```
Customer Request

↓

Payment Service

↓

Bank API

↓

Timeout

↓

Customer Waits
```

The same thing happens for every request.

---

# Cascading Failure

One failing service can cause multiple services to fail.

```
Bank API Down

↓

Payment Service Waiting

↓

Order Service Waiting

↓

API Gateway Waiting

↓

Customer Timeout
```

This chain reaction is called a

```
Cascading Failure
```

Circuit Breakers prevent this from happening.

---

# How Circuit Breaker Solves the Problem

Instead of calling the failing service forever,

the Circuit Breaker monitors failures.

Suppose the first five requests fail.

```
Request 1

↓

Failed
```

```
Request 2

↓

Failed
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

The Circuit Breaker decides

```
Stop Calling Bank API
```

Now every new request immediately receives a response such as

```
Payment Service is temporarily unavailable.
Please try again later.
```

No unnecessary calls are made.

---

# Request Flow

Without Circuit Breaker

```
Customer

↓

Payment Service

↓

Bank API

↓

Timeout
```

Every request waits.

---

With Circuit Breaker

```
Customer

↓

Payment Service

↓

Circuit Breaker

↓

Bank API

Blocked
```

The customer receives a response immediately.

---

# Why is This Better?

Without Circuit Breaker

```
1000 Requests

↓

1000 Bank Calls

↓

1000 Timeouts

↓

Application Slows Down
```

With Circuit Breaker

```
5 Failed Calls

↓

Circuit Opens

↓

Remaining Requests Blocked

↓

Application Remains Healthy
```

The failing service is protected and your application remains responsive.

---

# Retry vs Circuit Breaker

Many beginners confuse Retry with Circuit Breaker.

They solve different problems.

---

## Retry

Retry assumes the failure is temporary.

```
Call

↓

Failed

↓

Retry

↓

Failed

↓

Retry

↓

Success
```

Retry is useful for

- Temporary network failures
- Short database connection issues
- Small packet loss

---

## Circuit Breaker

Circuit Breaker assumes the service is unhealthy.

```
Call

↓

Failed

↓

Failed

↓

Failed

↓

Open Circuit

↓

Stop Calling
```

Circuit Breaker is useful for

- Service outages
- Database unavailable
- Payment Gateway down
- External REST API failures

---

# Should We Use Both?

Yes.

Most enterprise applications combine them.

```
Call Service

↓

Retry (2 or 3 times)

↓

Still Failed?

↓

Open Circuit

↓

Stop Sending Requests
```

Retry handles temporary failures.

Circuit Breaker handles long-running failures.

---

# Real-Time Example – Amazon

Suppose Amazon receives

```
10,000 Orders Per Minute
```

Payment Service calls Visa.

```
Amazon

↓

Payment Service

↓

Visa API
```

Visa suddenly becomes unavailable.

Without Circuit Breaker

```
10,000 Requests

↓

Visa API

↓

Timeout
```

Payment Service becomes overloaded.

---

With Circuit Breaker

```
First Few Failures

↓

Circuit Opens

↓

No More Calls

↓

Immediate Response
```

Amazon protects itself while Visa recovers.

---

# Real-Time Example – ATM

Imagine an ATM connected to the bank.

Bank Server

↓

Unavailable

Without Circuit Breaker

```
Insert Card

↓

Waiting...

↓

Waiting...

↓

Timeout
```

With Circuit Breaker

```
Insert Card

↓

Service Unavailable

↓

Please Try Again Later
```

The customer receives a quick response.

---

# Where Do We Use Circuit Breakers?

Circuit Breakers are commonly used when communicating with

- Payment Gateways
- Banking APIs
- SMS Services
- Email Providers
- Third-Party REST APIs
- Microservices
- Databases
- Cloud Services

Any dependency that can become unavailable is a candidate for a Circuit Breaker.

---

# Benefits

✔ Prevents Cascading Failures

✔ Reduces Timeouts

✔ Improves Application Stability

✔ Protects External Services

✔ Better User Experience

✔ Faster Failure Detection

✔ Higher Availability

---

# Interview Questions

## What is a Circuit Breaker?

A Circuit Breaker is a design pattern that prevents an application from repeatedly calling an unhealthy service.

---

## Why do we need a Circuit Breaker?

To protect our application from cascading failures, excessive timeouts, and resource exhaustion when a dependent service becomes unavailable.

---

## Does Circuit Breaker replace Retry?

No.

Retry is used for temporary failures.

Circuit Breaker is used for persistent failures.

They are commonly used together.

---

## Does Circuit Breaker repair the failing service?

No.

It only protects the calling application while the failing service recovers.

---

## Where is Circuit Breaker commonly used?

- Microservice communication
- External REST APIs
- Payment gateways
- Banking systems
- Third-party integrations

---

# Summary

A Circuit Breaker is a resilience pattern that prevents applications from continuously calling services that are already failing.

Instead of allowing every request to wait for a timeout, the Circuit Breaker detects repeated failures and temporarily blocks further requests.

This protects the application, reduces resource consumption, and prevents cascading failures.

Circuit Breakers are commonly used together with Retry policies in enterprise microservices built using ASP.NET Core.

---

# Key Takeaways

✔ Circuit Breaker protects applications from failing dependencies.

✔ It prevents repeated requests to unhealthy services.

✔ It reduces unnecessary resource consumption.

✔ It prevents cascading failures across microservices.

✔ Retry handles temporary failures.

✔ Circuit Breaker handles long-running failures.

✔ Enterprise applications commonly use Retry + Circuit Breaker together.

---

# Next Module

## Module 9.2 – Internal Working of Circuit Breaker

In the next module, you will learn:

- Closed State
- Open State
- Half-Open State
- State Transitions
- Failure Threshold
- Recovery Timeout
- Request Flow
- Internal Algorithm
- Complete .NET Polly implementation