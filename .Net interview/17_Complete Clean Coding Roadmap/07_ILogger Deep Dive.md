# Logging in ASP.NET Core

# README 2 – ILogger Deep Dive

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is ILogger?
2. Why ILogger?
3. How ILogger Works Internally
4. Logging Architecture
5. Dependency Injection
6. ILogger<T>
7. Log Levels
8. Structured Logging
9. Event IDs
10. Logging Scopes
11. Exception Logging
12. Performance Best Practices
13. Common Mistakes
14. Enterprise Example
15. Code Review Checklist
16. Common Interview Questions
17. Key Takeaways

---

# 1. What is ILogger?

`ILogger` is the **built-in logging abstraction** provided by ASP.NET Core.

Notice the word **abstraction**.

Your application writes logs through `ILogger`.

`ILogger` decides **how** to send those logs to the configured logging providers.

Example

```text
OrderService

↓

ILogger

↓

Console

File

Seq

Azure Application Insights

Elastic Stack
```

Your code never knows where logs are stored.

---

# 2. Why ILogger?

Without ILogger

```csharp
Console.WriteLine("Order Created");
```

Problems

* Console only
* Cannot switch providers
* Not testable
* No log levels
* No structured logging

---

With ILogger

```csharp
_logger.LogInformation(
    "Order created.");
```

Tomorrow you decide to use

* Serilog
* Azure Application Insights
* Seq

No business code changes.

---

# 3. How ILogger Works Internally

Imagine

```csharp
_logger.LogInformation("Order Created");
```

Internally

```text
OrderService

↓

ILogger

↓

LoggerFactory

↓

ConsoleLoggerProvider

↓

Console
```

Or

```text
OrderService

↓

ILogger

↓

LoggerFactory

↓

Serilog Provider

↓

File

↓

Seq
```

The provider decides where the log goes.

---

# 4. Logging Architecture

```text
Controller

↓

Service

↓

Repository

↓

ILogger<T>

↓

LoggerFactory

↓

Logging Provider

↓

Console

↓

File

↓

Azure Monitor

↓

Seq
```

Every layer can log.

---

# 5. Dependency Injection

Register nothing manually.

ASP.NET Core automatically provides `ILogger<T>`.

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
}
```

The DI container creates the logger automatically.

---

# 6. ILogger<T>

Example

```csharp
private readonly ILogger<OrderService> _logger;
```

Question

Why

```text
<OrderService>
```

Because the category becomes

```text
MyApplication.Services.OrderService
```

When reading production logs you immediately know which class generated the log.

---

# 7. Log Levels

ASP.NET Core provides six log levels.

```text
Trace

↓

Debug

↓

Information

↓

Warning

↓

Error

↓

Critical
```

---

## Trace

Very detailed diagnostics.

```csharp
_logger.LogTrace(
    "Entering CalculateTax()");
```

Used rarely.

---

## Debug

Developer information.

```csharp
_logger.LogDebug(
    "Discount calculated.");
```

Usually disabled in production.

---

## Information

Business events.

```csharp
_logger.LogInformation(
    "Order created.");
```

Examples

* User Logged In
* Invoice Generated
* Payment Started

---

## Warning

Unexpected but recoverable.

```csharp
_logger.LogWarning(
    "Retrying payment.");
```

Examples

* Cache Miss
* Retry
* Slow API

---

## Error

Operation failed.

```csharp
_logger.LogError(
    "Payment failed.");
```

Examples

* Database timeout
* External API failure
* Email sending failed

---

## Critical

Application cannot continue.

```csharp
_logger.LogCritical(
    "Database unavailable.");
```

Examples

* Startup failure
* Configuration corruption
* Disk full

---

# 8. Structured Logging

❌ Bad

```csharp
_logger.LogInformation(
    "Order " + order.Id + " created.");
```

Problems

* String concatenation
* Not searchable

---

✅ Good

```csharp
_logger.LogInformation(
    "Order created. OrderId: {OrderId}",
    order.Id);
```

Stored internally

```json
{
  "Message":"Order created.",
  "OrderId":101
}
```

Now you can search

```text
OrderId = 101
```

Very important in production.

---

# 9. Event IDs

Useful for grouping similar logs.

```csharp
private static readonly EventId OrderCreated =
    new(1001, "OrderCreated");
```

Usage

```csharp
_logger.LogInformation(
    OrderCreated,
    "Order created.");
```

Benefits

* Easier filtering
* Better dashboards
* Consistent event tracking

---

# 10. Logging Scopes

Suppose one HTTP request generates 50 logs.

How do we know they belong together?

Use scopes.

```csharp
using(_logger.BeginScope(
    "OrderId:{OrderId}",
    order.Id))
{
    _logger.LogInformation(
        "Payment started");

    _logger.LogInformation(
        "Inventory updated");
}
```

Every log inside the scope includes

```text
OrderId = 1001
```

Extremely useful in production.

---

# 11. Exception Logging

Wrong

```csharp
catch(Exception)
{
}
```

Exception lost.

---

Wrong

```csharp
catch(Exception ex)
{
    Console.WriteLine(ex);
}
```

Console is not production logging.

---

Correct

```csharp
catch(Exception ex)
{
    _logger.LogError(
        ex,
        "Payment failed. OrderId: {OrderId}",
        order.Id);

    throw;
}
```

Notice

Pass the exception object as the first argument.

This preserves:

* Message
* Stack Trace
* Inner Exception

---

# 12. Performance Best Practices

## Don't Log Everything

Bad

```csharp
foreach(var product in products)
{
    _logger.LogInformation(
        "Product {Id}", product.Id);
}
```

10,000 products

↓

10,000 log entries.

Expensive.

---

## Avoid Expensive String Creation

Bad

```csharp
_logger.LogInformation(
    $"Order {order.Id} created.");
```

Even if Information logging is disabled, the string may still be formatted.

Better

```csharp
_logger.LogInformation(
    "Order {OrderId} created.",
    order.Id);
```

Structured logging defers formatting until needed.

---

# 13. Common Mistakes

❌ Logging passwords.

❌ Logging JWT tokens.

❌ Logging API keys.

❌ Using `Console.WriteLine()`.

❌ Wrong log levels.

❌ Logging the same exception multiple times.

❌ Swallowing exceptions.

---

# 14. Enterprise Example

Order API

```text
POST /orders

↓

Information

"Order Created"

↓

Information

"Inventory Reserved"

↓

Information

"Payment Started"

↓

Error

"Payment Gateway Timeout"

↓

Warning

"Retry Payment"

↓

Information

"Payment Successful"
```

Support engineers can reconstruct the entire request.

---

# 15. Code Review Checklist

Before approving code ask:

* Is the correct log level used?
* Is structured logging used?
* Are secrets excluded?
* Is context included (OrderId, UserId)?
* Is the exception object passed to `LogError`?
* Are duplicate logs avoided?
* Are logs meaningful?

---

# 16. Common Interview Questions

### Why use ILogger instead of Console.WriteLine?

Because ILogger is an abstraction that supports:

* Multiple providers
* Dependency Injection
* Structured logging
* Filtering
* Log levels

---

### Why use ILogger<T>?

The generic type automatically sets the logging category to the class name, making logs easier to trace.

---

### Why use structured logging?

Structured logging stores values as searchable fields rather than plain text.

---

### Why pass the exception object to LogError?

It preserves the stack trace and inner exception information.

---

### Why use BeginScope()?

To attach common context (such as OrderId, UserId, CorrelationId) to all logs generated during a logical operation.

---

# 17. Key Takeaways

* `ILogger` is ASP.NET Core's logging abstraction.
* `ILogger<T>` automatically categorizes logs by class.
* Use the correct log level.
* Prefer structured logging over string concatenation.
* Use `BeginScope()` to group related logs.
* Always pass the exception object to `LogError`.
* Never log passwords, tokens, or secrets.
* Keep log messages meaningful and searchable.

---

# Next Module

## README 3 – Serilog (Production Logging)

Topics include:

* Why Serilog?
* Installing Serilog
* Configuring Serilog
* Console Logging
* File Logging
* Rolling File Logs
* JSON Logging
* Enrichers
* Filtering
* Seq Integration
* Production Configuration
* Best Practices
* Real Enterprise Examples

This module explains how most enterprise ASP.NET Core applications implement production-grade logging beyond the default providers.
