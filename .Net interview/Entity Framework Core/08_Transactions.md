# Entity Framework Core – Transactions
# Module 1 – Introduction to Transactions

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 1 – Introduction to Transactions

**Difficulty:** ⭐⭐⭐☆☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is a Transaction?
2. Why Do We Need Transactions?
3. Real-Time Banking Example
4. Internal Working
5. Commit
6. Rollback
7. ACID Properties
8. Entity Framework Core Example
9. Real-Time Examples
10. Best Practices
11. Interview Questions
12. Summary
13. Key Takeaways

---

# What is a Transaction?

A **Transaction** is a group of one or more database operations that are executed as a **single unit of work**.

A transaction follows the principle:

```
Either

Everything Succeeds

OR

Everything Fails
```

There is no partial success.

---

# Why Do We Need Transactions?

Suppose we perform multiple database operations.

Example

```
Create Order

↓

Create Payment

↓

Reduce Inventory
```

If the inventory update fails after creating the order and payment,

the database becomes inconsistent.

Transactions prevent this problem.

---

# Real-Time Banking Example

Suppose

Rakesh transfers

```
₹1000
```

to Raj.

Initial Database

| Account | Balance |
|----------|----------|
| Rakesh | ₹10,000 |
| Raj | ₹5,000 |

---

## Step 1

Debit Rakesh's account.

```
10000

↓

9000
```

---

## Step 2

Credit Raj's account.

```
5000

↓

6000
```

Everything is correct.

---

## What if Step 2 Fails?

Suppose

```
Debit Completed

↓

Credit Failed
```

Database becomes

| Account | Balance |
|----------|----------|
| Rakesh | ₹9,000 |
| Raj | ₹5,000 |

₹1000 has disappeared.

This is called

```
Data Inconsistency
```

---

# Transaction Solution

Execute both operations inside one transaction.

```
Begin Transaction

↓

Debit Account

↓

Credit Account

↓

Commit
```

If any step fails

```
Rollback

↓

Restore Original Data
```

---

# Internal Working

```
Application

↓

Begin Transaction

↓

Execute SQL Statement 1

↓

Execute SQL Statement 2

↓

Execute SQL Statement 3

↓

Success?

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

# Commit

Commit means

```
Save all changes permanently.
```

Example

Before

| Account | Balance |
|----------|----------|
| Rakesh | ₹10,000 |
| Raj | ₹5,000 |

After Commit

| Account | Balance |
|----------|----------|
| Rakesh | ₹9,000 |
| Raj | ₹6,000 |

Changes are permanently stored in the database.

---

# Rollback

Rollback means

```
Undo all changes.
```

Suppose

```
Debit Successful

↓

Credit Failed
```

Rollback restores

| Account | Balance |
|----------|----------|
| Rakesh | ₹10,000 |
| Raj | ₹5,000 |

The database returns to its previous state.

---

# ATM Example

Suppose a customer withdraws

```
₹5000
```

Steps

```
Check Balance

↓

Deduct Balance

↓

Dispense Cash

↓

Update Transaction History
```

Suppose

Cash dispenser fails.

Without Transaction

```
Balance Deducted

↓

Cash Not Dispensed
```

Customer loses money.

With Transaction

```
Balance Deducted

↓

Machine Failure

↓

Rollback

↓

Original Balance Restored
```

---

# Order Processing Example

Suppose a customer places an order.

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

If invoice generation fails,

everything should be rolled back.

Without a transaction

```
Order Created

Payment Created

Inventory Reduced

Invoice Missing
```

Database becomes inconsistent.

With a transaction

```
Failure

↓

Rollback

↓

Everything Restored
```

---

# ACID Properties

Every database transaction follows the ACID principles.

---

# A – Atomicity

Atomicity means

```
All

OR

Nothing
```

Example

```
Debit

+

Credit
```

Either both operations succeed,

or both fail.

---

# C – Consistency

A transaction always keeps the database in a valid state.

Example

Before Transfer

```
Rakesh = 10000

Raj = 5000

Total = 15000
```

After Transfer

```
Rakesh = 9000

Raj = 6000

Total = 15000
```

The total amount remains the same.

---

# I – Isolation

Multiple transactions should not interfere with each other.

Example

User A

```
Withdraw ₹1000
```

User B

```
Withdraw ₹500
```

Both transactions execute independently and safely.

---

# D – Durability

Once a transaction is committed,

the data remains saved even if

- Application crashes
- SQL Server restarts
- Power failure occurs

Committed data is permanent.

---

# Entity Framework Core Example

```csharp
using var context = new AppDbContext();

var order = new Order
{
    CustomerName = "Rakesh",
    Amount = 1000
};

context.Orders.Add(order);

context.SaveChanges();
```

Internally

```
Begin Transaction

↓

INSERT Order

↓

Commit
```

If INSERT fails

```
Rollback
```

---

# Multiple Operations Example

```csharp
context.Orders.Add(order);

context.Payments.Add(payment);

context.Inventory.Update(stock);

context.SaveChanges();
```

Internally

```
Begin Transaction

↓

INSERT Order

↓

INSERT Payment

↓

UPDATE Inventory

↓

Commit
```

Suppose

Inventory update fails.

```
Rollback

↓

Order Removed

↓

Payment Removed
```

Everything returns to the previous state.

---

# Internal Flow Diagram

```
Application

↓

DbContext

↓

SaveChanges()

↓

Begin Transaction

↓

Execute SQL

↓

Success?

↓

Commit

OR

Rollback

↓

Return Result
```

---

# Why Transactions Are Important

Without Transactions

❌ Partial Updates

❌ Lost Data

❌ Incorrect Balance

❌ Duplicate Records

❌ Database Inconsistency

With Transactions

✔ Reliable Operations

✔ Data Consistency

✔ Safe Updates

✔ Automatic Recovery

✔ Atomic Operations

---

# Best Practices

✔ Keep transactions as short as possible.

✔ Don't keep transactions open while calling external APIs.

✔ Always handle exceptions.

✔ Use transactions for operations involving multiple tables.

✔ Commit only after all operations succeed.

✔ Rollback immediately when an error occurs.

---

# Interview Questions

## What is a Transaction?

A transaction is a group of database operations that execute as a single unit of work. Either all operations succeed or all operations fail.

---

## What is Commit?

Commit permanently saves all changes made during a transaction.

---

## What is Rollback?

Rollback undoes all changes made during the transaction if an error occurs.

---

## Why are Transactions important?

Transactions ensure data consistency by preventing partial updates and maintaining database integrity.

---

## What are ACID properties?

- **Atomicity** – All or Nothing
- **Consistency** – Database remains valid
- **Isolation** – Transactions don't interfere with each other
- **Durability** – Committed changes are permanent

---

## Does Entity Framework Core use transactions automatically?

Yes.

`SaveChanges()` automatically wraps all pending database operations in a transaction (when appropriate) to ensure data consistency.

---

# Summary

A transaction is one of the most important concepts in database systems.

It ensures that multiple database operations execute as one logical unit.

If every operation succeeds, the transaction is committed.

If any operation fails, all changes are rolled back.

Transactions guarantee data consistency, reliability, and integrity, making them essential for banking systems, payment processing, e-commerce, inventory management, and enterprise applications.

---

# Key Takeaways

✔ Transaction = One Unit of Work

✔ Either Everything Commits or Everything Rolls Back

✔ Commit permanently saves changes

✔ Rollback restores the previous state

✔ Transactions follow ACID principles

✔ Entity Framework Core automatically uses transactions in `SaveChanges()` when required

✔ Transactions prevent partial updates and inconsistent data

---

# Next Module

## Module 2 – Transactions Inside Entity Framework Core

Topics Covered

- Implicit Transactions
- SaveChanges() Internal Transaction Pipeline
- How EF Core Automatically Begins Transactions
- Multiple SaveChanges()
- SQL Generated by EF Core
- When EF Creates Transactions Automatically
- Interview Questions