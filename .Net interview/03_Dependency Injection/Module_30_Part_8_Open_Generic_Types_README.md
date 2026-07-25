# Module 30 -- Dependency Injection

# Part 8 -- Open Generic Types (`IRepository<T>`)

## Learning Objectives

-   Understand Generic Repositories
-   Open vs Closed Generic Types
-   Register Open Generics with Dependency Injection
-   How ASP.NET Core resolves generic services
-   Real-world EF Core example
-   Interview questions

------------------------------------------------------------------------

# Problem

Without generics:

``` csharp
EmployeeRepository
CustomerRepository
OrderRepository
```

Each repository contains almost identical CRUD methods.

This causes code duplication.

------------------------------------------------------------------------

# Solution -- Generic Repository

## Generic Interface

``` csharp
public interface IRepository<T>
{
    void Add(T entity);
    void Update(T entity);
    void Delete(T entity);
    T GetById(int id);
}
```

## Generic Repository

``` csharp
public class Repository<T> : IRepository<T>
{
    public void Add(T entity) { }

    public void Update(T entity) { }

    public void Delete(T entity) { }

    public T GetById(int id)
    {
        return default;
    }
}
```

One repository can now work with any entity.

------------------------------------------------------------------------

# Open Generic Type

An open generic does not specify the type parameter.

Examples:

``` csharp
IRepository<>
Repository<>
```

Think of it as a template.

------------------------------------------------------------------------

# Closed Generic Type

A closed generic specifies the type parameter.

Examples:

``` csharp
IRepository<Employee>

IRepository<Customer>

IRepository<Order>
```

The generic type parameter `T` has been replaced with a real type.

------------------------------------------------------------------------

# Register Open Generic Types

``` csharp
builder.Services.AddScoped(
    typeof(IRepository<>),
    typeof(Repository<>));
```

This registers the generic template with the DI container.

------------------------------------------------------------------------

# How DI Resolves It

Suppose a service requests:

``` csharp
public class EmployeeService
{
    public EmployeeService(IRepository<Employee> repository)
    {
    }
}
```

The DI container performs these steps:

``` text
Need IRepository<Employee>

        ↓

Check registration

        ↓

Find IRepository<>

        ↓

Replace T with Employee

        ↓

Create Repository<Employee>

        ↓

Inject into EmployeeService
```

No explicit registration of `Repository<Employee>` is required.

------------------------------------------------------------------------

# Another Example

``` csharp
public class OrderService
{
    public OrderService(IRepository<Order> repository)
    {
    }
}
```

The DI container automatically creates:

``` text
Repository<Order>
```

------------------------------------------------------------------------

# Real EF Core Example

``` csharp
public class Repository<T> : IRepository<T>
    where T : class
{
    private readonly ApplicationDbContext _context;

    public Repository(ApplicationDbContext context)
    {
        _context = context;
    }

    public void Add(T entity)
    {
        _context.Set<T>().Add(entity);
    }

    public void Update(T entity)
    {
        _context.Set<T>().Update(entity);
    }

    public void Delete(T entity)
    {
        _context.Set<T>().Remove(entity);
    }

    public IQueryable<T> GetAll()
    {
        return _context.Set<T>();
    }
}
```

------------------------------------------------------------------------

# What Does Set`<T>`{=html}() Do?

``` text
_context.Set<Employee>()

↓

DbSet<Employee>
```

``` text
_context.Set<Order>()

↓

DbSet<Order>
```

`Set<T>()` returns the `DbSet<T>` for the specified entity type.

------------------------------------------------------------------------

# Benefits

-   Reduces code duplication
-   Reusable CRUD logic
-   Easier maintenance
-   Automatic DI resolution
-   Consistent implementation

------------------------------------------------------------------------

# Limitations

Generic repositories are ideal for common CRUD operations.

Entity-specific queries should be placed in dedicated repositories or
services.

Example:

``` csharp
GetEmployeesByDepartment()
```

This belongs in an Employee-specific repository or service.

------------------------------------------------------------------------

# Open vs Closed Generic

  Open Generic      Closed Generic
  ----------------- --------------------------------
  IRepository\<\>   IRepository`<Employee>`{=html}
  Repository\<\>    Repository`<Order>`{=html}
  Template          Concrete Type

------------------------------------------------------------------------

# Interview Questions

## What is an Open Generic?

A generic type without a concrete type parameter.

Example:

``` csharp
IRepository<>
```

------------------------------------------------------------------------

## What is a Closed Generic?

A generic type with a concrete type parameter.

Example:

``` csharp
IRepository<Employee>
```

------------------------------------------------------------------------

## Why use

``` csharp
builder.Services.AddScoped(
    typeof(IRepository<>),
    typeof(Repository<>));
```

Because it registers the generic template so the DI container can
automatically create closed generic types when requested.

------------------------------------------------------------------------

## Does ASP.NET Core automatically create Repository`<Employee>`{=html}?

Yes.

When `IRepository<Employee>` is requested, the DI container creates
`Repository<Employee>` from the open generic registration.

------------------------------------------------------------------------

## What does DbContext.Set`<T>`{=html}() return?

It returns the `DbSet<T>` corresponding to the specified entity type.

------------------------------------------------------------------------

# Quick Revision

✅ Open Generic = Template

``` csharp
IRepository<>
```

✅ Closed Generic = Concrete Type

``` csharp
IRepository<Employee>
```

✅ Register once

``` csharp
builder.Services.AddScoped(
    typeof(IRepository<>),
    typeof(Repository<>));
```

✅ DI automatically creates:

-   Repository`<Employee>`{=html}
-   Repository`<Customer>`{=html}
-   Repository`<Order>`{=html}

without additional registrations.

------------------------------------------------------------------------

# Key Takeaways

-   Use open generic registration for reusable services.
-   Avoid creating duplicate repositories for every entity.
-   Use `DbContext.Set<T>()` to access entity sets generically.
-   Create dedicated repositories only for entity-specific business
    logic.
