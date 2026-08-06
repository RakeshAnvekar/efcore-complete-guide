# Clean Code for .NET Developers

# README 5 – YAGNI Principle (You Aren't Gonna Need It)

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is YAGNI?
2. Why YAGNI Exists
3. The Cost of Overengineering
4. YAGNI in Real Projects
5. Premature Abstraction
6. Premature Optimization
7. Feature Creep
8. YAGNI in ASP.NET Core
9. YAGNI in Repository Pattern
10. YAGNI in CQRS
11. YAGNI vs DRY
12. YAGNI vs KISS
13. Enterprise Examples
14. Code Review Checklist
15. Common Interview Questions
16. Key Takeaways

---

# 1. What is YAGNI?

YAGNI stands for:

```text
You Aren't Gonna Need It
```

Meaning:

> **Don't build functionality until there is a real business requirement for it.**

Developers often try to solve tomorrow's problems today.

YAGNI says:

Solve **today's** problem well.

---

# 2. Why YAGNI Exists

Suppose the requirement is:

```text
Send Email
```

Developer thinks

```text
Maybe in future

SMS

WhatsApp

Telegram

Slack

Push Notification

Signal
```

So they build

```text
NotificationFactory

NotificationStrategy

NotificationProvider

NotificationConfiguration

NotificationBuilder
```

Reality

Business only wanted

```text
Email
```

Everything else becomes unused code.

---

# 3. The Cost of Overengineering

Overengineering causes

* More classes
* More interfaces
* More testing
* More bugs
* More maintenance
* More documentation

Example

Requirement

```text
Export CSV
```

Developer builds

```text
CSV

Excel

PDF

Word

PowerPoint

XML

JSON
```

Only CSV is used.

---

# 4. YAGNI in Real Projects

Requirement

```text
One Payment Gateway
```

Bad Design

```text
PaymentFactory

↓

Stripe

PayPal

RazorPay

Google Pay

Apple Pay

Square

Amazon Pay
```

Good Design

```text
StripePaymentService
```

When another provider is actually required, refactor then.

---

# 5. Premature Abstraction

Bad

```csharp
public interface IRepository<TEntity, TKey, TFilter, TContext>
{
}
```

The application has only one entity.

The abstraction adds complexity without solving a current problem.

---

Good

```csharp
public interface IOrderRepository
{
    Task<Order> GetByIdAsync(int id);
}
```

Simple.

---

# 6. Premature Optimization

Bad

```text
Project Starts

↓

Redis

↓

Kafka

↓

ElasticSearch

↓

Distributed Cache

↓

Read Replica

↓

Message Queue
```

Current users

```text
15
```

Huge unnecessary complexity.

---

Good

```text
ASP.NET Core

↓

SQL Server

↓

Deploy
```

Optimize only after measuring a real bottleneck.

---

# 7. Feature Creep

Requirement

```text
Create Customer
```

Developer also adds

* Import Customers
* Export Customers
* Archive Customers
* Restore Customers
* Merge Customers

Business never requested them.

Every extra feature increases maintenance.

---

# 8. YAGNI in ASP.NET Core

Bad

```text
CustomerController

↓

15 Endpoints

↓

Only 2 Used
```

Good

Create only the endpoints required today.

---

# 9. YAGNI in Repository Pattern

Bad

```csharp
public interface IRepository<TEntity>
{
    Get();

    Add();

    Delete();

    Update();

    BulkInsert();

    BulkDelete();

    Archive();

    SoftDelete();

    Restore();

    Export();

    Import();
}
```

Half of these methods may never be used.

---

Good

Expose only the methods the application currently needs.

---

# 10. YAGNI in CQRS

Bad

Project with

```text
CreateOrder

↓

50 Pipeline Behaviors

↓

Domain Events

↓

Saga

↓

Kafka

↓

Outbox Pattern
```

Requirement

```text
Simple CRUD
```

The architecture is far too complex.

---

Good

```text
Controller

↓

Mediator

↓

Handler

↓

Repository

↓

SQL
```

Add more only when justified.

---

# 11. YAGNI vs DRY

## DRY

Avoid duplicate business logic.

---

## YAGNI

Avoid building unnecessary functionality.

They complement each other.

---

# 12. YAGNI vs KISS

KISS

```text
Choose the simplest solution.
```

YAGNI

```text
Build only what is currently needed.
```

Example

Requirement

```text
Upload Images
```

KISS

Simple upload service.

YAGNI

Don't build video upload, PDF upload, and audio upload until requested.

---

# 13. Enterprise Examples

## Example 1

Requirement

```text
One Database
```

Bad

```text
CQRS

Read Database

Write Database

Redis

Kafka

ElasticSearch
```

Good

```text
SQL Server
```

---

## Example 2

Requirement

```text
One Country
```

Bad

Global localization framework supporting 50 languages.

Good

Support one language today and extend later if needed.

---

## Example 3

Requirement

```text
One User Role
```

Bad

Complex RBAC system with 25 roles.

Good

Implement only the required roles.

---

# 14. Code Review Checklist

Ask:

* Was this requested by the business?
* Is this solving a real problem?
* Is there evidence this abstraction is needed?
* Can we add this later without major redesign?
* Are we optimizing too early?
* Is this feature actually used?

---

# 15. Common Interview Questions

### What is YAGNI?

A principle that says don't implement features until they are actually required.

---

### Why is YAGNI important?

Because unused code adds complexity, maintenance cost, and opportunities for bugs.

---

### Is YAGNI against good design?

No.

It encourages evolving the design based on real requirements rather than assumptions.

---

### How does YAGNI help projects?

* Faster delivery
* Simpler code
* Lower maintenance
* Fewer bugs
* Better focus on business value

---

### Can YAGNI conflict with DRY?

Sometimes.

Avoid creating overly generic abstractions too early just to eliminate small amounts of duplication.

---

# 16. Key Takeaways

* Build for today's requirements.
* Avoid premature abstraction.
* Avoid premature optimization.
* Don't create features "just in case."
* Refactor when new requirements arrive.
* Measure before optimizing.
* Every unnecessary class, interface, or feature has a maintenance cost.

---

# Real Code Review Example

## ❌ Bad

```csharp
public interface IPaymentGateway
{
    ProcessStripe();

    ProcessPayPal();

    ProcessGooglePay();

    ProcessApplePay();

    ProcessAmazonPay();
}
```

Current requirement:

```text
Only Stripe
```

---

## ✅ Better

```csharp
public interface IPaymentService
{
    Task ProcessPaymentAsync(PaymentRequest request);
}
```

If another payment provider is introduced later, refactor then.

---

# What's Next?

## README 6 – Logging Best Practices

Topics include:

* Why logging is critical
* Logging levels (Trace, Debug, Information, Warning, Error, Critical)
* `ILogger`
* Structured logging
* Serilog
* Correlation IDs
* Request/Response logging
* Exception logging
* Security considerations
* Logging in Microservices
* Production logging strategy
* Common interview questions
