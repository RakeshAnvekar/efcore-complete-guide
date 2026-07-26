# Built-in Exception Handling in ASP.NET Core

> **Course:** ASP.NET Core – Complete Interview Roadmap
>
> **Module:** Built-in Exception Handling Middleware
>
> **Level:** Intermediate → Advanced
>
> **Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Introduction
2. Built-in Exception Handling Middleware
3. UseExceptionHandler()
4. How UseExceptionHandler() Works
5. Custom Exception Middleware
6. IExceptionHandler (.NET 8+)
7. Comparison
8. Best Practices
9. Interview Questions
10. Summary

---

# 1. Introduction

ASP.NET Core provides built-in mechanisms to handle unhandled exceptions.

Instead of writing custom middleware from scratch, Microsoft provides middleware that can catch exceptions and return appropriate responses.

There are three common approaches:

- UseExceptionHandler()
- Custom Exception Middleware
- IExceptionHandler (.NET 8+)

---

# 2. Built-in Exception Handling Middleware

ASP.NET Core includes a built-in middleware called **Exception Handler Middleware**.

It catches all unhandled exceptions thrown by the application.

Example

```csharp
var app = builder.Build();

app.UseExceptionHandler("/Error");

app.MapControllers();

app.Run();
```

The middleware catches exceptions before the response is sent to the client.

---

# Request Flow

```
HTTP Request

↓

UseExceptionHandler

↓

Controller

↓

Service

↓

Repository

↓

Exception

↓

UseExceptionHandler

↓

Error Response
```

---

# 3. UseExceptionHandler()

The simplest way to enable global exception handling.

```csharp
app.UseExceptionHandler();
```

Or configure a custom endpoint.

```csharp
app.UseExceptionHandler("/Error");
```

You can also configure it inline.

```csharp
app.UseExceptionHandler(errorApp =>
{
    errorApp.Run(async context =>
    {
        context.Response.StatusCode = 500;

        await context.Response.WriteAsync(
            "Something went wrong.");
    });
});
```

---

# 4. How UseExceptionHandler() Works

Internally, ASP.NET Core wraps the remaining request pipeline.

Conceptually it works like this:

```csharp
try
{
    await _next(context);
}
catch(Exception ex)
{
    // Log exception

    // Generate response

    // Return response
}
```

Whenever an exception occurs, the middleware catches it and prevents the application from crashing.

---

# Execution Flow

```
HTTP Request

↓

UseExceptionHandler

↓

Next Middleware

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

      Return Error Response
```

---

# 5. Custom Exception Middleware

Sometimes the built-in middleware is not enough.

For example, you may need:

- Custom JSON responses
- Exception logging
- Correlation IDs
- Custom exception mapping
- ProblemDetails
- Serilog integration

Example

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
            // Custom handling
        }
    }
}
```

Register

```csharp
app.UseMiddleware<ExceptionMiddleware>();
```

---

# 6. IExceptionHandler (.NET 8+)

.NET 8 introduced **IExceptionHandler**.

Instead of creating middleware manually, implement the interface.

```csharp
public class GlobalExceptionHandler : IExceptionHandler
{
    public async ValueTask<bool> TryHandleAsync(
        HttpContext context,
        Exception exception,
        CancellationToken cancellationToken)
    {
        context.Response.StatusCode = 500;

        await context.Response.WriteAsJsonAsync(
            new
            {
                Message = "Something went wrong."
            },
            cancellationToken);

        return true;
    }
}
```

Register

```csharp
builder.Services.AddExceptionHandler<GlobalExceptionHandler>();

var app = builder.Build();

app.UseExceptionHandler();
```

Benefits

- Built into .NET 8+
- Cleaner code
- Easier testing
- Works well with ProblemDetails
- Microsoft recommended approach

---

# 7. Comparison

| Feature | UseExceptionHandler | Custom Middleware | IExceptionHandler (.NET 8+) |
|----------|---------------------|-------------------|-----------------------------|
| Built into ASP.NET Core | ✅ Yes | ❌ No | ✅ Yes |
| Easy to configure | ✅ Yes | ⚠ Moderate | ✅ Yes |
| Fully customizable | ⚠ Limited | ✅ Yes | ✅ Yes |
| Supports custom exception mapping | ⚠ With extra code | ✅ Yes | ✅ Yes |
| Recommended for .NET 8+ | ⚠ Yes | ⚠ When needed | ✅ Yes |

---

# 8. Best Practices

✔ Use `UseExceptionHandler()` for simple applications.

✔ Use `IExceptionHandler` for new .NET 8+ applications.

✔ Create custom middleware only when you need complete control.

✔ Never expose stack traces to clients.

✔ Always log unhandled exceptions.

✔ Return consistent JSON responses.

✔ Integrate with `ProblemDetails` for standard API error responses.

---

# 9. Interview Questions

## Q1. Does ASP.NET Core provide built-in exception handling?

Yes.

ASP.NET Core provides the built-in **Exception Handler Middleware**, enabled using `UseExceptionHandler()`.

---

## Q2. What is UseExceptionHandler()?

It is built-in middleware that catches unhandled exceptions and returns an error response.

---

## Q3. What is IExceptionHandler?

`IExceptionHandler` is a new interface introduced in .NET 8 for implementing centralized exception handling without creating custom middleware.

---

## Q4. Which approach should you use?

- Small applications → `UseExceptionHandler()`
- .NET 8+ applications → `IExceptionHandler`
- Complex enterprise scenarios → Custom Middleware or `IExceptionHandler` with custom logic

---

## Q5. Why would you create custom middleware instead of using UseExceptionHandler()?

To:

- Return custom JSON
- Handle custom exceptions differently
- Integrate with logging frameworks
- Add Correlation IDs
- Implement custom business rules

---

# 10. Summary

In this module you learned:

- ✅ ASP.NET Core has built-in exception handling.
- ✅ `UseExceptionHandler()` is the built-in middleware.
- ✅ Custom middleware provides full flexibility.
- ✅ `IExceptionHandler` is the recommended approach for .NET 8+ applications.
- ✅ Always log exceptions and return consistent responses.

---

# Architecture Diagram

```
                 HTTP Request
                      │
                      ▼
          UseExceptionHandler()
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
                 Exception
                      │
                      ▼
        Built-in Exception Handler
                      │
                      ▼
              Error Response
```

---

# Quick Revision

| Scenario | Recommended Approach |
|-----------|----------------------|
| Simple application | `UseExceptionHandler()` |
| .NET 8+ application | `IExceptionHandler` |
| Full customization | Custom Exception Middleware |
| Enterprise logging & custom responses | `IExceptionHandler` or Custom Middleware |

---

# Next Module

## Module 35.8 – ProblemDetails (RFC 7807)

Topics Covered:

- What is ProblemDetails?
- Why ASP.NET Core uses it
- RFC 7807 Standard
- ProblemDetails vs Custom JSON
- Integrating with Exception Handling
- ValidationProblemDetails
- Enterprise Best Practices
- Senior-Level Interview Questions

> **Goal:** Learn how ASP.NET Core returns standardized, production-ready error responses using `ProblemDetails`.