# ASP.NET Core - 5 Most Common Custom Middleware (Enterprise Guide)

## Overview

These are five custom middleware types that are widely used in
enterprise ASP.NET Core applications. They are excellent interview
topics because they demonstrate cross-cutting concerns.

------------------------------------------------------------------------

# 1. Global Exception Middleware ⭐⭐⭐⭐⭐

## Purpose

Catch all unhandled exceptions and return a consistent error response.

## Why use it?

-   Avoid try/catch in every controller
-   Centralized error handling
-   Log exceptions
-   Return standard JSON responses

## Example

``` csharp
public class ExceptionMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<ExceptionMiddleware> _logger;

    public ExceptionMiddleware(RequestDelegate next,
        ILogger<ExceptionMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, ex.Message);

            context.Response.StatusCode = 500;
            await context.Response.WriteAsJsonAsync(new
            {
                Message = "An unexpected error occurred."
            });
        }
    }
}
```

Used in almost every production application.

------------------------------------------------------------------------

# 2. Request Logging Middleware ⭐⭐⭐⭐⭐

## Purpose

Log every incoming request.

## Information commonly logged

-   HTTP Method
-   URL
-   Query String
-   IP Address
-   User
-   Timestamp

## Example

``` csharp
public class RequestLoggingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<RequestLoggingMiddleware> _logger;

    public RequestLoggingMiddleware(
        RequestDelegate next,
        ILogger<RequestLoggingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        _logger.LogInformation(
            "{Method} {Path}",
            context.Request.Method,
            context.Request.Path);

        await _next(context);
    }
}
```

Useful for auditing and troubleshooting.

------------------------------------------------------------------------

# 3. Performance (Execution Time) Middleware ⭐⭐⭐⭐☆

## Purpose

Measure request execution time.

## Example

``` csharp
public class PerformanceMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<PerformanceMiddleware> _logger;

    public PerformanceMiddleware(
        RequestDelegate next,
        ILogger<PerformanceMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        var watch = System.Diagnostics.Stopwatch.StartNew();

        await _next(context);

        watch.Stop();

        _logger.LogInformation(
            "{Path} took {Elapsed} ms",
            context.Request.Path,
            watch.ElapsedMilliseconds);
    }
}
```

Ideal for identifying slow APIs.

------------------------------------------------------------------------

# 4. Correlation ID Middleware ⭐⭐⭐⭐⭐

## Purpose

Attach a unique identifier to every request so logs across multiple
services can be correlated.

## Example

``` csharp
public class CorrelationIdMiddleware
{
    private readonly RequestDelegate _next;

    public CorrelationIdMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        var correlationId =
            Guid.NewGuid().ToString();

        context.Items["CorrelationId"] =
            correlationId;

        context.Response.Headers["X-Correlation-ID"] =
            correlationId;

        await _next(context);
    }
}
```

Very common in microservices.

------------------------------------------------------------------------

# 5. Security Headers Middleware ⭐⭐⭐⭐☆

## Purpose

Automatically add security headers to every HTTP response.

## Example

``` csharp
public class SecurityHeadersMiddleware
{
    private readonly RequestDelegate _next;

    public SecurityHeadersMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        context.Response.Headers["X-Frame-Options"] = "DENY";
        context.Response.Headers["X-Content-Type-Options"] = "nosniff";
        context.Response.Headers["Referrer-Policy"] = "strict-origin";

        await _next(context);
    }
}
```

Improves browser security.

------------------------------------------------------------------------

# Registration

``` csharp
app.UseMiddleware<ExceptionMiddleware>();
app.UseMiddleware<RequestLoggingMiddleware>();
app.UseMiddleware<PerformanceMiddleware>();
app.UseMiddleware<CorrelationIdMiddleware>();
app.UseMiddleware<SecurityHeadersMiddleware>();
```

------------------------------------------------------------------------

# Typical Enterprise Pipeline

``` text
Browser
    ↓
Correlation ID
    ↓
Exception Middleware
    ↓
Request Logging
    ↓
Performance Middleware
    ↓
Authentication
    ↓
Authorization
    ↓
Controller
    ↓
Response
```

------------------------------------------------------------------------

# Interview Questions

1.  Why is Exception Middleware preferred over try/catch in controllers?
2.  Why should logging be implemented in middleware?
3.  What is a Correlation ID and why is it useful?
4.  Why measure request execution time?
5.  Why should middleware focus on a single responsibility?

------------------------------------------------------------------------

# Key Takeaways

-   Middleware is ideal for cross-cutting concerns.
-   Keep middleware lightweight and reusable.
-   Use `ILogger<T>` instead of `Console.WriteLine()`.
-   Call `await _next(context)` unless intentionally short-circuiting.
-   These five middleware are among the most commonly used in enterprise
    ASP.NET Core applications.
