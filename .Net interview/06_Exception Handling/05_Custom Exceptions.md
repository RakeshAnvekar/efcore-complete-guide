# Module 35.5 – Custom Exceptions in C#

> **Course:** ASP.NET Core – Complete Interview Roadmap
>
> **Module:** 35.5 – Custom Exceptions
>
> **Level:** Intermediate → Advanced
>
> **Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Introduction
2. What are Custom Exceptions?
3. Why Do We Need Custom Exceptions?
4. Built-in Exceptions vs Custom Exceptions
5. Real-World Analogy
6. How to Create a Custom Exception
7. Understanding Exception Constructors
8. Using Custom Exceptions
9. Exception Flow in Enterprise Applications
10. Custom Exceptions with InnerException
11. Custom Exceptions with Additional Properties
12. Best Practices
13. When NOT to Create a Custom Exception
14. Common Mistakes
15. Interview Questions
16. Summary

---

# 1. Introduction

The .NET Framework provides many built-in exceptions for common runtime errors.

Examples include:

- `ArgumentNullException`
- `ArgumentException`
- `InvalidOperationException`
- `NullReferenceException`
- `DivideByZeroException`
- `FileNotFoundException`

However, these exceptions cannot represent every business scenario.

For business-specific errors, we create **Custom Exceptions**.

---

# 2. What are Custom Exceptions?

A **Custom Exception** is a user-defined exception class that represents a specific business or application error.

Instead of writing:

```csharp
throw new Exception("Order failed.");
```

Create a meaningful exception.

```csharp
throw new OrderProcessingException("Order failed.");
```

The exception name itself explains the problem.

---

# 3. Why Do We Need Custom Exceptions?

Suppose you're building an E-Commerce application.

Possible business errors:

- Product Out of Stock
- Payment Failed
- Coupon Expired
- Customer Not Found
- Order Already Cancelled

If every error is written like this:

```csharp
throw new Exception("Error");
```

Questions arise:

- Which module failed?
- What actually happened?
- Can the caller handle it differently?

Instead, use meaningful exceptions.

```csharp
throw new PaymentFailedException();
```

or

```csharp
throw new OutOfStockException();
```

The code becomes much more readable.

---

# 4. Built-in Exceptions vs Custom Exceptions

| Built-in Exception | Custom Exception |
|--------------------|------------------|
| Provided by .NET | Created by developers |
| Common runtime/programming errors | Business-specific errors |
| Reusable across applications | Specific to your application |
| Examples: `ArgumentNullException`, `InvalidOperationException` | Examples: `PaymentFailedException`, `OrderNotFoundException` |

---

# 5. Real-World Analogy

Imagine visiting a hospital.

The doctor does not simply say:

```
You are sick.
```

Instead, the doctor provides a diagnosis.

```
Viral Fever

Migraine

Diabetes

Fracture
```

Each diagnosis immediately explains the actual problem.

Similarly,

Instead of

```
Exception
```

Use

```
PaymentFailedException

OrderNotFoundException

OutOfStockException
```

Specific exceptions make the application easier to understand and maintain.

---

# 6. How to Create a Custom Exception

A custom exception should inherit from the `Exception` class.

```csharp
public class PaymentFailedException : Exception
{
    public PaymentFailedException()
    {
    }

    public PaymentFailedException(string message)
        : base(message)
    {
    }

    public PaymentFailedException(string message, Exception innerException)
        : base(message, innerException)
    {
    }
}
```

---

# 7. Understanding Exception Constructors

A custom exception normally contains three constructors.

## Default Constructor

```csharp
new PaymentFailedException();
```

Useful when no custom message is required.

---

## Message Constructor

```csharp
new PaymentFailedException(
    "Payment was declined.");
```

Allows a meaningful error message.

---

## Message + InnerException Constructor

```csharp
new PaymentFailedException(
    "Unable to process payment.",
    ex);
```

This preserves the original exception while adding business context.

---

# Why Do We Pass InnerException?

Suppose the Repository throws:

```csharp
throw new Exception("SQL Server timeout.");
```

The Service catches it.

```csharp
catch(Exception ex)
{
    throw new PaymentFailedException(
        "Unable to complete payment.",
        ex);
}
```

Now:

```
Outer Exception

↓

PaymentFailedException

↓

Unable to complete payment.

↓

InnerException

↓

SQL Server timeout.
```

The application shows a business-friendly message while developers can still identify the original cause.

---

# 8. Using Custom Exceptions

## Repository

```csharp
public void SavePayment()
{
    throw new Exception("SQL Server timeout.");
}
```

---

## Service

```csharp
public void ProcessPayment()
{
    try
    {
        SavePayment();
    }
    catch(Exception ex)
    {
        throw new PaymentFailedException(
            "Unable to complete payment.",
            ex);
    }
}
```

---

## Controller

```csharp
try
{
    ProcessPayment();
}
catch(PaymentFailedException ex)
{
    Console.WriteLine(ex.Message);

    Console.WriteLine(ex.InnerException.Message);
}
```

Output

```
Unable to complete payment.

SQL Server timeout.
```

---

# 9. Exception Flow in Enterprise Applications

```
Controller

↓

Service

↓

Repository

↓

SQL Exception

↓

Repository

↓

PaymentFailedException

↓

Controller

↓

Global Exception Middleware

↓

HTTP Response
```

Each layer has a different responsibility.

| Layer | Responsibility |
|--------|---------------|
| Repository | Technical operations (Database, API, File System) |
| Service | Business rules and exception wrapping |
| Controller | Receive business exception |
| Middleware | Convert exception into API response |

---

# 10. Custom Exceptions with InnerException

One of the biggest advantages of custom exceptions is preserving the original exception.

```csharp
catch(Exception ex)
{
    throw new PaymentFailedException(
        "Payment could not be completed.",
        ex);
}
```

Memory Representation

```
PaymentFailedException

↓

Message

↓

Payment could not be completed.

↓

InnerException

↓

SQL Server timeout.
```

This allows developers to debug the original issue without exposing technical details to users.

---

# 11. Custom Exceptions with Additional Properties

Sometimes extra information is useful.

Example:

```csharp
public class PaymentFailedException : Exception
{
    public int OrderId { get; }

    public PaymentFailedException(
        int orderId,
        string message)
        : base(message)
    {
        OrderId = orderId;
    }
}
```

Usage

```csharp
throw new PaymentFailedException(
    101,
    "Payment failed.");
```

Now the exception contains:

- OrderId
- Message

This information can also be logged.

---

# 12. Best Practices

✔ Always end custom exception names with **Exception**

Examples

```
PaymentFailedException

OrderNotFoundException

InventoryException
```

---

✔ Inherit from `Exception`.

---

✔ Implement the standard constructors.

---

✔ Preserve the original exception using `InnerException`.

---

✔ Create custom exceptions only for business scenarios.

---

✔ Use meaningful exception names.

---

✔ Log exceptions before returning API responses.

---

# 13. When NOT to Create a Custom Exception

Don't create custom exceptions for common programming errors.

Instead of

```csharp
throw new CustomerIsNullException();
```

Use

```csharp
throw new ArgumentNullException(nameof(customer));
```

Similarly,

Instead of

```csharp
throw new InvalidCustomerStateException();
```

If the situation represents an invalid operation rather than a business rule, use

```csharp
throw new InvalidOperationException();
```

Always prefer an existing .NET exception when it accurately describes the problem.

---

# 14. Common Mistakes

## Throwing Generic Exception

Bad

```csharp
throw new Exception("Error");
```

Reason

No business meaning.

---

## Ignoring InnerException

Bad

```csharp
catch(Exception ex)
{
    throw new PaymentFailedException(
        "Payment failed.");
}
```

The original exception is lost.

Correct

```csharp
catch(Exception ex)
{
    throw new PaymentFailedException(
        "Payment failed.",
        ex);
}
```

---

## Wrong Naming

Bad

```
PaymentError

OrderProblem
```

Good

```
PaymentFailedException

OrderNotFoundException
```

---

# 15. Interview Questions

## Q1. What is a Custom Exception?

A custom exception is a user-defined exception that represents a business-specific error not covered by the built-in .NET exceptions.

---

## Q2. Why do we create Custom Exceptions?

To improve readability, maintainability, and allow higher layers to handle business-specific errors differently.

---

## Q3. How do you create a Custom Exception?

Create a class that inherits from `Exception` and implement the standard constructors.

---

## Q4. Why should custom exceptions include an InnerException constructor?

It preserves the original exception while adding business-specific context.

---

## Q5. Should every exception be custom?

No.

Use built-in exceptions for programming errors and custom exceptions only for business-specific scenarios.

---

## Q6. Why shouldn't we throw Exception everywhere?

Because generic exceptions don't describe what actually went wrong, making debugging and exception handling more difficult.

---

# 16. Summary

In this module you learned:

- ✅ What Custom Exceptions are
- ✅ Why they are needed
- ✅ Difference between built-in and custom exceptions
- ✅ How to create a custom exception
- ✅ Standard constructors
- ✅ Using InnerException
- ✅ Enterprise exception flow
- ✅ Adding custom properties
- ✅ Best practices
- ✅ Common mistakes
- ✅ Interview questions

---

# Architecture Diagram

```
                Repository
                     │
                     ▼
          SQL Server Exception
                     │
                     ▼
                Service Layer
                     │
      Wrap into PaymentFailedException
                     │
                     ▼
               Controller Layer
                     │
                     ▼
       Global Exception Middleware
                     │
                     ▼
             HTTP Response (400/500)
```

---

# Quick Revision

| Scenario | Recommended Exception |
|-----------|-----------------------|
| Invalid method argument | `ArgumentException` / `ArgumentNullException` |
| Invalid object state | `InvalidOperationException` |
| Business rule violation | Custom Exception |
| Payment failed | `PaymentFailedException` |
| Order not found | `OrderNotFoundException` |
| Product out of stock | `OutOfStockException` |

---

# Next Module

## Module 35.6 – Global Exception Handling Middleware

Topics Covered:

- Why Global Exception Handling is Needed
- Local vs Global Exception Handling
- Creating Exception Middleware
- Request Pipeline Flow
- Returning Standard API Responses
- ProblemDetails in ASP.NET Core
- Logging Exceptions
- Production Best Practices
- Enterprise Architecture
- Senior-Level Interview Questions

> **Goal:** Learn how ASP.NET Core catches all unhandled exceptions in one place and returns consistent, secure, and production-ready API responses.