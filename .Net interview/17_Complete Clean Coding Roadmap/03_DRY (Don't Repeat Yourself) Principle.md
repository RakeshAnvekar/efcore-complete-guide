# Clean Code for .NET Developers

# README 3 – DRY Principle (Don't Repeat Yourself)

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is DRY?
2. Why DRY Matters
3. Problems Without DRY
4. Duplicate Code Examples
5. Applying DRY
6. DRY in ASP.NET Core
7. DRY in Repository Pattern
8. DRY with Generic Repository
9. DRY with Extension Methods
10. DRY with Middleware
11. DRY with CQRS & MediatR
12. When NOT to Apply DRY
13. Common Interview Questions
14. Code Review Checklist
15. Key Takeaways

---

# 1. What is DRY?

**DRY** stands for:

```text
Don't Repeat Yourself
```

The principle means:

> **Every piece of knowledge or business rule should have a single, authoritative representation in your application.**

Notice:

DRY is **not only about duplicate code**.

It is about **avoiding duplicate business logic**.

---

# 2. Why DRY Matters

Imagine an E-Commerce application.

You calculate discount in three places.

```text
Checkout Service

↓

10% Discount

----------------

Invoice Service

↓

10% Discount

----------------

Order Service

↓

10% Discount
```

Business changes discount to **15%**.

Now you must modify

* Checkout
* Invoice
* Order

Miss one place?

Bug.

---

# 3. Problems Without DRY

Suppose

Checkout

```csharp
discount = amount * 0.10m;
```

Invoice

```csharp
discount = amount * 0.10m;
```

Order

```csharp
discount = amount * 0.10m;
```

Three copies.

Tomorrow

```text
10%

↓

15%
```

Need to update

three places.

Easy to forget one.

---

# 4. Duplicate Code Example

## Bad Example

```csharp
public decimal CalculateOrderDiscount(decimal amount)
{
    return amount * 0.10m;
}

public decimal CalculateInvoiceDiscount(decimal amount)
{
    return amount * 0.10m;
}

public decimal CalculateCartDiscount(decimal amount)
{
    return amount * 0.10m;
}
```

Three identical implementations.

---

# 5. Applying DRY

Create one service.

```csharp
public class DiscountCalculator
{
    public decimal Calculate(decimal amount)
    {
        return amount * 0.10m;
    }
}
```

Use it everywhere.

```text
Checkout

↓

DiscountCalculator

----------------

Invoice

↓

DiscountCalculator

----------------

Order

↓

DiscountCalculator
```

Business changes

```text
10%

↓

15%
```

Only one file changes.

---

# 6. DRY in ASP.NET Core

Bad

```csharp
_logger.LogInformation("Order Created");
```

written inside

* Controller
* Service
* Repository
* Handler

Repeated everywhere.

---

Better

Create

```text
LoggingBehavior

or

LoggingService
```

Reuse it.

---

# 7. DRY in Repository Pattern

Bad

```csharp
public async Task<Order> GetOrder(int id)
{
    return await _context.Orders
        .FirstOrDefaultAsync(x => x.Id == id);
}

public async Task<Customer> GetCustomer(int id)
{
    return await _context.Customers
        .FirstOrDefaultAsync(x => x.Id == id);
}
```

Same retrieval logic repeated.

---

Generic Repository

```csharp
public async Task<TEntity?> GetByIdAsync(int id)
{
    return await _context.Set<TEntity>()
        .FindAsync(id);
}
```

Works for

* Order
* Customer
* Product
* Invoice

Single implementation.

---

# 8. DRY with Generic Repository

```csharp
public interface IRepository<TEntity>
{
    Task<TEntity?> GetByIdAsync(int id);

    Task AddAsync(TEntity entity);

    Task UpdateAsync(TEntity entity);

    Task DeleteAsync(TEntity entity);
}
```

One reusable abstraction.

---

# 9. DRY with Extension Methods

Bad

```csharp
customer.FirstName + " " + customer.LastName
```

Repeated everywhere.

---

Good

```csharp
public static class CustomerExtensions
{
    public static string FullName(this Customer customer)
    {
        return $"{customer.FirstName} {customer.LastName}";
    }
}
```

Usage

```csharp
customer.FullName();
```

---

# 10. DRY with Middleware

Bad

Every controller

```csharp
try
{
}
catch(Exception ex)
{
}
```

---

Good

Global Exception Middleware

```text
Controller

↓

Exception Middleware

↓

Response
```

One implementation.

---

# 11. DRY with CQRS + MediatR

Without Pipeline Behaviors

Every Handler

```csharp
_logger.LogInformation(...)

Validate(...)

Measure Time(...)

try/catch
```

Repeated.

---

With Pipeline Behaviors

```text
Mediator

↓

Logging Behavior

↓

Validation Behavior

↓

Performance Behavior

↓

Handler
```

Handlers contain only business logic.

---

# 12. When NOT to Apply DRY

One of the biggest mistakes is forcing unrelated code into one method.

Example

```csharp
CalculateTaxForIndia()

CalculateTaxForUSA()
```

They look similar today.

Tomorrow

Tax rules become completely different.

Keeping them separate is better.

---

Another example

```text
Customer Address Validation

Employee Address Validation
```

They may diverge later.

Don't combine them too early.

Sometimes duplication is acceptable until the business rules stabilize.

---

# 13. Common Interview Questions

### What is DRY?

A principle stating that every piece of business knowledge should exist in only one place.

---

### Does DRY only mean duplicate code?

No.

It primarily means avoiding duplicate business logic.

---

### How do you apply DRY?

* Shared services
* Helper classes
* Generic repositories
* Extension methods
* Middleware
* Pipeline Behaviors
* Reusable components

---

### Can DRY be overused?

Yes.

Trying to eliminate every duplication can create overly generic code that is difficult to understand and maintain.

---

# 14. Code Review Checklist

Before approving code ask:

* Is business logic duplicated?
* Can this method be reused?
* Is the same validation repeated?
* Is logging duplicated?
* Is exception handling duplicated?
* Can middleware solve this?
* Can MediatR Pipeline Behaviors solve this?
* Is a generic solution justified?
* Would abstraction make the code simpler or more complex?

---

# 15. Key Takeaways

* DRY = **Don't Repeat Yourself**.
* Duplicate business logic is more harmful than duplicate syntax.
* Centralize reusable logic.
* Use services, extension methods, middleware, and pipeline behaviors to reduce repetition.
* Don't create unnecessary abstractions just to eliminate small amounts of duplication.
* Follow the **Rule of Three**: if similar logic appears three times, it's usually time to extract it into a reusable component.

---

# Enterprise Examples

| Duplicate Logic      | Better Solution             |
| -------------------- | --------------------------- |
| Discount calculation | DiscountCalculator          |
| Tax calculation      | TaxService                  |
| Validation           | FluentValidation            |
| Logging              | ILogger / Pipeline Behavior |
| Exception Handling   | Global Middleware           |
| CRUD Operations      | Generic Repository          |
| Full Name formatting | Extension Method            |
| Authorization        | Authorization Policy        |
| Mapping              | AutoMapper                  |

---

# What's Next?

**README 4 – KISS (Keep It Simple, Stupid)**

Topics include:

* Why overengineering is dangerous
* Simple vs clever code
* KISS in API design
* KISS in Repository Pattern
* KISS in CQRS
* KISS in Microservices
* Real code review examples
* Enterprise case studies
* Senior interview questions
