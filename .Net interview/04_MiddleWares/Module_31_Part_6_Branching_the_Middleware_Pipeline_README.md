# Module 31 -- ASP.NET Core Middleware Pipeline

# Part 6 -- Branching the Middleware Pipeline

## Learning Objectives

-   Understand middleware branching
-   Learn `Map()`
-   Learn `MapWhen()`
-   Learn `UseWhen()`
-   Understand when to use each
-   Prepare for senior interview questions

------------------------------------------------------------------------

# Why Branch the Middleware Pipeline?

Different requests often require different processing.

Examples:

-   `/admin` → Admin authentication
-   `/api` → JWT authentication
-   `/health` → No authentication
-   `/images` → Static files

Instead of using one pipeline for every request, ASP.NET Core allows
branching.

------------------------------------------------------------------------

# `Map()`

`Map()` creates a separate pipeline based on the request path.

``` csharp
app.Map("/admin", admin =>
{
    admin.Run(async context =>
    {
        await context.Response.WriteAsync("Welcome Admin");
    });
});
```

### Flow

``` text
Request
   ↓
Is path '/admin'?
   ├── Yes → Admin pipeline
   └── No  → Continue main pipeline
```

### Common Uses

-   `/admin`
-   `/api`
-   `/swagger`
-   `/health`
-   `/metrics`

------------------------------------------------------------------------

# `MapWhen()`

`MapWhen()` creates a branch using any boolean condition.

``` csharp
app.MapWhen(
    context => context.Request.Method == "POST",
    branch =>
    {
        branch.Run(async context =>
        {
            await context.Response.WriteAsync("POST Request");
        });
    });
```

Possible conditions:

-   HTTP method
-   Headers
-   IP address
-   Query string
-   User-Agent
-   Country

------------------------------------------------------------------------

# `UseWhen()`

`UseWhen()` executes additional middleware when a condition is true and
then returns to the main pipeline.

``` csharp
app.UseWhen(
    context => context.Request.Path.StartsWithSegments("/api"),
    branch =>
    {
        branch.Use(async (context, next) =>
        {
            Console.WriteLine("API Request");
            await next();
        });
    });

app.MapControllers();
```

### Flow

``` text
Request
   ↓
Condition True?
   ├── Yes → Execute branch middleware
   │          ↓
   │     Return to main pipeline
   └── No → Continue main pipeline
```

------------------------------------------------------------------------

# Map() vs MapWhen() vs UseWhen()

  Feature                      Map()                        MapWhen()   UseWhen()
  ---------------------------- ---------------------------- ----------- -----------------------------
  Branch by URL                ✅                           ❌          ❌
  Branch by custom condition   ❌                           ✅          ✅
  Continue main pipeline       Only if path doesn't match   ❌          ✅
  Separate pipeline            ✅                           ✅          Conditional middleware only

------------------------------------------------------------------------

# Real Banking Example

-   `/admin` → Admin authentication and authorization
-   `/api` → JWT authentication
-   `/health` → Immediate health response
-   `/transfer` → Normal application pipeline

------------------------------------------------------------------------

# When Should You Use Each?

### Use `Map()`

-   Different URL paths
-   Admin area
-   Swagger
-   Health endpoint

### Use `MapWhen()`

-   Browser detection
-   IP filtering
-   Custom headers
-   Feature flags

### Use `UseWhen()`

-   Conditional logging
-   API request timing
-   Extra validation
-   Diagnostics

------------------------------------------------------------------------

# Interview Questions

## Difference between `Map()` and `MapWhen()`?

-   `Map()` branches using the URL path.
-   `MapWhen()` branches using any boolean condition.

## Difference between `MapWhen()` and `UseWhen()`?

-   `MapWhen()` switches to a separate pipeline.
-   `UseWhen()` executes conditional middleware and then returns to the
    main pipeline.

## Can a `Map()` branch contain multiple middleware?

Yes.

------------------------------------------------------------------------

# Quick Revision

-   `Map()` → Path-based branching
-   `MapWhen()` → Condition-based branching
-   `UseWhen()` → Conditional middleware then continue
-   Branching creates specialized request pipelines

------------------------------------------------------------------------

# Key Takeaways

-   Branching improves flexibility and performance.
-   `Map()` is ideal for URL-based routing.
-   `MapWhen()` supports any custom condition.
-   `UseWhen()` is useful for conditional middleware without replacing
    the main pipeline.
-   These features are commonly used in enterprise ASP.NET Core
    applications.
