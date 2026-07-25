# Module 31 -- ASP.NET Core Middleware Pipeline

# Part 5 -- Middleware Execution Flow & Pipeline Internals

## Learning Objectives

-   Startup vs Request Processing
-   Middleware pipeline creation
-   Delegate chaining
-   RequestDelegate
-   HttpContext lifecycle
-   Interview preparation

------------------------------------------------------------------------

## Application Startup

Runs once.

``` csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

The middleware pipeline is built during startup.

------------------------------------------------------------------------

## Request Processing

Runs for every request.

``` text
Browser
 ↓
Kestrel
 ↓
Create HttpContext
 ↓
Middleware Pipeline
 ↓
Controller
```

------------------------------------------------------------------------

## Middleware Registration

``` csharp
app.UseMiddleware<LoggingMiddleware>();
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
```

ASP.NET Core registers middleware and builds a delegate chain.
Middleware is not executed during startup.

------------------------------------------------------------------------

## Delegate Chain

``` text
Logging
 ↓
Authentication
 ↓
Authorization
 ↓
Controller
```

Each middleware knows only the next middleware through
`RequestDelegate`.

------------------------------------------------------------------------

## Request Execution

``` text
Browser
 ↓
Logging
 ↓
Authentication
 ↓
Authorization
 ↓
Controller
```

Response:

``` text
Controller
 ↑
Authorization
 ↑
Authentication
 ↑
Logging
 ↑
Browser
```

------------------------------------------------------------------------

## How UseMiddleware Works

-   Registers middleware
-   Links it with the next middleware
-   Prepares `InvokeAsync()`
-   Executes only when a request arrives

------------------------------------------------------------------------

## Why Middleware is Fast

The delegate chain is built once during startup.

Every request simply executes the existing chain.

------------------------------------------------------------------------

## Common Misconceptions

-   Middleware is recreated for every request ❌
-   `UseAuthentication()` authenticates immediately ❌
-   `next()` calls the controller directly ❌

------------------------------------------------------------------------

## Interview Questions

### Does `UseAuthentication()` run for every request?

No. It registers middleware once during startup.

### What creates `HttpContext`?

The ASP.NET Core hosting infrastructure creates one per request.

### What is `RequestDelegate`?

A delegate representing the next middleware.

### Why is middleware execution fast?

Because the pipeline is pre-built.

------------------------------------------------------------------------

## Quick Revision

-   Startup builds the pipeline.
-   Requests execute the pipeline.
-   Each request gets a new `HttpContext`.
-   Responses execute in reverse order.

------------------------------------------------------------------------

## Key Takeaways

-   Middleware is registered once and executed per request.
-   Delegate chaining makes middleware efficient.
-   Understanding pipeline internals is essential for senior ASP.NET
    Core interviews.
