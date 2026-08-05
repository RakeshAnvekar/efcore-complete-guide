# Entity Framework Core – Transactions
# Module 4 – TransactionScope

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 4 – TransactionScope

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is TransactionScope?
2. Why Do We Need TransactionScope?
3. Ambient Transaction
4. Internal Working
5. TransactionScope Lifecycle
6. Complete()
7. Rollback
8. Multiple DbContexts
9. Distributed Transactions
10. Async TransactionScope
11. TransactionScope vs BeginTransaction()
12. Real-Time Examples
13. Best Practices
14. Interview Questions
15. Summary
16. Key Takeaways

---

# What is TransactionScope?

TransactionScope is a .NET class that automatically creates a transaction scope.

Every database operation executed inside this scope automatically becomes part of the same transaction.

Unlike BeginTransaction(),

you don't manually call

- Begin Transaction
- Commit
- Rollback

TransactionScope manages them automatically.

---

# Why Do We Need TransactionScope?

Suppose an Order Processing System has

```
Order Service

↓

Payment Service

↓

Inventory Service
```

Each service uses a different repository.

```
Order Repository

↓

Payment Repository

↓

Inventory Repository
```

Each repository has its own DbContext.

Question

How do we make all these operations part of one transaction?

TransactionScope solves this problem.

---

# Example Without TransactionScope

```csharp
orderRepository.CreateOrder();

paymentRepository.CreatePayment();

inventoryRepository.UpdateStock();
```

Internally

```
DbContext A

↓

Transaction A

------------------------

DbContext B

↓

Transaction B

------------------------

DbContext C

↓

Transaction C
```

Suppose Inventory fails.

Database

```
Order Saved

Payment Saved

Inventory Failed
```

Database becomes inconsistent.

---

# Solution

```csharp
using(var scope = new TransactionScope())
{
    orderRepository.CreateOrder();

    paymentRepository.CreatePayment();

    inventoryRepository.UpdateStock();

    scope.Complete();
}
```

Now

Everything belongs to one transaction.

---

# What is an Ambient Transaction?

The most important concept of TransactionScope.

When TransactionScope starts,

.NET creates one global transaction.

```
Ambient Transaction
```

Every DbContext created inside this scope automatically joins the same transaction.

Visual

```
TransactionScope

↓

Ambient Transaction

↓

DbContext A

↓

DbContext B

↓

DbContext C
```

All participate in one transaction.

---

# Internal Working

```
Application

↓

TransactionScope Created

↓

Ambient Transaction Created

↓

Repository A Joins

↓

Repository B Joins

↓

Repository C Joins

↓

Complete()

↓

Commit
```

---

# Complete Example

```csharp
using System.Transactions;

using(var scope = new TransactionScope())
{
    orderRepository.CreateOrder();

    paymentRepository.CreatePayment();

    inventoryRepository.UpdateInventory();

    scope.Complete();
}
```

---

# TransactionScope Lifecycle

```
TransactionScope Starts

↓

Ambient Transaction Created

↓

Execute Database Operations

↓

Complete() Called?

↓

YES

↓

Commit

----------------------------

NO

↓

Rollback
```

---

# What does Complete() do?

Many developers think

```
Complete()

=

Commit()
```

This is not correct.

Complete() simply tells TransactionScope

```
Everything completed successfully.
```

When the TransactionScope object is disposed,

.NET commits the transaction.

---

# Example

```csharp
using(var scope = new TransactionScope())
{
    context.Orders.Add(order);

    context.SaveChanges();

    scope.Complete();
}
```

Result

```
Commit
```

---

# What Happens if Complete() is Missing?

```csharp
using(var scope = new TransactionScope())
{
    context.Orders.Add(order);

    context.SaveChanges();
}
```

No Complete()

Result

```
Rollback
```

Even though SaveChanges() succeeded,

the transaction is rolled back.

---

# Rollback

Suppose

```
Order Saved

↓

Payment Saved

↓

Inventory Failed
```

No Complete()

↓

TransactionScope automatically performs

```
Rollback
```

Database

```
Order Removed

↓

Payment Removed

↓

Inventory Restored
```

---

# Multiple DbContexts

Suppose

```csharp
using(var scope = new TransactionScope())
{
    using(var orderContext = new OrderDbContext())
    {
        orderContext.SaveChanges();
    }

    using(var paymentContext = new PaymentDbContext())
    {
        paymentContext.SaveChanges();
    }

    scope.Complete();
}
```

Both DbContexts automatically join the same transaction.

---

# Internal Flow

```
TransactionScope

↓

Ambient Transaction

↓

OrderDbContext

↓

PaymentDbContext

↓

InventoryDbContext

↓

Commit
```

---

# Distributed Transactions

Suppose

```
SQL Server A

↓

SQL Server B
```

TransactionScope can coordinate both databases.

```
Database A

↓

Database B

↓

Commit Together
```

This is called a

```
Distributed Transaction
```

---

# Important Note

Modern Microservices generally avoid Distributed Transactions because

- Slow
- Expensive
- Difficult to Scale
- Not supported by many cloud environments

Instead

```
Saga Pattern

+

Outbox Pattern
```

are preferred.

---

# Async TransactionScope

Wrong

```csharp
using(var scope = new TransactionScope())
{
    await context.SaveChangesAsync();

    scope.Complete();
}
```

This can fail because the transaction does not automatically flow across `await`.

Correct

```csharp
using var scope =
new TransactionScope(
TransactionScopeAsyncFlowOption.Enabled);

await context.SaveChangesAsync();

scope.Complete();
```

Always enable AsyncFlowOption when using async methods.

---

# TransactionScope vs BeginTransaction()

| BeginTransaction() | TransactionScope |
|--------------------|------------------|
| Manual Transaction | Ambient Transaction |
| One DbContext | Multiple DbContexts |
| One Database Connection | Multiple Connections (when supported) |
| Commit() | Complete() |
| Rollback() | Automatic Rollback if Complete() isn't called |
| Best for Single DbContext | Best for Multiple DbContexts |

---

# Real-Time Banking Example

Transfer ₹1000

Operations

```
Debit Account

↓

Credit Account

↓

Create Audit Record
```

Without TransactionScope

```
Transaction A

↓

Debit

------------------------

Transaction B

↓

Credit

------------------------

Transaction C

↓

Audit
```

If Audit fails

```
Database Inconsistent
```

---

With TransactionScope

```
TransactionScope

↓

Debit

↓

Credit

↓

Audit

↓

Complete()

↓

Commit
```

If any operation fails

```
Rollback

↓

Everything Restored
```

---

# Real-Time E-Commerce Example

Customer Places Order

Operations

```
Create Order

↓

Create Payment

↓

Reduce Inventory

↓

Generate Invoice
```

All repositories participate in the same TransactionScope.

```
Begin TransactionScope

↓

Order

↓

Payment

↓

Inventory

↓

Invoice

↓

Complete()

↓

Commit
```

If Invoice fails

```
Rollback

↓

Order Removed

↓

Payment Removed

↓

Inventory Restored
```

---

# Best Practices

✔ Use BeginTransaction() for a single DbContext.

✔ Use TransactionScope for multiple DbContexts.

✔ Always call Complete() after all operations succeed.

✔ Enable AsyncFlowOption for async code.

✔ Keep TransactionScope short-lived.

✔ Avoid external API calls inside TransactionScope.

✔ Avoid Distributed Transactions in Microservices.

✔ Prefer Saga Pattern for Microservices.

---

# Interview Questions

## What is TransactionScope?

TransactionScope is a .NET class that creates an Ambient Transaction, allowing multiple database operations to participate in the same transaction.

---

## What is an Ambient Transaction?

An Ambient Transaction is a transaction automatically available to every DbContext created inside a TransactionScope.

---

## What does Complete() do?

Complete() tells TransactionScope that every operation succeeded.

The actual Commit happens when the TransactionScope is disposed.

---

## What happens if Complete() is not called?

TransactionScope automatically rolls back the transaction.

---

## Difference between BeginTransaction() and TransactionScope?

BeginTransaction() works with a single DbContext and requires manual Commit/Rollback.

TransactionScope creates an Ambient Transaction that multiple DbContexts can automatically join.

---

## Should we use TransactionScope in Microservices?

Generally No.

Modern Microservices prefer

- Saga Pattern
- Outbox Pattern

instead of Distributed Transactions.

---

# Summary

TransactionScope is a higher-level transaction management mechanism provided by .NET.

It creates an Ambient Transaction that automatically flows to multiple DbContexts and repositories.

Developers only need to call Complete() when every operation succeeds.

If Complete() is not called, TransactionScope automatically rolls back every operation.

TransactionScope is excellent for monolithic applications with multiple repositories but is generally avoided in Microservice architectures in favor of Saga and Outbox patterns.

---

# Key Takeaways

✔ TransactionScope creates an Ambient Transaction.

✔ Multiple DbContexts automatically join the same transaction.

✔ Complete() indicates success.

✔ Missing Complete() causes automatic Rollback.

✔ Supports multiple repositories.

✔ Supports Distributed Transactions (where available).

✔ Enable TransactionScopeAsyncFlowOption.Enabled for async code.

✔ Prefer BeginTransaction() for a single DbContext.

✔ Prefer Saga Pattern for Microservices.

---

