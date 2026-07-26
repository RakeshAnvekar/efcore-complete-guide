# Module 35.7 – Global Exception Handling in ASP.NET Core

> **Course:** ASP.NET Core – Complete Interview Roadmap
>
> **Module:** 35.7 – Global Exception Handling
>
> **Level:** Intermediate → Advanced
>
> **Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Introduction
2. What is Global Exception Handling?
3. Why Do We Need Global Exception Handling?
4. Local vs Global Exception Handling
5. ASP.NET Core Request Pipeline
6. Creating Exception Middleware
7. Understanding the Middleware
8. Registering the Middleware
9. Returning Standard Error Responses
10. Logging Exceptions
11. Enterprise Request Flow
12. Handling Custom Exceptions
13. Best Practices
14. Common Mistakes
15. Interview Questions
16. Summary

---

# 1. Introduction

Applications interact with:

- Databases
- APIs
- File Systems
- External Services
- Message Queues

Any of these operations can fail unexpectedly.

Examples:

- Database timeout
- Network failure
- File not found
- Payment gateway failure
- Invalid operation

If exceptions are not handled properly, the application may return inconsistent responses or fail unexpectedly.

Global Exception Handling ensures that every unhandled exception is processed in one central location.

---

# 2. What is Global Exception Handling?

Global Exception Handling is a centralized mechanism that catches **all unhandled exceptions** occurring during an HTTP request.

Instead of placing `try-catch` blocks inside every controller action, one middleware handles all unexpected exceptions.

```
Client

↓

Request

↓

Exception Middleware

↓

Controller

↓

Service

↓

Repository

↓

Database
```

If any layer throws an exception, the middleware catches it before the response is sent back to the client.

---

# 3. Why Do We Need Global Exception Handling?

Imagine an application with:

- 40 Controllers
- 250 API Endpoints

Without Global Exception Handling:

Every API contains:

```csharp
try
{
    // Business Logic
}
catch(Exception ex)
{
    return StatusCode(500);
}
```

Problems:

- Duplicate code
- Difficult maintenance
- Inconsistent responses
- Difficult logging
- Different error formats

Instead, one middleware can handle every exception.

---

# 4. Local vs Global Exception Handling

## Local Exception Handling

```csharp
public IActionResult GetCustomer(int id)
{
    try
    {
        var customer = _service.GetCustomer(id);

        return Ok(customer);
    }
    catch(Exception ex)
    {
        return StatusCode(500, ex.Message);
    }
}
```

Problems

- Repeated in every controller
- Hard to maintain
- Easy to forget
- Different developers return different responses

---

## Global Exception Handling

```
Controller

↓

Service

↓

Repository

↓

Exception

↓

Exception Middleware

↓

Standard JSON Response
```

Controllers remain clean and focus only on business logic.

---

# 5. ASP.NET Core Request Pipeline

Every HTTP request passes through middleware.

```
Client

↓

Routing Middleware

↓

Authentication Middleware

↓

Authorization Middleware

↓

Exception Middleware

↓

Controller

↓

Service

↓

Repository

↓

Database
```

If any component throws an exception,

the request travels back through the pipeline.

The Exception Middleware catches it.

---

# Request Flow

```
HTTP Request

↓

Middleware

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

Exception

↓

Exception Middleware

↓

JSON Response
```

---

# 6. Creating Exception Middleware

```csharp
public class ExceptionMiddleware
{
    private readonly RequestDelegate _next;

    public ExceptionMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch(Exception ex)
        {
            await HandleException(context, ex);
        }
    }

    private static async Task HandleException(
        HttpContext context,
        Exception ex)
    {
        context.Response.ContentType = "application/json";
        context.Response.StatusCode = StatusCodes.Status500InternalServerError;

        await context.Response.WriteAsJsonAsync(new
        {
            StatusCode = 500,
            Message = "An unexpected error occurred."
        });
    }
}
```

---

# 7. Understanding the Middleware

## RequestDelegate

```csharp
private readonly RequestDelegate _next;
```

Represents the next middleware in the pipeline.

---

## Constructor

```csharp
public ExceptionMiddleware(RequestDelegate next)
{
    _next = next;
}
```

ASP.NET Core injects the next middleware automatically.

---

## InvokeAsync()

```csharp
await _next(context);
```

This forwards the request to the next middleware.

If an exception occurs,

execution jumps to:

```csharp
catch(Exception ex)
```

---

## HandleException()

This method:

- Logs the exception (optional)
- Sets HTTP Status Code
- Creates a standard response
- Sends JSON back to the client

---

# Middleware Execution Flow

```
HTTP Request

↓

Exception Middleware

↓

_next(context)

↓

Controller

↓

Exception?

        │
   ┌────┴────┐
   │         │
 No        Yes
   │         │
   ▼         ▼
Response   Catch Exception

              ↓

      HandleException()

              ↓

      Return JSON Response
```

---

# 8. Registering the Middleware

In **Program.cs**

```csharp
var app = builder.Build();

app.UseMiddleware<ExceptionMiddleware>();

app.MapControllers();

app.Run();
```

The middleware should be registered **early in the pipeline** so it can catch exceptions thrown by middleware and endpoints that execute after it.

---

# 9. Returning Standard Error Responses

Instead of different APIs returning different formats,

return one consistent structure.

```csharp
await context.Response.WriteAsJsonAsync(new
{
    StatusCode = 500,
    Message = "An unexpected error occurred."
});
```

Response

```json
{
  "statusCode": 500,
  "message": "An unexpected error occurred."
}
```

Benefits

- Easy for frontend developers
- Consistent APIs
- Easier debugging
- Easier documentation

---

# 10. Logging Exceptions

Always log exceptions.

Example

```csharp
_logger.LogError(
    ex,
    "Unhandled Exception");
```

Useful information to log:

- Exception Message
- Stack Trace
- Request Path
- User Id
- Correlation Id
- Timestamp

Example

```
Error

↓

Message

↓

Stack Trace

↓

Request URL

↓

User

↓

Date & Time
```

Logging helps diagnose production issues.

---

# 11. Enterprise Request Flow

```
Client

↓

HTTP Request

↓

Exception Middleware

↓

Authentication

↓

Authorization

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

Exception

↓

Exception Middleware

↓

Logger

↓

JSON Response

↓

Client
```

Notice:

Only one component handles every exception.

---

# 12. Handling Custom Exceptions

Suppose the Service throws:

```csharp
throw new PaymentFailedException(
    "Payment declined.");
```

The middleware can return different HTTP status codes based on the exception type.

```csharp
context.Response.StatusCode =
    ex switch
    {
        PaymentFailedException => StatusCodes.Status400BadRequest,

        KeyNotFoundException => StatusCodes.Status404NotFound,

        _ => StatusCodes.Status500InternalServerError
    };
```

Response

```
PaymentFailedException

↓

400 Bad Request
```

```
KeyNotFoundException

↓

404 Not Found
```

```
Unknown Exception

↓

500 Internal Server Error
```

This makes APIs much more meaningful.

---

# 13. Best Practices

✔ Handle exceptions in one central middleware.

✔ Return consistent JSON responses.

✔ Never expose stack traces in production.

✔ Log every unhandled exception.

✔ Use custom exceptions for business rules.

✔ Return appropriate HTTP status codes.

✔ Use **ProblemDetails** for standardized API error responses.

✔ Keep controllers focused on business logic.

---

# 14. Common Mistakes

## Returning Exception Details

Bad

```csharp
return ex.ToString();
```

This exposes:

- Server Paths
- Stack Trace
- Internal Code

Instead

```json
{
    "message":"Something went wrong."
}
```

---

## Using try-catch Everywhere

Bad

```
Controller 1

↓

try-catch
```

```
Controller 2

↓

try-catch
```

```
Controller 3

↓

try-catch
```

Use Global Exception Middleware instead.

---

## Ignoring Exceptions

Bad

```csharp
catch(Exception)
{
}
```

Always log exceptions.

---

## Returning Different Error Formats

Bad

Controller A

```json
{
    "error":"Failed"
}
```

Controller B

```json
{
    "message":"Error"
}
```

Controller C

```json
{
    "status":"Failed"
}
```

Always return one standard structure.

---

# 15. Interview Questions

## Q1. What is Global Exception Handling?

Global Exception Handling is a centralized mechanism that catches all unhandled exceptions and returns consistent API responses.

---

## Q2. Why use Exception Middleware?

To eliminate duplicate try-catch blocks, centralize logging, and return standardized error responses.

---

## Q3. Where should Exception Middleware be registered?

Near the beginning of the middleware pipeline so it can catch exceptions thrown by later middleware and controllers.

---

## Q4. Should exception details be returned to clients?

No.

Detailed information should be logged internally.

Clients should receive safe and user-friendly messages.

---

## Q5. What are the advantages?

- Centralized exception handling
- Cleaner controllers
- Consistent responses
- Easier maintenance
- Centralized logging
- Better production debugging

---

## Q6. Can Global Exception Middleware handle Custom Exceptions?

Yes.

It can inspect the exception type and return different HTTP status codes.

Example:

- `PaymentFailedException` → **400 Bad Request**
- `KeyNotFoundException` → **404 Not Found**
- Other exceptions → **500 Internal Server Error**

---

# 16. Summary

In this module you learned:

- ✅ What Global Exception Handling is
- ✅ Why it is needed
- ✅ Local vs Global Exception Handling
- ✅ ASP.NET Core Request Pipeline
- ✅ Creating Exception Middleware
- ✅ Registering Middleware
- ✅ Returning standard JSON responses
- ✅ Logging exceptions
- ✅ Handling Custom Exceptions
- ✅ Best practices
- ✅ Common mistakes
- ✅ Senior-level interview questions

---

# Architecture Diagram

```
                   HTTP Request
                        │
                        ▼
           Global Exception Middleware
                        │
                        ▼
                 Authentication
                        │
                        ▼
                 Authorization
                        │
                        ▼
                   Controller
                        │
                        ▼
                     Service
                        │
                        ▼
                   Repository
                        │
                        ▼
                    Database
                        │
                  Exception Thrown
                        │
                        ▼
           Global Exception Middleware
                        │
          ┌─────────────┴─────────────┐
          │                           │
          ▼                           ▼
     Log Exception          Create JSON Response
          │                           │
          └─────────────┬─────────────┘
                        ▼
                 HTTP Response
```

---

# Quick Revision

| Local Exception Handling | Global Exception Handling |
|--------------------------|---------------------------|
| `try-catch` in every action | One middleware for the whole application |
| Duplicate code | Centralized code |
| Hard to maintain | Easy to maintain |
| Inconsistent responses | Standard responses |
| Logging in multiple places | Centralized logging |
| Controller handles errors | Middleware handles errors |

---

# Next Module

## Module 35.8 – ProblemDetails (RFC 7807)

Topics Covered:

- What is `ProblemDetails`
- Why ASP.NET Core introduced it
- Standard API Error Format
- RFC 7807 Specification
- Using `ProblemDetails` in Middleware
- Customizing Error Responses
- Validation Problem Details
- Enterprise Best Practices
- Senior-Level Interview Questions

> **Goal:** Learn how to build standardized, production-ready error responses using `ProblemDetails` in ASP.NET Core.