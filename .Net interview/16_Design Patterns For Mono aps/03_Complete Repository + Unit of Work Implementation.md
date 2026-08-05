# Repository + Unit of Work

# README 2 – Dependency Injection & Shared DbContext

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. Why Dependency Injection Matters
2. Repository Registration
3. Unit of Work Registration
4. What Happens During an HTTP Request?
5. How Repositories Share the Same DbContext
6. Scoped vs Transient vs Singleton
7. Why DbContext Should Be Scoped
8. What Happens if DbContext is Transient?
9. Complete Execution Flow
10. Common Interview Questions
11. Key Takeaways

---

# 1. Why Dependency Injection Matters

The Repository and Unit of Work patterns rely heavily on **Dependency Injection (DI)**.

DI is responsible for:

* Creating objects
* Managing their lifetime
* Injecting dependencies
* Ensuring repositories share the same `DbContext`

Without DI, you would manually create every object.

Example (Not Recommended)

```csharp
var context = new AppDbContext();

var orderRepo = new OrderRepository(context);

var paymentRepo = new PaymentRepository(context);

var inventoryRepo = new InventoryRepository(context);

var unitOfWork = new UnitOfWork(
    context,
    orderRepo,
    inventoryRepo,
    paymentRepo);
```

Instead, ASP.NET Core creates everything automatically.

---

# 2. Repository Registration

Register repositories.

```csharp
builder.Services.AddScoped<IOrderRepository, OrderRepository>();

builder.Services.AddScoped<IInventoryRepository, InventoryRepository>();

builder.Services.AddScoped<IPaymentRepository, PaymentRepository>();

builder.Services.AddScoped<IShippingRepository, ShippingRepository>();
```

Meaning

Whenever an application asks for:

```text
IOrderRepository
```

ASP.NET Core creates

```text
OrderRepository
```

The same applies to all repositories.

---

# 3. Unit of Work Registration

```csharp
builder.Services.AddScoped<IUnitOfWork, UnitOfWork>();
```

Whenever an application requests

```text
IUnitOfWork
```

ASP.NET Core creates

```text
UnitOfWork
```

---

# 4. What Happens During an HTTP Request?

Suppose a client sends:

```http
POST /api/checkout
```

The controller requires

```csharp
public CheckoutController(IUnitOfWork unitOfWork)
{
}
```

Execution

```text
HTTP Request

↓

Controller Needs IUnitOfWork

↓

Dependency Injection

↓

Create UnitOfWork
```

---

# 5. How Repositories Share the Same DbContext

Suppose UnitOfWork is constructed like this:

```csharp
public UnitOfWork(
    AppDbContext context,
    IOrderRepository orderRepository,
    IInventoryRepository inventoryRepository,
    IPaymentRepository paymentRepository,
    IShippingRepository shippingRepository)
{
}
```

Each repository also depends on `AppDbContext`.

Example

```csharp
public OrderRepository(AppDbContext context)
{
    _context = context;
}
```

```csharp
public InventoryRepository(AppDbContext context)
{
    _context = context;
}
```

Question

Will ASP.NET Core create four different DbContexts?

**No.**

Because `AppDbContext` is registered as **Scoped**.

For one HTTP request

```text
HTTP Request

↓

Create DbContext #1

↓

OrderRepository

↓

DbContext #1

↓

InventoryRepository

↓

DbContext #1

↓

PaymentRepository

↓

DbContext #1

↓

ShippingRepository

↓

DbContext #1

↓

UnitOfWork

↓

DbContext #1
```

Every repository shares the same DbContext instance.

---

# 6. Scoped vs Transient vs Singleton

## Scoped

```csharp
builder.Services.AddScoped<AppDbContext>();
```

Creates one instance per HTTP request.

Example

```text
Request 1

↓

DbContext #1
```

```text
Request 2

↓

DbContext #2
```

Recommended for `DbContext`.

---

## Transient

```csharp
builder.Services.AddTransient<OrderRepository>();
```

Creates a new repository object every time it is requested.

If `DbContext` is still Scoped, the repositories will still receive the same `DbContext` instance for the current request.

---

## Singleton

```csharp
builder.Services.AddSingleton<MyService>();
```

Creates only one instance for the application's lifetime.

**Never register `DbContext` as Singleton.**

`DbContext` is **not thread-safe** and should not be shared across requests.

---

# 7. Why DbContext Should Be Scoped

A single HTTP request represents one **Unit of Work**.

Example

```text
Customer Places Order

↓

Create Order

↓

Reduce Inventory

↓

Create Payment

↓

Create Shipment

↓

SaveChanges()
```

All operations should use the same `DbContext`.

Benefits

* One Change Tracker
* One Transaction
* One SaveChanges()
* Consistent database state

---

# 8. What Happens if DbContext is Transient?

Suppose

```csharp
builder.Services.AddTransient<AppDbContext>();
```

Now every repository receives a different DbContext.

```text
OrderRepository

↓

DbContext #1

----------------

InventoryRepository

↓

DbContext #2

----------------

PaymentRepository

↓

DbContext #3
```

Problems

* Separate Change Trackers
* Separate transactions
* Unit of Work cannot coordinate all changes
* Data consistency issues

This breaks the purpose of the Unit of Work pattern.

---

# 9. Complete Execution Flow

```text
HTTP Request

        │

        ▼

Dependency Injection

        │

Create Scoped DbContext

        │

        ▼

Create OrderRepository

        │

Uses DbContext #1

        ▼

Create InventoryRepository

        │

Uses DbContext #1

        ▼

Create PaymentRepository

        │

Uses DbContext #1

        ▼

Create ShippingRepository

        │

Uses DbContext #1

        ▼

Create UnitOfWork

        │

Coordinates All Repositories

        ▼

CheckoutService

        │

Repository Operations

        ▼

DbContext Change Tracker

        │

SaveChanges()

        ▼

SQL Server
```

---

# 10. Common Interview Questions

### Why is DbContext registered as Scoped?

Because one HTTP request should use one DbContext instance so all repositories share the same Change Tracker and transaction.

---

### Can repositories be registered as Transient?

Yes.

If `DbContext` remains Scoped, all repository instances created during the same request still receive the same `DbContext`.

---

### What happens if DbContext is Transient?

Each repository may receive a different DbContext, resulting in multiple Change Trackers and breaking the Unit of Work concept.

---

### Why shouldn't DbContext be Singleton?

Because:

* It is not thread-safe.
* It keeps entity tracking state.
* Sharing it across requests can cause concurrency problems and stale tracked entities.

---

### Why do all repositories need the same DbContext?

Because all changes should be tracked together and committed together using one `SaveChanges()` call.

---

# 11. Key Takeaways

* Dependency Injection automatically creates repositories and Unit of Work.
* All repositories should share the same `DbContext`.
* `DbContext` should normally be registered as **Scoped**.
* Scoped means one `DbContext` per HTTP request.
* Repository lifetime can be Scoped or Transient, provided the injected `DbContext` remains Scoped.
* `DbContext` should not be Singleton.
* Sharing one `DbContext` enables EF Core's Change Tracker to collect changes from multiple repositories and commit them together.

---
