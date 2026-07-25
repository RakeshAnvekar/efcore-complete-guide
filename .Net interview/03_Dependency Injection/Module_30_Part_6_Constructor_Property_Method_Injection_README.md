# Module 30 -- Dependency Injection

# Part 6 -- Constructor Injection vs Property Injection vs Method Injection

## Learning Objectives

After this module you will understand:

-   What Dependency Injection (DI) is
-   Constructor Injection
-   Property Injection
-   Method Injection
-   Why ASP.NET Core prefers Constructor Injection
-   Pros and Cons of each approach
-   Real-world examples
-   Interview questions

------------------------------------------------------------------------

# What is Dependency Injection?

Dependency Injection means:

> A class receives its dependencies from the outside instead of creating
> them itself.

## Bad Example (Tightly Coupled)

``` csharp
public class EmployeeService
{
    private readonly EmployeeRepository _repository;

    public EmployeeService()
    {
        _repository = new EmployeeRepository();
    }
}
```

Problems:

-   Tight coupling
-   Hard to unit test
-   Cannot replace implementation easily

------------------------------------------------------------------------

## Good Example (Dependency Injection)

``` csharp
public class EmployeeService
{
    private readonly EmployeeRepository _repository;

    public EmployeeService(EmployeeRepository repository)
    {
        _repository = repository;
    }
}
```

The DI container creates the repository and injects it.

------------------------------------------------------------------------

# Three Types of Injection

1.  Constructor Injection ⭐⭐⭐⭐⭐ (Recommended)
2.  Property Injection ⭐⭐
3.  Method Injection ⭐⭐⭐

------------------------------------------------------------------------

# 1. Constructor Injection

``` csharp
public class EmployeeService
{
    private readonly EmployeeRepository _repository;

    public EmployeeService(EmployeeRepository repository)
    {
        _repository = repository;
    }
}
```

## How it works

``` text
HTTP Request
      ↓
DI Container
      ↓
Create EmployeeRepository
      ↓
Pass it into EmployeeService constructor
      ↓
Return EmployeeService
```

## Advantages

-   Required dependencies are guaranteed
-   Supports readonly fields
-   Prevents partially initialized objects
-   Easy to unit test
-   Recommended by Microsoft
-   Default pattern in ASP.NET Core

## Real Project Example

``` text
Controller
      ↓
Service
      ↓
Repository
      ↓
DbContext
```

The DI container builds the complete object graph automatically.

------------------------------------------------------------------------

# 2. Property Injection

``` csharp
public class EmployeeService
{
    public EmployeeRepository Repository { get; set; }
}
```

Later:

``` csharp
service.Repository = repository;
```

## Problem

If the property is never assigned:

``` csharp
service.Repository.GetEmployees();
```

Result:

``` text
NullReferenceException
```

## Advantages

-   Useful for optional dependencies
-   Simple in special scenarios

## Disadvantages

-   Dependency can be forgotten
-   Object may be invalid
-   Harder to reason about
-   Not recommended for required dependencies

------------------------------------------------------------------------

# 3. Method Injection

``` csharp
public class ReportGenerator
{
    public void Generate(IPdfService pdfService)
    {
        pdfService.Create();
    }
}
```

The dependency is supplied only to the method that needs it.

## Advantages

-   Dependency is available only where needed
-   Keeps the class lightweight
-   Good for occasional operations

## When to use

-   Utility methods
-   Optional operations
-   One-time dependencies

------------------------------------------------------------------------

# Comparison

  Feature                 Constructor   Property   Method
  ----------------------- ------------- ---------- --------------------
  Required Dependency     ✅            ❌         ❌
  Null Safe               ✅            ❌         ✅
  Immutable               ✅            ❌         ✅
  Easy to Unit Test       ✅            ⚠️         ✅
  Microsoft Recommended   ✅            ❌         Specific Scenarios

------------------------------------------------------------------------

# Why Constructor Injection is Best

-   Dependencies are mandatory
-   Dependencies cannot be changed accidentally
-   Easy to mock during unit testing
-   Prevents NullReferenceException
-   Dependencies are visible from the constructor
-   Works seamlessly with ASP.NET Core DI

------------------------------------------------------------------------

# Common Mistake

``` csharp
public class EmployeeService
{
    public EmployeeService()
    {
        var repository = new EmployeeRepository();
    }
}
```

Problems:

-   Breaks Dependency Injection
-   Tight coupling
-   Difficult to test
-   Hard to replace implementation

------------------------------------------------------------------------

# Real-Life Analogy

## Constructor Injection

Buying a car with the engine already installed.

Ready to drive.

## Property Injection

Buying a car without an engine and expecting someone to install it
later.

Risky.

## Method Injection

Borrowing a trailer only when transporting goods.

No need to keep it permanently.

------------------------------------------------------------------------

# Interview Questions

## Which injection type does ASP.NET Core recommend?

Constructor Injection.

------------------------------------------------------------------------

## Why?

-   Mandatory dependencies
-   Better testability
-   Immutable dependencies
-   Safer object creation
-   Microsoft recommended

------------------------------------------------------------------------

## When should Property Injection be used?

Only for optional dependencies.

------------------------------------------------------------------------

## When should Method Injection be used?

When only one method needs a dependency.

------------------------------------------------------------------------

## Why is using new inside a service a bad practice?

Because it creates tight coupling, bypasses the DI container, and makes
testing difficult.

------------------------------------------------------------------------

# Quick Revision

✅ Constructor Injection - Default in ASP.NET Core - Best choice -
Required dependencies - Readonly fields - Easy testing

⚠️ Property Injection - Optional dependencies - Can lead to
NullReferenceException

✅ Method Injection - Temporary dependencies - Use only where required

------------------------------------------------------------------------

# Key Takeaways

-   Prefer Constructor Injection for almost all services.
-   Avoid creating dependencies using `new`.
-   Use Property Injection only for optional dependencies.
-   Use Method Injection when a dependency is needed only for a single
    operation.
