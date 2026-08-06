# Logging in ASP.NET Core

# README 1 – Logging Fundamentals

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is Logging?
2. Why Logging is Important
3. Logging vs Debugging
4. Logging vs Monitoring
5. Logging vs Tracing
6. Logging Architecture
7. Types of Logs
8. Logging Levels
9. ASP.NET Core Logging Pipeline
10. ILogger
11. Logging Providers
12. Logging Best Practices
13. What Should Be Logged?
14. What Should NOT Be Logged?
15. Enterprise Logging Flow
16. Common Logging Mistakes
17. Code Review Checklist
18. Interview Questions
19. Key Takeaways
20. Next Module

---

# 1. What is Logging?

Logging is the process of recording information about what happens inside an application while it is running.

Logs help developers understand:

* What happened
* When it happened
* Where it happened
* Why it happened (if enough context is logged)

Think of logs as the **black box of an aircraft**. When something goes wrong, logs help reconstruct the sequence of events.

---

# 2. Why Logging is Important

Without logging

```text
Customer

↓

Clicks "Place Order"

↓

500 Internal Server Error

↓

No Logs

↓

Nobody Knows Why
```

With logging

```text
Customer

↓

Place Order

↓

Order Created

↓

Payment Started

↓

Payment Failed

↓

Stripe Timeout

↓

Error Logged

↓

Developer Fixes Issue
```

Logging reduces debugging time dramatically.

---

# 3. Logging vs Debugging

| Logging               | Debugging                 |
| --------------------- | ------------------------- |
| Works in Production   | Usually local development |
| Continuous            | Temporary                 |
| Historical data       | Current execution         |
| Used after deployment | Used during development   |

Debugging helps you while writing code.

Logging helps after the application is deployed.

---

# 4. Logging vs Monitoring

Logging

```text
Application

↓

Stores Events

↓

Log Files / Database / Cloud
```

Monitoring

```text
CPU Usage

Memory

Disk

Availability

Response Time

Alerts
```

Logs explain **what happened**.

Monitoring tells you **how the system is performing**.

---

# 5. Logging vs Tracing

Logging

```text
Payment Failed
```

Tracing

```text
Request Received

↓

Authentication

↓

Order Service

↓

Inventory Service

↓

Payment Service

↓

Shipping Service

↓

Response
```

Tracing follows an entire request across services.

Logging records individual events.

---

# 6. Logging Architecture

```text
Client

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

ILogger

↓

Logging Provider

↓

Console

File

Seq

Application Insights

Elastic Stack
```

Every layer can write logs through `ILogger`.

---

# 7. Types of Logs

## Business Logs

Examples

```text
Order Created

Customer Registered

Invoice Generated
```

---

## Audit Logs

Track who performed an action.

Example

```text
User

↓

Deleted Customer

↓

Time

↓

IP Address
```

Often required for compliance.

---

## Security Logs

Examples

```text
Login Failed

Password Changed

Unauthorized Access

Account Locked
```

---

## Application Logs

Examples

```text
Order Created

Payment Started

Inventory Updated
```

---

## Infrastructure Logs

Examples

```text
Database Connection Failed

Redis Timeout

Kafka Connection Lost

Disk Full
```

---

# 8. Logging Levels

ASP.NET Core provides six log levels.

## Trace

Very detailed diagnostic information.

Used only during troubleshooting.

---

## Debug

Developer-focused information.

Not usually enabled in production.

---

## Information

Normal application events.

Examples

```text
Order Created

Customer Logged In

Invoice Generated
```

Most business events use Information.

---

## Warning

Something unexpected happened but the application continues.

Examples

```text
Retrying Payment

Cache Miss

Slow Database Query
```

---

## Error

An operation failed.

Examples

```text
Payment Failed

Database Timeout

Email Sending Failed
```

---

## Critical

Application cannot continue safely.

Examples

```text
Database Unavailable

Application Startup Failed

Disk Full
```

---

# 9. ASP.NET Core Logging Pipeline

```text
Controller

↓

ILogger

↓

Logging Provider

↓

Console

↓

File

↓

Azure Application Insights

↓

Seq

↓

Elastic Stack
```

Application code only depends on `ILogger`.

Providers decide where logs are stored.

---

# 10. ILogger

Example

```csharp
public class OrderService
{
    private readonly ILogger<OrderService> _logger;

    public OrderService(
        ILogger<OrderService> logger)
    {
        _logger = logger;
    }

    public void CreateOrder()
    {
        _logger.LogInformation(
            "Creating order");

        _logger.LogInformation(
            "Order created successfully");
    }
}
```

Benefits

* Built into ASP.NET Core
* Supports Dependency Injection
* Supports multiple providers
* Supports structured logging

---

# 11. Logging Providers

ASP.NET Core supports multiple providers.

```text
ILogger

↓

Console

↓

Debug Window

↓

Event Viewer

↓

File (Serilog)

↓

Seq

↓

Application Insights

↓

Elastic Stack
```

Changing providers does not require changing business code.

---

# 12. Logging Best Practices

✔ Log meaningful business events.

✔ Include identifiers.

```text
OrderId

CustomerId

RequestId
```

✔ Use structured logging.

✔ Log exceptions.

✔ Log startup and shutdown.

✔ Log external service failures.

✔ Keep log messages clear.

---

# 13. What Should Be Logged?

Good candidates

* User Login
* User Logout
* Order Created
* Payment Started
* Payment Failed
* Database Timeout
* External API Failure
* Background Job Started
* Background Job Completed
* Authentication Failure

---

# 14. What Should NOT Be Logged?

Never log

* Passwords
* JWT Tokens
* Credit Card Numbers
* CVV
* API Secrets
* Database Passwords
* Connection Strings
* Personal Identifiable Information (PII) unless required and protected

Bad

```text
Password = MyPassword123
```

Good

```text
User login failed.

UserId = 102
```

---

# 15. Enterprise Logging Flow

```text
Client

↓

Order API

↓

Authentication

↓

Order Created

↓

Inventory Reserved

↓

Payment Started

↓

Payment Failed

↓

Exception Logged

↓

Error Response

↓

Logs Sent

↓

Seq

↓

Application Insights

↓

Alert
```

This allows production support teams to diagnose issues quickly.

---

# 16. Common Logging Mistakes

❌ Logging every line of code.

❌ Using the wrong log level.

❌ Logging the same error multiple times.

❌ Logging sensitive information.

❌ Writing unreadable messages.

❌ Using string concatenation instead of structured logging.

Bad

```csharp
_logger.LogInformation(
    "Order Id : " + order.Id);
```

Better

```csharp
_logger.LogInformation(
    "Order created. OrderId: {OrderId}",
    order.Id);
```

Structured logging allows log systems to index and search `OrderId` as a separate field.

---

# 17. Code Review Checklist

Before approving code ask:

* Is the correct log level used?
* Is sensitive data avoided?
* Are important business events logged?
* Are exceptions logged with context?
* Is structured logging used?
* Are duplicate logs avoided?
* Is the message understandable?
* Can production support diagnose the issue from this log?

---

# 18. Common Interview Questions

### What is logging?

Logging is the process of recording application events to help diagnose issues, monitor behavior, and support production systems.

---

### Why use ILogger?

* Built into ASP.NET Core
* Supports Dependency Injection
* Supports multiple providers
* Easy to test
* Supports structured logging

---

### Difference between Debug and Information?

Debug

* Developer diagnostics
* Usually disabled in production

Information

* Normal business events
* Commonly enabled in production

---

### What should never be logged?

* Passwords
* JWT Tokens
* API Keys
* Connection Strings
* Credit Card Details
* Sensitive personal information

---

### Why use structured logging?

Structured logging stores data as searchable fields instead of plain text, making production troubleshooting much easier.

---

# 19. Key Takeaways

* Logging is essential for production support.
* Use `ILogger` instead of custom logging code.
* Choose the correct log level.
* Prefer structured logging over string concatenation.
* Never log secrets or sensitive information.
* Log meaningful business events with enough context.
* Use centralized logging platforms in production.

---

# 20. Next Module

## README 2 – ILogger Deep Dive

Topics include:

* `ILogger<T>`
* Dependency Injection
* `LogTrace`
* `LogDebug`
* `LogInformation`
* `LogWarning`
* `LogError`
* `LogCritical`
* Event IDs
* Logging Scopes
* Structured Logging
* Performance Considerations
* Real Production Examples
* Common Interview Questions

This module includes complete code examples and explains how `ILogger` works internally in ASP.NET Core.
