# Dependency Injection (DI) Design Pattern in ASP.NET Core

# README 1 – Dependency Injection Design Pattern Fundamentals

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is Dependency Injection?
2. What is a Dependency?
3. Why Do We Need Dependency Injection?
4. Inversion of Control (IoC) vs Dependency Injection (DI)
5. Problems Without Dependency Injection
6. Dependency Injection Pattern
7. Types of Dependency Injection
8. Constructor Injection (ASP.NET Core Standard)
9. Dependency Injection Architecture
10. Service Registration
11. Service Lifetimes
12. Advantages of Dependency Injection
13. Common Interview Questions
14. Key Takeaways

---

# 1. What is Dependency Injection?

**Dependency Injection (DI)** is a design pattern where a class receives its dependencies from an external source instead of creating them itself.

Instead of this:

```csharp
public class OrderService
{
    private readonly EmailService _emailService;

    public OrderService()
    {
        _emailService = new EmailService();
    }
}
```

Use this:

```csharp
public class OrderService
{
    private readonly IEmailService _emailService;

    public OrderService(IEmailService emailService)
    {
        _emailService = emailService;
    }
}
```

The `OrderService` no longer creates the dependency.

The dependency is **injected**.

---

# 2. What is a Dependency?

A dependency is any object that another class requires to perform its work.

Example

```csharp
public class OrderService
{
    private readonly IEmailService _emailService;

    public OrderService(IEmailService emailService)
    {
        _emailService = emailService;
    }
}
```

Here

```text
OrderService

↓

depends on

↓

IEmailService
```

`IEmailService` is the dependency.

---

# 3. Why Do We Need Dependency Injection?

Without DI

```text
OrderService

↓

new EmailService()
```

Problems

* Tight coupling
* Hard to test
* Hard to replace implementations
* Violates Single Responsibility Principle (SRP)

Suppose the company decides to replace `EmailService` with `SendGridEmailService`.

Without DI, the `OrderService` must be modified.

With DI, only the registration changes.

---

# 4. Inversion of Control (IoC) vs Dependency Injection (DI)

Many developers confuse these concepts.

Relationship

```text
Object-Oriented Design

        │

        ▼

Inversion of Control (IoC)

        │

        ▼

Dependency Injection (DI)

        │

        ▼

Constructor Injection

Property Injection

Method Injection
```

## Inversion of Control (IoC)

A design principle where object creation and control are delegated to an external component.

## Dependency Injection (DI)

A design pattern used to implement the IoC principle by supplying dependencies from outside the class.

---

# 5. Problems Without Dependency Injection

Example

```csharp
public class OrderService
{
    private readonly EmailService _emailService;

    public OrderService()
    {
        _emailService = new EmailService();
    }
}
```

Problems

### Tight Coupling

`OrderService` depends directly on a concrete implementation.

---

### Difficult Testing

Replacing `EmailService` with a mock object is difficult.

---

### Poor Maintainability

Changing implementations requires modifying the class.

---

### Violates SRP

The class performs two responsibilities:

* Business logic
* Object creation

---

# 6. Dependency Injection Pattern

Instead of

```text
OrderService

↓

Creates EmailService
```

Use

```text
DI Container

↓

Creates EmailService

↓

Injects EmailService

↓

OrderService
```

The responsibility of object creation is moved outside the class.

---

# 7. Types of Dependency Injection

## Constructor Injection (Recommended)

```csharp
public OrderService(IEmailService emailService)
{
    _emailService = emailService;
}
```

The dependency is provided through the constructor.

ASP.NET Core primarily uses constructor injection.

---

## Property Injection

```csharp
public IEmailService EmailService { get; set; }
```

Dependency is assigned after object creation.

Less preferred because required dependencies can be forgotten.

---

## Method Injection

```csharp
public void SendEmail(IEmailService emailService)
{
}
```

Dependency is supplied only for that specific method call.

Useful when the dependency is required only temporarily.

---

# 8. Constructor Injection (ASP.NET Core Standard)

ASP.NET Core automatically resolves constructor parameters.

Example

```csharp
public class OrderController : ControllerBase
{
    private readonly IOrderService _service;

    public OrderController(IOrderService service)
    {
        _service = service;
    }
}
```

Execution

```text
Controller Requested

↓

DI Container

↓

Create OrderService

↓

Inject into Controller

↓

Controller Executes
```

---

# 9. Dependency Injection Architecture

```text
HTTP Request

      │

      ▼

OrderController

      │

Needs

      ▼

IOrderService

      │

DI Container

      │

Creates

      ▼

OrderService

      │

Needs

      ▼

IEmailService

      │

Creates

      ▼

EmailService

      │

Injects

      ▼

OrderService

      │

Injects

      ▼

OrderController
```

Object creation is handled entirely by the DI container.

---

# 10. Service Registration

Services are registered in `Program.cs`.

Example

```csharp
builder.Services.AddScoped<IEmailService, EmailService>();

builder.Services.AddScoped<IOrderService, OrderService>();
```

Meaning

Whenever the application requires:

```text
IEmailService
```

Create

```text
EmailService
```

---

# 11. Service Lifetimes

## Transient

```csharp
builder.Services.AddTransient<IEmailService, EmailService>();
```

Creates a new instance every time the service is requested.

---

## Scoped (Most Common)

```csharp
builder.Services.AddScoped<IEmailService, EmailService>();
```

Creates one instance per HTTP request.

Recommended for:

* DbContext
* Repositories
* Unit of Work
* Business Services

---

## Singleton

```csharp
builder.Services.AddSingleton<IEmailService, EmailService>();
```

Creates one instance for the entire lifetime of the application.

Suitable for stateless services or shared caches.

Avoid using Singleton for services that depend on Scoped services such as `DbContext`.

---

# 12. Advantages of Dependency Injection

## Loose Coupling

Classes depend on abstractions instead of concrete implementations.

---

## Easier Testing

Dependencies can be replaced with mocks.

Example

```csharp
var emailServiceMock =
new Mock<IEmailService>();
```

---

## Better Maintainability

Implementation changes are isolated to DI registration.

---

## Reusability

Services can be reused throughout the application.

---

## Centralized Configuration

All service registrations are maintained in one place.

---

# 13. Common Interview Questions

### What is Dependency Injection?

A design pattern where dependencies are supplied to a class instead of being created inside the class.

---

### What is a Dependency?

An object that another class requires to perform its work.

---

### Difference between IoC and DI?

| IoC                                  | DI                               |
| ------------------------------------ | -------------------------------- |
| Design Principle                     | Design Pattern                   |
| Transfers control of object creation | Supplies dependencies to a class |
| Broader concept                      | One implementation of IoC        |

---

### Why is Constructor Injection preferred?

* Makes required dependencies explicit.
* Ensures the object is fully initialized.
* Supports immutability.
* Improves testability.

---

### Why use interfaces with DI?

Programming against interfaces reduces coupling and allows implementations to be swapped without modifying consuming classes.

---

### Which DI lifetime should DbContext use?

**Scoped**

One `DbContext` per HTTP request ensures consistent change tracking and transaction management.

---

# 14. Key Takeaways

* Dependency Injection is a design pattern.
* It implements the Inversion of Control principle.
* Classes receive dependencies instead of creating them.
* Constructor Injection is the preferred approach in ASP.NET Core.
* ASP.NET Core includes a built-in DI container.
* Register services using `AddTransient()`, `AddScoped()`, or `AddSingleton()`.
* Program against interfaces rather than concrete implementations.
* Dependency Injection improves maintainability, flexibility, and testability.

---
