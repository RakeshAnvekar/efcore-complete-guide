# Clean Code for .NET Developers

# README 1 – Clean Code Fundamentals

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is Clean Code?
2. Why Clean Code Matters
3. Characteristics of Clean Code
4. Clean Code Principles
5. Meaningful Naming
6. Small Methods
7. Single Responsibility
8. Avoid Deep Nesting
9. Eliminate Duplicate Code (DRY)
10. Keep It Simple (KISS)
11. You Aren't Gonna Need It (YAGNI)
12. Avoid Magic Numbers & Strings
13. Write Readable Code
14. Thin Controllers
15. Clean Architecture Flow
16. Clean Code Checklist
17. Common Code Smells
18. Common Interview Questions
19. Key Takeaways
20. Learning Roadmap

---

# 1. What is Clean Code?

Clean code is code that is:

* Easy to read
* Easy to understand
* Easy to maintain
* Easy to modify
* Easy to test
* Easy to debug

Robert C. Martin (Uncle Bob) describes clean code as code that clearly communicates its intent.

---

# 2. Why Clean Code Matters

Software is written once but read and modified many times.

Reality

```text
Write Code Once

↓

Read Hundreds of Times

↓

Modified Thousands of Times

↓

Maintained for Years
```

Good code reduces:

* Bugs
* Development time
* Maintenance cost
* Onboarding time for new developers

---

# 3. Characteristics of Clean Code

A clean codebase should have:

* Meaningful names
* Small methods
* Small classes
* Single responsibility
* Minimal duplication
* Consistent formatting
* Clear business logic
* Proper error handling
* Appropriate logging
* Unit testability

---

# 4. Clean Code Principles

The most important principles are:

* Meaningful Naming
* Single Responsibility Principle (SRP)
* DRY (Don't Repeat Yourself)
* KISS (Keep It Simple)
* YAGNI (You Aren't Gonna Need It)
* Small Methods
* Small Classes
* Guard Clauses
* Low Coupling
* High Cohesion

---

# 5. Meaningful Naming

## Bad Example

```csharp
public void P(Order o)
{
    if(o.A > 1000)
    {
    }
}
```

Problems

* What is P?
* What is o?
* What is A?

---

## Good Example

```csharp
public void ProcessOrder(Order order)
{
    if(order.TotalAmount > 1000)
    {
        ApplyPremiumDiscount(order);
    }
}
```

The code explains itself.

---

## Variable Naming

Bad

```csharp
int x;
string a;
bool b;
```

Good

```csharp
int customerAge;
string customerEmail;
bool isPremiumCustomer;
```

---

## Method Naming

Bad

```csharp
Save();

Run();

Do();
```

Good

```csharp
SaveOrder();

CalculateDiscount();

GenerateInvoice();

SendOrderConfirmationEmail();
```

Method names should describe an action.

---

## Boolean Naming

Bad

```csharp
bool status;
```

Good

```csharp
bool isActive;

bool hasPermission;

bool isDeleted;
```

---

# 6. Small Methods

Bad

```text
ProcessOrder()

↓

300 Lines
```

Good

```text
ValidateOrder()

↓

CalculateDiscount()

↓

CalculateTax()

↓

ReserveInventory()

↓

SaveOrder()

↓

SendConfirmationEmail()
```

Each method should ideally perform one task.

---

# 7. Single Responsibility

Bad

```csharp
public void ProcessOrder()
{
    Validate();

    SaveToDatabase();

    SendEmail();

    GenerateInvoice();

    UpdateInventory();

    WriteLog();
}
```

One method performs many unrelated responsibilities.

---

Good

```text
OrderService

↓

OrderRepository

↓

EmailService

↓

InventoryService

↓

InvoiceService

↓

LoggingService
```

Each class has one responsibility.

---

# 8. Avoid Deep Nesting

Bad

```csharp
if(customer != null)
{
    if(customer.IsActive)
    {
        if(customer.HasPermission)
        {
            ProcessOrder();
        }
    }
}
```

---

Good (Guard Clauses)

```csharp
if(customer == null)
    return;

if(!customer.IsActive)
    return;

if(!customer.HasPermission)
    return;

ProcessOrder();
```

Benefits

* Easier to read
* Easier to debug
* Lower cyclomatic complexity

---

# 9. DRY (Don't Repeat Yourself)

## Bad

Discount calculation copied into:

* Order Service
* Invoice Service
* Checkout Service

```csharp
discount = amount * 0.10m;
```

appears everywhere.

---

## Good

```text
DiscountCalculator

↓

CalculateDiscount()
```

Reuse the method everywhere.

Benefits

* One place to maintain
* Fewer bugs
* Easier changes

---

# 10. KISS (Keep It Simple)

KISS = **Keep It Simple, Stupid**

Bad

```csharp
return !(a && !b || c);
```

Good

```csharp
bool canProcessOrder =
    customer.IsActive &&
    customer.HasPermission &&
    !customer.IsBlocked;

return canProcessOrder;
```

Simple code is easier to maintain.

---

# 11. YAGNI (You Aren't Gonna Need It)

Don't build features that are not required today.

Bad

```text
Customer wants Email.

Developer also builds:

SMS

WhatsApp

Telegram

Push Notifications

Slack

Signal
```

Only Email was required.

---

Good

Build Email first.

Add SMS when the business actually requests it.

Benefits

* Less code
* Lower maintenance
* Faster delivery

---

# 12. Avoid Magic Numbers & Strings

Bad

```csharp
if(order.Amount > 5000)
```

Good

```csharp
const decimal PremiumOrderAmount = 5000;

if(order.Amount > PremiumOrderAmount)
{
}
```

---

Bad

```csharp
if(status == "Completed")
```

Good

```csharp
public static class OrderStatus
{
    public const string Completed = "Completed";
}
```

Or even better:

```csharp
public enum OrderStatus
{
    Pending,
    Completed,
    Cancelled
}
```

---

# 13. Write Readable Code

Avoid clever code.

Bad

```csharp
return !(a && !b || c);
```

Good

```csharp
bool canShipOrder =
    customer.IsActive &&
    customer.HasPermission &&
    inventoryAvailable;

return canShipOrder;
```

Code is written for humans first.

---

# 14. Thin Controllers

Bad

```text
Controller

↓

Validation

↓

Business Logic

↓

Repository

↓

Email

↓

Logging
```

---

Good

```text
Controller

↓

Application Service / MediatR

↓

Repository

↓

Database
```

Controller responsibilities

* Receive Request
* Validate Model (basic)
* Call Service/Mediator
* Return Response

Nothing more.

---

# 15. Clean Architecture Flow

```text
Client

↓

Controller

↓

Application Service / MediatR

↓

Repository

↓

DbContext

↓

SQL Server
```

Each layer has a single responsibility.

---

# 16. Clean Code Checklist

Before every Pull Request ask yourself:

* Are variable names meaningful?
* Are method names descriptive?
* Does each method do one thing?
* Is the method too long?
* Is there duplicate code?
* Are there magic numbers?
* Is nesting too deep?
* Can another developer understand this without comments?
* Have I handled errors appropriately?
* Is the code testable?

---

# 17. Common Code Smells

## Long Method

```text
800 Lines
```

Split into smaller methods.

---

## Large Class

```text
OrderService

↓

1500 Lines
```

Split into focused services.

---

## Duplicate Code

The same logic exists in multiple places.

Extract it into a reusable method or service.

---

## Long Parameter List

Bad

```csharp
CreateOrder(
    customerId,
    customerName,
    customerPhone,
    customerAddress,
    city,
    state,
    country)
```

Better

```csharp
CreateOrder(CreateOrderRequest request)
```

---

## Primitive Obsession

Avoid passing many strings and integers when a domain object makes the intent clearer.

---

## God Class

One class performing everything.

Split responsibilities.

---

# 18. Common Interview Questions

### What is Clean Code?

Code that is easy to understand, maintain, test, and extend.

---

### Why is Clean Code important?

Because software spends far more time being maintained than being written.

---

### What are the most important Clean Code principles?

* Meaningful Naming
* SRP
* DRY
* KISS
* YAGNI
* Small Methods
* Guard Clauses

---

### What are Code Smells?

Indicators that suggest code may need refactoring, such as long methods, duplicate logic, large classes, or excessive parameters.

---

### How do you write maintainable code?

* Follow SOLID principles
* Use meaningful names
* Keep methods small
* Avoid duplication
* Add appropriate logging
* Write unit tests
* Perform code reviews
* Refactor regularly

---

# 19. Key Takeaways

* Code is read much more than it is written.
* Prefer clarity over cleverness.
* Use meaningful names.
* Keep methods and classes focused.
* Eliminate duplication (DRY).
* Keep solutions simple (KISS).
* Build only what is required (YAGNI).
* Avoid magic numbers and hard-coded strings.
* Use guard clauses to reduce nesting.
* Keep controllers thin and business logic in the application layer.
* Regular refactoring and code reviews keep the codebase healthy.

---

# 20. Complete Clean Code Learning Roadmap

## Module 1

✅ Clean Code Fundamentals

## Module 2

Naming Conventions (Microsoft C# Standards)

## Module 3

SOLID Principles (Deep Dive)

## Module 4

DRY Principle

## Module 5

KISS Principle

## Module 6

YAGNI Principle

## Module 7

Code Smells & Refactoring

## Module 8

Logging Best Practices (`ILogger`, Serilog, Structured Logging, Correlation IDs)

## Module 9

Exception Handling Best Practices

## Module 10

Code Review Guidelines

## Module 11

Production-Ready Coding Standards

## Module 12

Enterprise Clean Architecture Best Practices

---

## Next README

**README 2 – Naming Conventions (Microsoft C# Coding Standards)**

Topics include:

* Class naming
* Interface naming
* Method naming
* Variable naming
* Private field naming
* Constants
* Enums
* Async method naming
* Folder structure
* Namespace conventions
* File naming
* Real enterprise examples
* Common naming mistakes discussed in code reviews
