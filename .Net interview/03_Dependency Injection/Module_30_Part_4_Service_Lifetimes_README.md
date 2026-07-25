# Module 30 -- Dependency Injection

# Part 4 -- Service Lifetimes (Singleton, Scoped, Transient)

## Why Service Lifetimes?

ASP.NET Core must control how long objects live.

Three lifetimes:

-   Singleton: One instance for the entire application.
-   Scoped: One instance per HTTP request.
-   Transient: A new instance every time the service is resolved.

## Restaurant Analogy

-   Singleton = Chef (one chef serves everyone)
-   Scoped = Table (one table per customer group/request)
-   Transient = Glass of Water (new glass every request)

## Singleton

``` csharp
builder.Services.AddSingleton<ILogger, Logger>();
```

Use when: - Stateless - Thread-safe - Safe to share - Expensive to
create

Examples: - ILogger - IConfiguration - IMemoryCache - PasswordHasher

## Scoped

``` csharp
builder.Services.AddScoped<IRepository, Repository>();
```

Use when: - Uses DbContext - Holds request-specific state - Represents
one unit of work

Examples: - DbContext - Repository - Business Services

Why DbContext is Scoped: - Change Tracker - Not thread-safe - One
database unit of work per request

## Transient

``` csharp
builder.Services.AddTransient<IEmailFormatter, EmailFormatter>();
```

Use when: - Lightweight helper - Stateless - Fresh instance preferred

Examples: - EmailFormatter - CsvBuilder - PdfGenerator - TextParser

## Comparison

  Lifetime    Instance Count        Typical Use
  ----------- --------------------- ---------------------------------
  Singleton   One per application   Logger, Configuration, Cache
  Scoped      One per request       DbContext, Repository, Services
  Transient   New every resolve     Helper classes

## Decision Tree

1.  Uses DbContext?
    -   Yes → Scoped
2.  Holds request-specific state?
    -   Yes → Scoped
3.  Stateless and safe to share?
    -   Yes → Singleton
4.  Lightweight helper?
    -   Yes → Transient

## Lifetime Rule

Longer lifetime cannot depend on shorter lifetime.

Allowed: - Scoped → Scoped - Scoped → Singleton - Transient → Scoped -
Transient → Singleton - Singleton → Singleton

Not Allowed: - Singleton → Scoped

Example:

``` csharp
builder.Services.AddSingleton<EmployeeService>();
builder.Services.AddScoped<EmployeeRepository>();
```

This fails because EmployeeService lives for the application while
EmployeeRepository lives only for one request.

Typical exception:

    Cannot consume scoped service 'EmployeeRepository'
    from singleton 'EmployeeService'

## Interview Questions

### Difference between Singleton, Scoped and Transient?

Singleton: One instance for application.

Scoped: One instance per HTTP request.

Transient: New instance every resolution.

### Why is DbContext Scoped?

It is not thread-safe and represents one unit of work.

### Can Singleton depend on Scoped?

No.

### Can Scoped depend on Singleton?

Yes.

### Repository Lifetime?

Scoped.

### Business Service Lifetime?

Scoped.

## Quick Revision

-   Logger → Singleton
-   Configuration → Singleton
-   MemoryCache → Singleton
-   DbContext → Scoped
-   Repository → Scoped
-   Business Service → Scoped
-   EmailFormatter → Transient
-   PdfGenerator → Transient

Remember:

Anything using DbContext is almost always Scoped.

Stateless shared services are Singleton.

Lightweight helper classes are Transient.
