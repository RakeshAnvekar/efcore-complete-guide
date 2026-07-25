# Module 31 -- ASP.NET Core Middleware Pipeline

# Part 3 -- Middleware Ordering

## Learning Objectives

-   Why middleware order matters
-   Request and response flow
-   next()
-   Short-circuiting
-   Use(), Run(), Map()
-   Interview preparation

------------------------------------------------------------------------

## Middleware Execution

``` csharp
app.UseMiddleware<A>();
app.UseMiddleware<B>();
app.UseMiddleware<C>();
app.MapControllers();
```

### Request

``` text
Browser
↓
A
↓
B
↓
C
↓
Controller
```

### Response

``` text
Controller
↑
C
↑
B
↑
A
↑
Browser
```

------------------------------------------------------------------------

## Before and After next()

``` csharp
public async Task InvokeAsync(HttpContext context)
{
    Console.WriteLine("Before");
    await _next(context);
    Console.WriteLine("After");
}
```

Output:

``` text
A Before
B Before
C Before
Controller
C After
B After
A After
```

------------------------------------------------------------------------

## What does next() do?

`await _next(context)` executes the next middleware in the pipeline.

Without calling it, the pipeline stops.

------------------------------------------------------------------------

## Short-Circuiting

``` csharp
await context.Response.WriteAsync("Blocked");
```

No `_next(context)` means the request ends immediately.

Common examples:

-   Static Files
-   Authentication failure
-   Health checks
-   Maintenance mode

------------------------------------------------------------------------

## Recommended Order

``` csharp
app.UseExceptionHandler("/Error");
app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseRouting();
app.UseCors();
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

------------------------------------------------------------------------

## Why This Order?

-   Exception handler catches later exceptions.
-   HTTPS redirects early.
-   Static files bypass MVC.
-   Routing selects endpoint.
-   Authentication identifies user.
-   Authorization checks permission.
-   Endpoint executes controller.

------------------------------------------------------------------------

## Use vs Run vs Map

### Use()

Continues the pipeline.

### Run()

Terminal middleware.

### Map()

Creates a branch based on the request path.

------------------------------------------------------------------------

## Common Mistakes

-   Authorization before Authentication
-   MapControllers before Authentication
-   Forgetting UseRouting
-   Exception handler registered too late

------------------------------------------------------------------------

## Interview Questions

**Why is middleware order important?**

Because middleware executes in registration order for requests and
reverse order for responses.

**What happens if next() isn't called?**

The pipeline is short-circuited.

**Why Authentication before Authorization?**

Authorization requires an authenticated user.

------------------------------------------------------------------------

## Quick Revision

-   Request → registration order
-   Response → reverse order
-   next() continues execution
-   No next() stops the pipeline
-   Use() continues
-   Run() terminates
-   Map() branches

------------------------------------------------------------------------

## Key Takeaways

-   Middleware ordering is critical.
-   Requests and responses move in opposite directions.
-   Correct ordering ensures routing, security, and exception handling
    work properly.
