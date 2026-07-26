# Module 35.6 – Exception Filters (`when`) in C#

> **Course:** ASP.NET Core – Complete Interview Roadmap
>
> **Module:** 35.6 – Exception Filters (`when`)
>
> **Level:** Intermediate → Advanced
>
> **Interview Importance:** ⭐⭐⭐⭐☆

---

# Table of Contents

1. Introduction
2. What are Exception Filters?
3. Why Do We Need Exception Filters?
4. Syntax
5. How Exception Filters Work
6. Basic Example
7. What Happens if the Condition is False?
8. Exception Filters with Custom Exceptions
9. `if` vs `when`
10. Multiple Exception Filters
11. Enterprise Example
12. Internal CLR Working
13. Best Practices
14. Common Mistakes
15. Interview Questions
16. Summary

---

# 1. Introduction

In C#, a `catch` block normally catches every exception of the specified type.

However, sometimes we only want to catch an exception when a particular condition is true.

C# provides **Exception Filters** using the **`when`** keyword for this purpose.

Exception Filters make exception handling cleaner, more readable, and more efficient.

---

# 2. What are Exception Filters?

An **Exception Filter** allows a catch block to execute only when a specified condition evaluates to **true**.

Syntax

```csharp
catch (Exception ex) when (condition)
{
    // Handle exception
}
```

The `when` keyword acts as a filter.

If the condition is true, the catch block executes.

If the condition is false, the CLR ignores that catch block and continues searching for another matching catch block.

---

# 3. Why Do We Need Exception Filters?

Suppose your application communicates with SQL Server.

Possible errors include:

- SQL Timeout
- Login Failed
- Deadlock
- Network Error

Without exception filters:

```csharp
try
{
    ProcessPayment();
}
catch(Exception ex)
{
    if(ex.Message.Contains("timeout"))
    {
        Console.WriteLine("Retry payment.");
    }
}
```

Problems:

- The exception is already caught.
- The catch block executes even if the condition is false.
- Additional logic is required inside the catch block.

Exception Filters solve this problem.

```csharp
try
{
    ProcessPayment();
}
catch(Exception ex) when (ex.Message.Contains("timeout"))
{
    Console.WriteLine("Retry payment.");
}
```

The CLR evaluates the condition before entering the catch block.

---

# 4. Syntax

General Syntax

```csharp
try
{
    // Risky Code
}
catch(Exception ex) when(condition)
{
    // Handle Exception
}
```

Example

```csharp
try
{
    throw new Exception("Database timeout.");
}
catch(Exception ex) when (ex.Message.Contains("timeout"))
{
    Console.WriteLine("Retry the operation.");
}
```

Output

```
Retry the operation.
```

---

# 5. How Exception Filters Work

Execution Flow

```
Exception Thrown

↓

Find Matching Catch Block

↓

Evaluate when Condition

        │
   ┌────┴────┐
   │         │
 True      False
   │         │
   ▼         ▼
Execute   Search Next
Catch      Catch Block
```

Notice that the CLR evaluates the condition **before** entering the catch block.

---

# 6. Basic Example

```csharp
try
{
    throw new Exception("Payment timeout.");
}
catch(Exception ex) when (ex.Message.Contains("timeout"))
{
    Console.WriteLine("Retry Payment");
}
```

Output

```
Retry Payment
```

The exception is handled because the filter evaluates to true.

---

# 7. What Happens if the Condition is False?

Example

```csharp
try
{
    throw new Exception("Login failed.");
}
catch(Exception ex) when (ex.Message.Contains("timeout"))
{
    Console.WriteLine("Retry");
}
catch(Exception)
{
    Console.WriteLine("General Error");
}
```

Output

```
General Error
```

Why?

The first filter returned **false**.

Therefore, the CLR skipped the first catch block and continued searching.

The second catch block handled the exception.

---

# 8. Exception Filters with Custom Exceptions

Suppose we have a custom exception.

```csharp
public class PaymentFailedException : Exception
{
    public string ErrorCode { get; }

    public PaymentFailedException(string errorCode, string message)
        : base(message)
    {
        ErrorCode = errorCode;
    }
}
```

Using Exception Filter

```csharp
try
{
    throw new PaymentFailedException(
        "TIMEOUT",
        "Payment gateway timeout.");
}
catch(PaymentFailedException ex)
    when (ex.ErrorCode == "TIMEOUT")
{
    Console.WriteLine("Retry Payment");
}
```

Output

```
Retry Payment
```

This is much cleaner than checking `ErrorCode` inside the catch block.

---

# 9. `if` vs `when`

## Using `if`

```csharp
catch(Exception ex)
{
    if(ex.Message.Contains("timeout"))
    {
        Console.WriteLine("Retry");
    }
}
```

Flow

```
Exception

↓

Catch Block Executes

↓

if Condition

↓

Handle
```

The exception is already caught.

---

## Using `when`

```csharp
catch(Exception ex)
    when(ex.Message.Contains("timeout"))
{
    Console.WriteLine("Retry");
}
```

Flow

```
Exception

↓

Evaluate Filter

↓

True?

↓

Catch Executes
```

The catch block executes only if the condition is true.

---

# Comparison

| `if` inside `catch` | `when` Exception Filter |
|----------------------|-------------------------|
| Exception already caught | Condition checked before catch |
| More code | Cleaner code |
| Cannot skip catch | CLR can skip catch |
| Less efficient | More efficient |

---

# 10. Multiple Exception Filters

```csharp
try
{
    throw new Exception("Network timeout.");
}
catch(Exception ex)
    when(ex.Message.Contains("timeout"))
{
    Console.WriteLine("Retry");
}
catch(Exception ex)
    when(ex.Message.Contains("network"))
{
    Console.WriteLine("Check Network");
}
catch(Exception)
{
    Console.WriteLine("Unknown Error");
}
```

The CLR evaluates each filter from top to bottom.

The first filter that evaluates to true is executed.

---

# 11. Enterprise Example

Suppose your application architecture is:

```
API

↓

Service

↓

Repository

↓

SQL Server
```

Repository throws

```
SqlException
```

Service

```csharp
catch(SqlException ex)
    when(ex.Number == -2)
{
    logger.LogWarning("SQL Timeout");

    throw;
}
```

Explanation

- SQL Error Number **-2** indicates a timeout.
- The timeout is logged.
- The exception is rethrown.
- Other SQL exceptions continue searching for another handler.

---

# 12. Internal CLR Working

Suppose an exception occurs.

```
Exception Created

↓

Stack Trace Captured

↓

Find Matching Catch

↓

Evaluate Filter

        │
   ┌────┴────┐
   │         │
 True      False
   │         │
   ▼         ▼
Execute   Search Next Catch
Catch
```

The CLR checks the filter **before** entering the catch block.

This is the biggest difference between `if` and `when`.

---

# 13. Best Practices

✔ Use Exception Filters for conditional exception handling.

✔ Keep filter conditions simple.

✔ Filter using properties such as:

- ErrorCode
- StatusCode
- SQL Error Number

instead of error messages whenever possible.

✔ Continue using

```csharp
throw;
```

to preserve the stack trace if you need to rethrow.

✔ Use Exception Filters to keep catch blocks clean.

---

# 14. Common Mistakes

## Using `if` Instead of `when`

Bad

```csharp
catch(Exception ex)
{
    if(ex.Message.Contains("timeout"))
    {
        Console.WriteLine("Retry");
    }
}
```

Better

```csharp
catch(Exception ex)
    when(ex.Message.Contains("timeout"))
{
    Console.WriteLine("Retry");
}
```

---

## Depending on Exception Messages

Bad

```csharp
when(ex.Message == "Database failed")
```

Messages may change or be localized.

Better

```csharp
when(ex.ErrorCode == "TIMEOUT")
```

or

```csharp
when(ex.Number == -2)
```

---

## Complex Filter Expressions

Avoid long-running methods inside filters.

Bad

```csharp
when(CheckDatabaseConnection(ex))
```

Filters should be fast and simple.

---

# 15. Interview Questions

## Q1. What is an Exception Filter?

An Exception Filter is a condition applied to a catch block using the `when` keyword. The catch block executes only if the condition evaluates to true.

---

## Q2. What is the advantage of `when`?

The CLR evaluates the condition before entering the catch block. If the condition is false, it continues searching for another matching catch block.

---

## Q3. What is the difference between `if` and `when`?

`if` executes after the exception has already been caught.

`when` evaluates before the catch block executes.

---

## Q4. When should Exception Filters be used?

- SQL timeout handling
- HTTP status code handling
- Retry logic
- Custom error codes
- Conditional logging

---

## Q5. Should you filter using exception messages?

No.

Prefer exception-specific properties such as:

- SQL Error Number
- ErrorCode
- StatusCode

Messages can change and may be localized.

---

# 16. Summary

In this module you learned:

- ✅ What Exception Filters are
- ✅ Why they are useful
- ✅ `when` syntax
- ✅ How the CLR evaluates Exception Filters
- ✅ Difference between `if` and `when`
- ✅ Using Exception Filters with Custom Exceptions
- ✅ Enterprise examples
- ✅ Best practices
- ✅ Common mistakes
- ✅ Interview questions

---

# Architecture Diagram

```
                Exception Thrown
                       │
                       ▼
           Find Matching Catch Block
                       │
                       ▼
          Evaluate Exception Filter
                       │
            ┌──────────┴──────────┐
            │                     │
         True                  False
            │                     │
            ▼                     ▼
      Execute Catch      Search Next Catch
            │                     │
            ▼                     ▼
      Continue Flow      If none found,
                         propagate exception
```

---

# Quick Revision

| Feature | `if` | `when` |
|----------|-------|---------|
| Evaluated before entering catch | ❌ No | ✅ Yes |
| Cleaner code | ❌ No | ✅ Yes |
| Can skip catch block | ❌ No | ✅ Yes |
| Better for conditional handling | ❌ No | ✅ Yes |
| Recommended | ⚠️ Sometimes | ✅ Yes |

---

# Next Module

## Module 35.7 – Global Exception Handling Middleware

Topics Covered:

- Why Global Exception Handling is Needed
- Local vs Global Exception Handling
- Creating Custom Exception Middleware
- Request Pipeline Flow
- Returning Standard API Responses
- ProblemDetails
- Logging Exceptions
- Production Best Practices
- Enterprise Architecture
- Senior-Level Interview Questions

> **Goal:** Learn how ASP.NET Core centrally handles unhandled exceptions, logs them, and returns consistent, secure, and production-ready API responses.