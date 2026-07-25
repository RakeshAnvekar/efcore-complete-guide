# Module 31 -- ASP.NET Core Middleware Pipeline

# Part 2 -- Built-in Middleware

## Learning Objectives

-   Understand built-in middleware
-   Learn the purpose of each middleware
-   Understand middleware execution order
-   Learn internal working
-   Prepare for interview questions

------------------------------------------------------------------------

# Typical Middleware Pipeline

``` csharp
var app = builder.Build();

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

The order is important because each middleware depends on previous
middleware.

------------------------------------------------------------------------

# Exception Handling Middleware

``` csharp
app.UseExceptionHandler("/Error");
```

## Purpose

Handles unhandled exceptions and returns a friendly error response
instead of crashing the application.

Why first?

Because it can catch exceptions thrown by every middleware and
controller that executes after it.

------------------------------------------------------------------------

# HTTPS Redirection Middleware

``` csharp
app.UseHttpsRedirection();
```

Redirects HTTP requests to HTTPS to ensure encrypted communication.

Flow:

``` text
HTTP Request
   ↓
HTTPS Redirection
   ↓
302 Redirect
   ↓
HTTPS Request
```

------------------------------------------------------------------------

# Static File Middleware

``` csharp
app.UseStaticFiles();
```

Serves files directly from the **wwwroot** folder.

Examples:

-   CSS
-   JavaScript
-   Images
-   Fonts
-   PDF files

If a requested file exists, the controller is never executed.

------------------------------------------------------------------------

# Routing Middleware

``` csharp
app.UseRouting();
```

Finds the endpoint that matches the incoming request.

Example:

``` text
/api/employees

↓

EmployeeController.GetEmployees()
```

Routing only selects the endpoint; it does not execute it.

------------------------------------------------------------------------

# CORS Middleware

``` csharp
app.UseCors();
```

Controls which external websites are allowed to access your API.

Used for browser security and cross-origin requests.

------------------------------------------------------------------------

# Authentication Middleware

``` csharp
app.UseAuthentication();
```

Identifies the user by validating authentication credentials such as:

-   JWT
-   Cookies
-   OAuth

Authentication answers:

**Who are you?**

------------------------------------------------------------------------

# Authorization Middleware

``` csharp
app.UseAuthorization();
```

Checks whether the authenticated user has permission to access the
requested resource.

Authorization answers:

**Are you allowed to access this resource?**

Authentication must always execute before Authorization.

------------------------------------------------------------------------

# Endpoint Middleware

``` csharp
app.MapControllers();
```

Executes the endpoint selected by the Routing Middleware.

Flow:

``` text
Routing
   ↓
Endpoint Selected
   ↓
Controller Executes
```

------------------------------------------------------------------------

# Complete Request Pipeline

``` text
Browser
 ↓
Exception Handler
 ↓
HTTPS Redirection
 ↓
Static Files
 ↓
Routing
 ↓
CORS
 ↓
Authentication
 ↓
Authorization
 ↓
Endpoint Middleware
 ↓
Controller
 ↓
Database
 ↓
Response
```

------------------------------------------------------------------------

# Middleware Summary

  Middleware            Purpose
  --------------------- --------------------------------
  Exception Handler     Handles unhandled exceptions
  HTTPS Redirection     Redirects HTTP to HTTPS
  Static Files          Serves files from wwwroot
  Routing               Finds the matching endpoint
  CORS                  Controls cross-origin requests
  Authentication        Identifies the user
  Authorization         Verifies permissions
  Endpoint Middleware   Executes the selected endpoint

------------------------------------------------------------------------

# Common Mistakes

-   Registering Authorization before Authentication
-   Forgetting UseRouting()
-   Placing Exception Handler late in the pipeline
-   Expecting Static File Middleware to execute controllers

------------------------------------------------------------------------

# Interview Questions

## Why should Exception Handling Middleware be first?

It catches exceptions from all later middleware and controllers.

## Difference between Routing and Endpoint Middleware?

Routing selects the endpoint.

Endpoint Middleware executes the endpoint.

## Authentication vs Authorization?

Authentication identifies the user.

Authorization checks permissions.

## Can Static File Middleware bypass MVC?

Yes. If the file exists, it returns the file directly.

------------------------------------------------------------------------

# Quick Revision

-   Exception Handling should be first.
-   HTTPS Redirection enforces HTTPS.
-   Static Files can short-circuit the pipeline.
-   Routing selects endpoints.
-   Authentication identifies users.
-   Authorization verifies access.
-   Endpoint Middleware executes controllers.

------------------------------------------------------------------------

# Key Takeaways

-   ASP.NET Core provides many built-in middleware components.
-   Middleware order is critical.
-   Each middleware has a single responsibility.
-   Understanding built-in middleware is essential before learning
    middleware ordering and custom middleware.
