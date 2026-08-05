# Entity Framework Core – Transactions
# Module 3 – Explicit Transactions

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 3 – Explicit Transactions

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What are Explicit Transactions?
2. Why Do We Need Explicit Transactions?
3. Implicit vs Explicit Transactions
4. BeginTransaction()
5. Commit()
6. Rollback()
7. Multiple SaveChanges()
8. Internal Working
9. Async Transactions
10. Real-Time Examples
11. Best Practices
12. Interview Questions
13. Summary
14. Key Takeaways

---

# What is an Explicit Transaction?

An Explicit Transaction is a transaction that is **created and controlled by the developer**.

Unlike implicit transactions, Entity Framework Core does **not** automatically commit or roll back the transaction.

The developer decides

- When the transaction starts
- When it commits
- When it rolls back

---

# Why Do We Need Explicit Transactions?

Suppose an Order Processing system performs the following operations.

```
Create Order

↓

Create Payment

↓

Reduce Inventory

↓

Generate Invoice
```

If every operation uses its own SaveChanges(),

each SaveChanges() creates a separate transaction.

This can leave the database in an inconsistent state if one operation fails.

---

# Example Without Explicit Transaction

```csharp
context.Orders.Add(order);
context.SaveChanges();

context.Payments.Add(payment);
context.SaveChanges();

context.Inventory.Update(stock);
context.SaveChanges();
```

Transaction Flow

```
SaveChanges()

↓

Transaction 1

↓

Commit

---------------------

SaveChanges()

↓

Transaction 2

↓

Commit

---------------------

SaveChanges()

↓

Transaction 3

↓

Commit
```

Three SaveChanges()

↓

Three Transactions

---

# Problem

Suppose

```
Order Saved

↓

Payment Saved

↓

Inventory Update Failed
```

Database

```
Order

Saved

Payment

Saved

Inventory

Not Updated
```

Database is inconsistent.

---

# Solution

Use one Explicit Transaction.

```
Begin Transaction

↓

Order

↓

Payment

↓

Inventory

↓

Commit
```

If anything fails

```
Rollback

↓

Everything Restored
```

---

# BeginTransaction()

Start a transaction manually.

```csharp
using var transaction =
context.Database.BeginTransaction();
```

This tells SQL Server

```
Begin Transaction
```

Nothing is committed yet.

---

# Complete Example

```csharp
using var transaction =
context.Database.BeginTransaction();

try
{
    context.Orders.Add(order);
    context.SaveChanges();

    context.Payments.Add(payment);
    context.SaveChanges();

    context.Inventory.Update(stock);
    context.SaveChanges();

    transaction.Commit();
}
catch(Exception)
{
    transaction.Rollback();

    throw;
}
```

---

# Internal Working

```
Application

↓

BeginTransaction()

↓

SaveChanges()

↓

INSERT Order

↓

SaveChanges()

↓

INSERT Payment

↓

SaveChanges()

↓

UPDATE Inventory

↓

Success?

↓

YES

↓

Commit()

----------------------------

NO

↓

Rollback()
```

Although SaveChanges() is called three times,

there is only **one database transaction**.

---

# Commit()

Commit permanently saves all operations.

```csharp
transaction.Commit();
```

Meaning

```
Everything is successful.

Save changes permanently.
```

Database

```
Order

Saved

Payment

Saved

Inventory

Updated
```

---

# Rollback()

Rollback undoes every operation.

```csharp
transaction.Rollback();
```

Suppose

```
Order Saved

↓

Payment Saved

↓

Inventory Failed
```

Rollback restores

```
Order

Removed

↓

Payment

Removed

↓

Inventory

Original Value
```

Database returns to its previous state.

---

# Real-Time Banking Example

Transfer ₹1000

Without Explicit Transaction

```
Debit Account

↓

SaveChanges()

↓

Commit

-----------------------

Credit Account

↓

SaveChanges()

↓

Failure
```

Database

```
Account A

₹9000

Account B

₹5000
```

₹1000 is lost.

---

# Banking Solution

```csharp
using var transaction =
context.Database.BeginTransaction();

try
{
    accountA.Balance -= 1000;

    context.SaveChanges();

    accountB.Balance += 1000;

    context.SaveChanges();

    transaction.Commit();
}
catch
{
    transaction.Rollback();
}
```

If Credit fails

```
Rollback

↓

Account A

₹10000

↓

Account B

₹5000
```

Money is never lost.

---

# Why Multiple SaveChanges()?

Sometimes we need the generated primary key.

Example

```csharp
var order = new Order
{
    CustomerName = "Rakesh"
};

context.Orders.Add(order);

context.SaveChanges();
```

Now

```
Order.Id

Generated
```

Use Order.Id

```csharp
payment.OrderId = order.Id;

context.Payments.Add(payment);

context.SaveChanges();
```

Without Explicit Transaction,

these are two separate transactions.

With Explicit Transaction,

both SaveChanges() belong to one transaction.

---

# Async Version

```csharp
await using var transaction =
    await context.Database.BeginTransactionAsync();

try
{
    context.Orders.Add(order);

    await context.SaveChangesAsync();

    context.Payments.Add(payment);

    await context.SaveChangesAsync();

    await transaction.CommitAsync();
}
catch
{
    await transaction.RollbackAsync();

    throw;
}
```

Recommended for ASP.NET Core applications.

---

# Internal Flow Diagram

```
Application

↓

DbContext

↓

BeginTransaction()

↓

SaveChanges()

↓

SQL Statement 1

↓

SaveChanges()

↓

SQL Statement 2

↓

SaveChanges()

↓

SQL Statement 3

↓

Everything Successful?

↓

YES

↓

Commit()

----------------------------

NO

↓

Rollback()
```

---

# Real-Time E-Commerce Example

Customer places an order.

Operations

```
Insert Order

↓

Insert Payment

↓

Reduce Inventory

↓

Generate Invoice
```

With Explicit Transaction

```
Begin Transaction

↓

Insert Order

↓

Insert Payment

↓

Reduce Inventory

↓

Generate Invoice

↓

Commit
```

If Invoice generation fails

```
Rollback

↓

Order Removed

↓

Payment Removed

↓

Inventory Restored
```

Everything returns to the original state.

---

# Implicit vs Explicit Transactions

| Feature | Implicit Transaction | Explicit Transaction |
|----------|----------------------|----------------------|
| Created By | Entity Framework Core | Developer |
| BeginTransaction() Required | No | Yes |
| Commit() Required | No | Yes |
| Rollback() Required | No | Yes |
| Number of SaveChanges() | One | One or Many |
| Control | Automatic | Manual |
| Best For | Simple CRUD | Complex Business Operations |

---

# When Should We Use Explicit Transactions?

Use Explicit Transactions when

✔ Multiple SaveChanges()

✔ Banking Applications

✔ Payment Processing

✔ Order Processing

✔ Inventory Management

✔ Multiple Repository Calls

✔ Complex Business Logic

---

# Best Practices

✔ Keep transactions as short as possible.

✔ Commit only after every operation succeeds.

✔ Rollback immediately if an exception occurs.

✔ Avoid calling external APIs inside a transaction.

✔ Prefer SaveChangesAsync() in ASP.NET Core.

✔ Always wrap transactions inside try-catch blocks.

---

# Interview Questions

## What is an Explicit Transaction?

An Explicit Transaction is a transaction manually created and controlled by the developer using BeginTransaction().

---

## Why do we need Explicit Transactions?

To execute multiple SaveChanges() or multiple database operations as one logical unit.

---

## Can multiple SaveChanges() belong to one transaction?

Yes.

If they are inside the same Explicit Transaction.

---

## What happens if one SaveChanges() fails?

Rollback() restores the database to its previous state.

---

## Difference between Implicit and Explicit Transactions?

Implicit transactions are automatically managed by Entity Framework Core.

Explicit transactions are manually managed by the developer.

---

# Summary

Explicit Transactions provide complete control over database transactions.

They allow multiple SaveChanges() calls to execute as one business transaction.

If every operation succeeds, Commit() permanently saves the changes.

If any operation fails, Rollback() restores the database to its original state.

Explicit Transactions are commonly used in banking systems, payment gateways, inventory management, and enterprise applications where multiple database operations must either succeed together or fail together.

---

# Key Takeaways

✔ Explicit Transactions are manually controlled.

✔ BeginTransaction() starts the transaction.

✔ Commit() permanently saves all changes.

✔ Rollback() undoes all changes.

✔ Multiple SaveChanges() can participate in one transaction.

✔ Best suited for complex business operations.

✔ Essential for Banking, Payment, Inventory, and E-Commerce applications.

---

# Next Module

## Module 4 – TransactionScope

Topics Covered

- What is TransactionScope?
- Ambient Transactions
- TransactionScope vs BeginTransaction()
- Distributed Transactions
- Async TransactionScope
- Multiple DbContexts
- Multiple Databases
- Real-Time Examples
- Interview Questions