# Module 35.3 – `throw` vs `throw ex` in C#

> **Course:** ASP.NET Core – Complete Interview Roadmap
>
> **Module:** 35.3 – `throw` vs `throw ex`
>
> **Level:** Intermediate → Advanced
>
> **Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Introduction
2. What is a Stack Trace?
3. What is `throw`?
4. What is `throw ex`?
5. Internal CLR Working
6. Visual Comparison
7. Real-World Example
8. Preserving Stack Trace
9. Wrapping Exceptions
10. Best Practices
11. Common Mistakes
12. Interview Questions
13. Summary

---

# 1. Introduction

One of the most common interview questions in C# is:

> **What is the difference between `throw` and `throw ex`?**

Many developers think both statements are identical because they both rethrow an exception.

However, internally they behave very differently.

The major difference is **Stack Trace Preservation**.

Understanding this difference is extremely important for debugging production applications.

---

# 2. What is a Stack Trace?

Whenever an exception occurs, the CLR records every method that was executed before the exception happened.

Example

```
Main()

↓

Controller()

↓

Service()

↓

Repository()

↓

Exception
```

This sequence of method calls is called the **Stack Trace**.

The stack trace tells us exactly where the exception originated.

---

## Example

```csharp
public void Repository()
{
    throw new Exception("Database failed.");
}

public void Service()
{
    Repository();
}

public void Controller()
{
    Service();
}

public static void Main()
{
    Controller();
}
```

Stack Trace

```
Repository()

↓

Service()

↓

Controller()

↓

Main()
```

This clearly shows that the exception started in `Repository()`.

---

# 3. What is `throw`?

`throw` rethrows the **current exception** while preserving its original stack trace.

Example

```csharp
public void Service()
{
    try
    {
        Repository();
    }
    catch(Exception)
    {
        throw;
    }
}
```

Notice:

```csharp
throw;
```

No exception variable is specified.

The CLR understands:

> Continue throwing the same exception object.

---

## Execution Flow

```
Repository()

↓

Exception Thrown

↓

Service catches Exception

↓

throw;

↓

Controller

↓

Main
```

The original exception is preserved.

---

## Stack Trace

```
Repository()

↓

Service()

↓

Controller()

↓

Main()
```

The exception still appears to have originated in `Repository()`.

This is exactly what we want while debugging.

---

# 4. What is `throw ex`?

Now consider the following code.

```csharp
public void Service()
{
    try
    {
        Repository();
    }
    catch(Exception ex)
    {
        throw ex;
    }
}
```

This looks similar but behaves differently.

When using

```csharp
throw ex;
```

the CLR treats it as a **new throw operation from the current location**.

The stack trace starts from the current catch block.

---

## Execution Flow

```
Repository()

↓

Exception

↓

Service catches

↓

throw ex;

↓

Controller

↓

Main
```

---

## Stack Trace

```
Service()

↓

Controller()

↓

Main()
```

The original `Repository()` location is lost.

---

# 5. Internal CLR Working

## Using `throw`

```
Exception Created

↓

Stack Trace Captured

↓

Catch Block

↓

throw;

↓

Reuse Existing Exception

↓

Original Stack Trace Preserved
```

---

## Using `throw ex`

```
Exception Created

↓

Catch Block

↓

throw ex;

↓

Exception Re-thrown

↓

Stack Trace Starts Here
```

The original location where the exception occurred is no longer the starting point of the stack trace.

---

# 6. Visual Comparison

## Using `throw`

```
Repository()

↓

Service()

↓

Controller()

↓

Main()
```

Original exception location preserved.

---

## Using `throw ex`

```
Service()

↓

Controller()

↓

Main()
```

Original exception location lost.

---

# 7. Real-World Example

Imagine an e-commerce application.

```
Controller

↓

OrderService

↓

PaymentService

↓

SQL Repository
```

Suppose the SQL Repository throws an exception.

Repository

```csharp
public void SaveOrder()
{
    throw new Exception("Database unavailable.");
}
```

Service

```csharp
public void ProcessOrder()
{
    try
    {
        SaveOrder();
    }
    catch(Exception)
    {
        throw;
    }
}
```

Stack Trace

```
SaveOrder()

↓

ProcessOrder()

↓

Controller()

↓

Main()
```

Now replace it with

```csharp
catch(Exception ex)
{
    throw ex;
}
```

Stack Trace

```
ProcessOrder()

↓

Controller()

↓

Main()
```

The SQL Repository no longer appears as the origin of the exception.

This makes production debugging much harder.

---

# 8. Preserving Stack Trace

Suppose you want to log the exception before allowing it to propagate.

Correct approach

```csharp
catch(Exception ex)
{
    logger.LogError(ex, "Database error.");

    throw;
}
```

Benefits

- Logs the exception
- Preserves stack trace
- Higher layers can still handle it

---

# 9. Wrapping Exceptions

Sometimes you want to provide additional business context.

Instead of

```csharp
throw ex;
```

create a new exception.

```csharp
catch(Exception ex)
{
    throw new Exception(
        "Unable to process customer order.",
        ex);
}
```

The original exception becomes the `InnerException`.

Example

```
Outer Exception

↓

InnerException

↓

Original Database Exception
```

This approach adds meaningful context while preserving the original cause.

---

# 10. Best Practices

✔ Use `throw;` when rethrowing the same exception.

✔ Preserve the original stack trace.

✔ Use custom exceptions to add business meaning.

✔ Wrap exceptions using `InnerException` when additional context is required.

✔ Log exceptions before rethrowing them.

✔ Let Global Exception Middleware handle unhandled exceptions.

---

# 11. Common Mistakes

## Using `throw ex`

Bad

```csharp
catch(Exception ex)
{
    throw ex;
}
```

Reason

The original stack trace is reset.

---

## Swallowing Exceptions

Bad

```csharp
catch
{
}
```

This hides the exception completely.

---

## Creating New Exceptions Without InnerException

Bad

```csharp
catch(Exception ex)
{
    throw new Exception("Database Error");
}
```

The original exception is lost.

Correct

```csharp
catch(Exception ex)
{
    throw new Exception(
        "Database Error",
        ex);
}
```

---

# 12. Interview Questions

## Q1. What is the difference between `throw` and `throw ex`?

`throw` rethrows the current exception while preserving the original stack trace.

`throw ex` rethrows the exception from the current catch block, causing the stack trace to start from that location.

---

## Q2. Which one should be used?

Always use

```csharp
throw;
```

when rethrowing the same exception.

---

## Q3. Why is preserving the stack trace important?

The stack trace tells developers where the exception actually originated.

Without it, debugging becomes much more difficult.

---

## Q4. When should we create a new exception?

When additional business context is needed.

Example

```csharp
throw new OrderProcessingException(
    "Unable to process order.",
    ex);
```

The original exception should always be passed as the `InnerException`.

---

## Q5. What is `InnerException`?

`InnerException` stores the original exception inside another exception.

This allows developers to add business context while still preserving the original error.

---

# 13. Summary

In this module you learned:

- ✅ What is a Stack Trace
- ✅ How the CLR creates a stack trace
- ✅ What `throw` does
- ✅ What `throw ex` does
- ✅ Why `throw` preserves the original stack trace
- ✅ Why `throw ex` resets the stack trace
- ✅ How to wrap exceptions correctly using `InnerException`
- ✅ Best practices for production applications
- ✅ Common mistakes
- ✅ Senior-level interview questions

---

# Architecture Diagram

```
                    Repository
                         │
                         ▼
                 Exception Thrown
                         │
                         ▼
                    Service Layer
                         │
             ┌───────────┴───────────┐
             │                       │
             ▼                       ▼
          throw;                 throw ex;
             │                       │
             ▼                       ▼
 Original Stack Trace       Stack Trace Reset
      Preserved              Starts Here
             │                       │
             ▼                       ▼
         Controller             Controller
             │                       │
             ▼                       ▼
             Main                    Main
```

---

# Quick Revision

| Feature | `throw;` | `throw ex;` |
|----------|----------|-------------|
| Reuses current exception | ✅ Yes | ✅ Yes |
| Preserves original stack trace | ✅ Yes | ❌ No |
| Original exception location retained | ✅ Yes | ❌ No |
| Recommended by Microsoft | ✅ Yes | ❌ No |
| Suitable for production | ✅ Yes | ❌ Avoid |

---

# Next Module

## Module 35.4 – Built-in Exceptions in .NET

Topics Covered:

- Exception
- ArgumentException
- ArgumentNullException
- InvalidOperationException
- NullReferenceException
- IndexOutOfRangeException
- DivideByZeroException
- FormatException
- FileNotFoundException
- UnauthorizedAccessException
- TimeoutException
- When to use each exception
- Best practices
- Real-world examples
- Senior-level interview questions

> **Goal:** Learn the purpose of the most commonly used .NET exceptions and understand when to throw or handle each one appropriately in production applications.