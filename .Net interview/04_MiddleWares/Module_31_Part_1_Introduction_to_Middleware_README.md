# Module 31 -- ASP.NET Core Middleware Pipeline

# Part 1 -- Introduction to Middleware

## Learning Objectives

-   What is Middleware?
-   Why Middleware exists
-   What is the Request Pipeline?
-   Request and Response flow
-   Internal architecture
-   Interview preparation

------------------------------------------------------------------------

## What is Middleware?

Middleware is a software component that processes HTTP requests and
responses in the ASP.NET Core request pipeline.

A middleware can:

-   Read the request
-   Modify the request
-   Call the next middleware
-   Stop the request
-   Modify the response

------------------------------------------------------------------------

## Why Do We Need Middleware?

Middleware moves common functionality such as logging, authentication,
authorization, exception handling, HTTPS redirection and response
compression out of controllers, keeping business logic clean and
reusable.

------------------------------------------------------------------------

## Request Flow

``` text
Browser
 ↓
Kestrel
 ↓
Middleware Pipeline
 ↓
Routing
 ↓
Authentication
 ↓
Authorization
 ↓
Controller
 ↓
Database
 ↓
Controller
 ↓
Middleware Pipeline
 ↓
Browser
```

------------------------------------------------------------------------

## Request Pipeline

The Request Pipeline is the ordered sequence of middleware components
that process every incoming request and outgoing response.

``` text
Request
 ↓
Middleware 1
 ↓
Middleware 2
 ↓
Middleware 3
 ↓
Controller
 ↑
Middleware 3
 ↑
Middleware 2
 ↑
Middleware 1
 ↑
Response
```

------------------------------------------------------------------------

## Why is it Called a Pipeline?

The HTTP request flows through each middleware one after another, just
like water flows through a pipe containing multiple filters.

------------------------------------------------------------------------

## Built-in Middleware

-   Exception Handling
-   HTTPS Redirection
-   Static Files
-   Routing
-   CORS
-   Authentication
-   Authorization
-   Session
-   Response Compression
-   Endpoint Middleware

------------------------------------------------------------------------

## Internal Architecture

``` text
Browser
 ↓
Kestrel
 ↓
RequestDelegate
 ↓
Middleware
 ↓
Controller
```

Each middleware receives a `RequestDelegate` representing the next
middleware.

------------------------------------------------------------------------

## What Can Middleware Do?

-   Authenticate users
-   Authorize users
-   Log requests
-   Handle exceptions
-   Serve static files
-   Read headers and cookies
-   Modify requests and responses
-   Stop request execution

------------------------------------------------------------------------

## Short-Circuiting

A middleware can stop the pipeline by returning a response without
calling the next middleware.

Example:

``` text
Request
 ↓
Authentication Middleware
 ↓
401 Unauthorized
```

The controller is never reached.

------------------------------------------------------------------------

## Interview Questions

### What is Middleware?

A software component that processes HTTP requests and responses in the
ASP.NET Core request pipeline.

### What is the Request Pipeline?

The ordered sequence of middleware that processes requests and
responses.

### Can Middleware stop a request?

Yes.

### Can Middleware modify both request and response?

Yes.

------------------------------------------------------------------------

## Quick Revision

-   Middleware processes HTTP requests and responses.
-   Every request passes through the middleware pipeline.
-   Middleware executes in registration order.
-   Responses travel back in reverse order.
-   Middleware can terminate the request.

------------------------------------------------------------------------

## Key Takeaways

-   Middleware is the foundation of ASP.NET Core request processing.
-   It separates cross-cutting concerns from business logic.
-   Understanding the middleware pipeline is essential before learning
    custom middleware and middleware ordering.
