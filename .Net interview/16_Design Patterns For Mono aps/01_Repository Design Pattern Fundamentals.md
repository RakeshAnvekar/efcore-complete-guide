# Repository Design Pattern in ASP.NET Core

# README 1 – Repository Design Pattern Fundamentals

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is the Repository Pattern?
2. Why Do We Need a Repository?
3. Problems Without a Repository
4. Repository Architecture
5. Real-World Analogy
6. Creating a Repository
7. Repository Flow
8. Advantages
9. Repository vs DbContext
10. Common Interview Questions
11. Key Takeaways

---

# 1. What is the Repository Pattern?

The **Repository Pattern** is a design pattern that provides an abstraction layer between the application's business logic and the data source.

Instead of accessing the database directly, the application communicates with a repository.

Architecture

```text
Controller

      │

      ▼

Repository

      │

      ▼

DbContext

      │

      ▼

SQL Server
```

The Repository hides all database operations from the rest of the application.

---

# 2. Why Do We Need a Repository?

Suppose we are building an E-Commerce application.

Without a Repository

```text
OrderController

↓

DbContext

↓

SQL Server
```

Controller Example

```csharp
public class OrderController : ControllerBase
{
    private readonly AppDbContext _context;

    public OrderController(AppDbContext context)
    {
        _context = context;
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> Get(int id)
    {
        var order = await _context.Orders.FindAsync(id);

        return Ok(order);
    }
}
```

Initially this looks simple.

As the application grows, the controller starts containing:

* Complex LINQ queries
* Include statements
* Filtering
* Pagination
* Sorting
* Business validation

The controller becomes difficult to maintain.

---

# 3. Problems Without a Repository

## Problem 1 – Business Logic in Controllers

Example

```csharp
var order = await _context.Orders
    .Include(o => o.Customer)
    .Include(o => o.OrderItems)
    .FirstOrDefaultAsync(o => o.Id == id);
```

The controller now knows about:

* Entity Framework Core
* Database relationships
* Query implementation

Controllers should focus on handling HTTP requests, not data access.

---

## Problem 2 – Code Duplication

Suppose multiple controllers require order information.

```text
OrderController

↓

Orders Query
```

```text
InvoiceController

↓

Orders Query
```

```text
ReportController

↓

Orders Query
```

The same query is duplicated in several places.

Maintenance becomes difficult.

---

## Problem 3 – Tight Coupling

Architecture

```text
Controller

↓

DbContext

↓

SQL Server
```

The controller is tightly coupled to Entity Framework Core.

If the application later changes from EF Core to Dapper or another persistence technology, every controller may require modifications.

---

# 4. Repository Architecture

Introducing a Repository

```text
Client

↓

OrderController

↓

IOrderRepository

↓

OrderRepository

↓

AppDbContext

↓

SQL Server
```

Now:

* Controllers know only about repositories.
* Repositories know about Entity Framework Core.
* EF Core knows about SQL Server.

Responsibilities are clearly separated.

---

# 5. Real-World Analogy

Think of a restaurant.

Customer

↓

Waiter

↓

Kitchen

The customer never enters the kitchen.

The waiter accepts the request, communicates with the kitchen, and brings the food.

Repository plays the same role.

```text
Controller

↓

Repository

↓

Database
```

The controller requests data.

The repository decides how to retrieve it.

---

# 6. Creating a Repository

## Step 1 – Repository Interface

```csharp
public interface IOrderRepository
{
    Task<Order?> GetByIdAsync(int id);

    Task<List<Order>> GetAllAsync();

    Task AddAsync(Order order);

    Task UpdateAsync(Order order);

    Task DeleteAsync(int id);
}
```

The interface defines business-oriented data operations.

---

## Step 2 – Repository Implementation

```csharp
public class OrderRepository : IOrderRepository
{
    private readonly AppDbContext _context;

    public OrderRepository(AppDbContext context)
    {
        _context = context;
    }

    public async Task<Order?> GetByIdAsync(int id)
    {
        return await _context.Orders.FindAsync(id);
    }

    public async Task<List<Order>> GetAllAsync()
    {
        return await _context.Orders.ToListAsync();
    }

    public async Task AddAsync(Order order)
    {
        await _context.Orders.AddAsync(order);

        await _context.SaveChangesAsync();
    }

    public async Task UpdateAsync(Order order)
    {
        _context.Orders.Update(order);

        await _context.SaveChangesAsync();
    }

    public async Task DeleteAsync(int id)
    {
        var order = await _context.Orders.FindAsync(id);

        if(order == null)
            return;

        _context.Orders.Remove(order);

        await _context.SaveChangesAsync();
    }
}
```

The repository contains all persistence logic.

---

## Step 3 – Dependency Injection

Register the repository.

```csharp
builder.Services.AddScoped<IOrderRepository, OrderRepository>();
```

This ensures one repository instance per HTTP request.

---

## Step 4 – Using the Repository

```csharp
public class OrderController : ControllerBase
{
    private readonly IOrderRepository _repository;

    public OrderController(IOrderRepository repository)
    {
        _repository = repository;
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> Get(int id)
    {
        var order = await _repository.GetByIdAsync(id);

        return Ok(order);
    }
}
```

Notice that the controller no longer depends on `AppDbContext`.

---

# 7. Repository Request Flow

```text
Client

↓

OrderController

↓

IOrderRepository

↓

OrderRepository

↓

AppDbContext

↓

SQL Server

↓

Return Data

↓

Repository

↓

Controller

↓

Client
```

---

# 8. Advantages of Repository Pattern

## Separation of Concerns

Controllers handle HTTP requests.

Repositories handle database operations.

---

## Loose Coupling

Controllers depend on interfaces rather than Entity Framework Core.

---

## Centralized Data Access

Complex queries are written once and reused.

---

## Improved Testability

Controllers can be unit tested by mocking the repository.

Example

```csharp
var repositoryMock = new Mock<IOrderRepository>();
```

No database connection is required.

---

## Easier Maintenance

Changes to data access logic are isolated inside repositories.

---

# 9. Repository vs DbContext

Many interviewers ask:

> **Doesn't EF Core already implement the Repository Pattern?**

### DbSet<TEntity>

`DbSet<TEntity>` already provides repository-like operations such as:

* Add
* Update
* Remove
* Find
* Query

### DbContext

`DbContext` behaves similarly to the Unit of Work pattern by tracking entity changes and committing them using `SaveChanges()`.

### Why Create a Custom Repository?

A custom repository is useful when you want to:

* Hide EF Core from higher layers.
* Centralize complex queries.
* Enforce architectural boundaries.
* Improve testability.
* Reuse business-specific data access methods.

For very simple CRUD applications, using `DbContext` directly inside the application/service layer can also be a valid design choice.

---

# 10. Common Interview Questions

### What is the Repository Pattern?

A design pattern that abstracts data access behind an interface, separating business logic from persistence logic.

---

### Why use a Repository?

* Separation of concerns
* Loose coupling
* Reusability
* Testability
* Centralized data access

---

### Should Controllers access DbContext directly?

Small demo applications can.

Large enterprise applications usually prefer repositories or an application/service layer to keep controllers clean.

---

### Does EF Core already implement Repository and Unit of Work?

Yes.

* `DbSet<TEntity>` behaves like a repository.
* `DbContext` behaves like a Unit of Work.

Whether to add a custom repository depends on the application's complexity and architectural goals.

---

### Why inject IOrderRepository instead of OrderRepository?

Programming against an interface:

* Reduces coupling.
* Improves testability.
* Allows swapping implementations without changing controllers.

---

# 11. Key Takeaways

* The Repository Pattern abstracts data access.
* Controllers should not contain database queries.
* Repositories encapsulate persistence logic.
* Dependency Injection is used to inject repositories.
* Repository Pattern improves maintainability, reuse, and testability.
* EF Core already provides repository-like behavior through `DbSet<TEntity>` and Unit of Work through `DbContext`.
* Introduce custom repositories when they provide clear value, such as reusable complex queries or architectural separation—not simply because every project "must" have them.

---

# What's Next?

**README 2 – Generic Repository Pattern**

Topics include:

* Why one repository per entity becomes repetitive
* `IGenericRepository<T>`
* Generic CRUD methods
* Generic Repository implementation
* Advantages and disadvantages
* Why many enterprise applications combine Generic Repositories with specialized repositories
