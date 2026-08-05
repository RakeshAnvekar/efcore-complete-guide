# Unit of Work Pattern in ASP.NET Core

# README 1 – Unit of Work Pattern Fundamentals

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is the Unit of Work Pattern?
2. Why Do We Need Unit of Work?
3. The Problem Without Unit of Work
4. How Unit of Work Solves the Problem
5. Repository vs Unit of Work
6. Real-World Analogy
7. Unit of Work Architecture
8. IUnitOfWork Interface
9. UnitOfWork Implementation
10. Advantages
11. DbContext vs Unit of Work
12. Common Interview Questions
13. Key Takeaways

---

# 1. What is the Unit of Work Pattern?

The **Unit of Work (UoW)** pattern coordinates multiple database operations and commits them as **one logical transaction**.

Instead of saving changes after every repository operation, all changes are collected and committed together.

Architecture

```text
Checkout Service

        │

        ▼

    Unit Of Work

        │

 ┌──────┼────────────┬──────────────┐

 ▼      ▼            ▼              ▼

OrderRepo InventoryRepo PaymentRepo ShippingRepo

        │

        ▼

     AppDbContext

        │

        ▼

     SQL Server
```

The Unit of Work ensures all operations succeed or fail together.

---

# 2. Why Do We Need Unit of Work?

Consider an e-commerce checkout process.

When a customer places an order, the application must:

1. Create Order
2. Reduce Inventory
3. Create Payment Record
4. Create Shipment

These are four separate database operations, but from the business perspective they represent **one business transaction**.

---

# 3. The Problem Without Unit of Work

Suppose every repository calls:

```csharp
await _context.SaveChangesAsync();
```

independently.

Example

### Order Repository

```csharp
public async Task AddAsync(Order order)
{
    await _context.Orders.AddAsync(order);

    await _context.SaveChangesAsync();
}
```

### Inventory Repository

```csharp
public async Task UpdateAsync(Product product)
{
    _context.Products.Update(product);

    await _context.SaveChangesAsync();
}
```

### Payment Repository

```csharp
public async Task AddAsync(Payment payment)
{
    await _context.Payments.AddAsync(payment);

    await _context.SaveChangesAsync();
}
```

---

## Failure Scenario

Execution

```text
Create Order

↓

Success

↓

Reduce Inventory

↓

Success

↓

Create Payment

↓

Exception

↓

Shipping Not Executed
```

Database State

```text
Orders

✔ Saved

Inventory

✔ Updated

Payments

✘ Missing

Shipment

✘ Missing
```

The database is now inconsistent.

---

# 4. How Unit of Work Solves the Problem

Instead of saving changes inside each repository, repositories only prepare changes.

Example

```csharp
public async Task AddAsync(Order order)
{
    await _context.Orders.AddAsync(order);
}
```

No `SaveChanges()`.

---

Inventory

```csharp
public Task UpdateAsync(Product product)
{
    _context.Products.Update(product);

    return Task.CompletedTask;
}
```

Again, no `SaveChanges()`.

---

Payment

```csharp
public async Task AddAsync(Payment payment)
{
    await _context.Payments.AddAsync(payment);
}
```

Still no `SaveChanges()`.

---

Finally

```csharp
await unitOfWork.SaveChangesAsync();
```

Only one commit.

Execution Flow

```text
Checkout Service

↓

Order Repository

↓

Inventory Repository

↓

Payment Repository

↓

Shipping Repository

↓

Unit Of Work

↓

SaveChanges()

↓

SQL Server
```

If any operation fails before `SaveChanges()`, nothing is committed.

---

# 5. Repository vs Unit of Work

## Repository

Responsible for **one entity's data access**.

Example

```text
Order Repository

↓

Orders Table
```

---

## Unit of Work

Responsible for coordinating multiple repositories.

Example

```text
Order Repository

Inventory Repository

Payment Repository

Shipping Repository

↓

One SaveChanges()

↓

One Transaction
```

Repositories perform operations.

Unit of Work commits them.

---

# 6. Real-World Analogy

Imagine buying groceries.

Cashier scans

```text
Milk

Rice

Bread

Oil
```

The store does **not** charge after each item.

Instead

```text
Scan All Items

↓

Calculate Total

↓

Single Payment
```

Unit of Work behaves the same way.

Multiple operations

↓

One Commit

---

# 7. Unit of Work Architecture

```text
Controller

      │

      ▼

Checkout Service

      │

      ▼

Unit Of Work

      │

 ┌────┼───────────┬─────────────┐

 ▼    ▼           ▼             ▼

OrderRepo InventoryRepo PaymentRepo ShippingRepo

      │

      ▼

AppDbContext

      │

      ▼

SQL Server
```

All repositories share the same `DbContext`.

---

# 8. IUnitOfWork Interface

Example

```csharp
public interface IUnitOfWork
{
    Task<int> SaveChangesAsync();

    Task BeginTransactionAsync();

    Task CommitAsync();

    Task RollbackAsync();
}
```

Responsibilities

* Save changes
* Begin transaction
* Commit transaction
* Rollback transaction

---

# 9. UnitOfWork Implementation

Example

```csharp
public class UnitOfWork : IUnitOfWork
{
    private readonly AppDbContext _context;

    public UnitOfWork(AppDbContext context)
    {
        _context = context;
    }

    public async Task<int> SaveChangesAsync()
    {
        return await _context.SaveChangesAsync();
    }
}
```

The Unit of Work delegates persistence to the shared `DbContext`.

---

# 10. Advantages

## Single Transaction

All changes succeed or fail together.

---

## Database Consistency

Avoids partially completed business operations.

---

## Coordinates Multiple Repositories

Repositories work together using the same `DbContext`.

---

## Centralized SaveChanges()

Only one place performs the commit.

---

## Easier Transaction Management

Business services no longer manage multiple commits.

---

## Better Maintainability

Transaction logic is centralized.

---

# 11. DbContext vs Unit of Work

One of the most common interview questions.

## Does EF Core already implement Unit of Work?

**Yes.**

`DbContext` already:

* Tracks entity changes.
* Groups multiple changes.
* Commits changes using `SaveChanges()`.
* Supports transactions.

Therefore `DbContext` behaves as a Unit of Work.

---

## Why Create a Custom Unit of Work?

A custom Unit of Work may be useful to:

* Hide EF Core from higher layers.
* Coordinate multiple repositories.
* Standardize transaction handling.
* Improve testability.
* Enforce architectural boundaries.

Not every application requires one.

---

# 12. Common Interview Questions

### What is the Unit of Work Pattern?

A pattern that groups multiple database operations into a single transaction and commits them together.

---

### Why use Unit of Work?

* Maintain consistency.
* Coordinate multiple repositories.
* Commit once.
* Support rollback.
* Simplify transaction management.

---

### Should repositories call SaveChanges()?

No.

Repositories prepare changes.

The Unit of Work commits all changes.

---

### What happens if one operation fails?

The transaction is rolled back, preventing partial updates.

---

### Does EF Core already implement Unit of Work?

Yes.

`DbContext` already provides Unit of Work behavior.

---

### What is the relationship between Repository and Unit of Work?

Repository handles data access for a specific entity.

Unit of Work coordinates multiple repositories and commits their changes as one transaction.

---

# 13. Key Takeaways

* Unit of Work coordinates multiple repository operations.
* All changes are committed together using a single `SaveChanges()`.
* Repositories should not call `SaveChanges()` directly.
* A Unit of Work helps maintain database consistency.
* It is especially useful for business operations involving multiple entities.
* `DbContext` already behaves as a Unit of Work in EF Core.
* Create a custom Unit of Work only when it provides architectural or business value.

---


