# Module 30 -- Dependency Injection (Parts 1--3)

## Overview

This document summarizes:

-   Part 1 -- Dependencies, Coupling, and Dependency Injection
-   Part 2 -- Dependency Inversion Principle (DIP), Inversion of Control
    (IoC), and Dependency Injection
-   Part 3 -- ASP.NET Core DI Container Internals

------------------------------------------------------------------------

# Part 1 -- Dependencies & Dependency Injection

## What is a Dependency?

A dependency is any object or service another class needs to perform its
work.

Example:

``` csharp
public class OrderService
{
    private readonly EmailService _email = new EmailService();
}
```

`EmailService` is a dependency of `OrderService`.

------------------------------------------------------------------------

## Tight Coupling

A class creates its own dependency.

``` csharp
private EmailService _email = new EmailService();
```

Problems:

-   Difficult to test
-   Difficult to replace
-   Violates SOLID
-   Hard to maintain

------------------------------------------------------------------------

## Loose Coupling

Depend on an abstraction.

``` csharp
public interface IEmailService
{
    void SendEmail();
}
```

``` csharp
public class OrderService
{
    private readonly IEmailService _email;

    public OrderService(IEmailService email)
    {
        _email = email;
    }
}
```

Benefits:

-   Easy to test
-   Easy to replace implementations
-   Better maintainability
-   Follows SOLID

------------------------------------------------------------------------

# Part 2 -- DIP, IoC and DI

## Dependency Inversion Principle (DIP)

Definition:

> High-level modules should not depend on low-level modules. Both should
> depend on abstractions.

Example:

    OrderService
          ↓
    IEmailService
          ↑
    EmailService

Never depend directly on concrete classes.

------------------------------------------------------------------------

## High-Level Module

Contains business logic.

Examples:

-   OrderService
-   PaymentService
-   EmployeeService

------------------------------------------------------------------------

## Low-Level Module

Performs infrastructure work.

Examples:

-   EmailService
-   Logger
-   Repository
-   Database

------------------------------------------------------------------------

## Inversion of Control (IoC)

Instead of creating dependencies yourself, someone else creates them.

Without IoC:

    Car
     ↓
    new Engine()

With IoC:

    Engine

    ↓

    Car

Control is inverted.

------------------------------------------------------------------------

## Dependency Injection (DI)

DI is a technique used to implement IoC.

``` csharp
public OrderService(IEmailService email)
{
    _email = email;
}
```

Types of DI:

-   Constructor Injection (Recommended)
-   Property Injection
-   Method Injection

------------------------------------------------------------------------

# Part 3 -- ASP.NET Core DI Container

## Startup Flow

    Program.cs

    ↓

    builder.Services

    ↓

    Register Services

    ↓

    Build()

    ↓

    IServiceProvider

    ↓

    Run()

------------------------------------------------------------------------

## IServiceCollection

Purpose:

Registers services.

It is NOT the DI container.

Think of it as a registration book.

Example:

``` csharp
builder.Services.AddScoped<IEmailService, EmailService>();
```

Stores:

    Service Type

    ↓

    IEmailService

    Implementation

    ↓

    EmailService

    Lifetime

    ↓

    Scoped

No object is created.

------------------------------------------------------------------------

## ServiceDescriptor

Every registration creates a ServiceDescriptor.

Contains:

-   Service Type
-   Implementation Type
-   Lifetime

------------------------------------------------------------------------

## Build()

``` csharp
var app = builder.Build();
```

Creates the DI container.

------------------------------------------------------------------------

## IServiceProvider

This IS the DI container.

Responsibilities:

-   Creates objects
-   Resolves dependencies
-   Builds object graph
-   Manages lifetimes
-   Disposes services

------------------------------------------------------------------------

## IServiceCollection vs IServiceProvider

  IServiceCollection   IServiceProvider
  -------------------- ------------------------------
  Registers services   Resolves services
  Stores metadata      Creates objects
  No object creation   Object creation happens here

------------------------------------------------------------------------

## Object Graph

Example:

    OrdersController
          ↓
    OrderService
          ↓
    Repository
          ↓
    ApplicationDbContext

Creation order:

1.  ApplicationDbContext
2.  Repository
3.  OrderService
4.  OrdersController

------------------------------------------------------------------------

## Difference Between AddScoped() and new

### AddScoped

``` csharp
builder.Services.AddScoped<IEmailService, EmailService>();
```

-   Registers mapping
-   Creates no object
-   Stores ServiceDescriptor

### new

``` csharp
var email = new EmailService();
```

-   Creates object immediately
-   Allocates memory
-   Executes constructor

------------------------------------------------------------------------

# Interview Questions

## What is Dependency Injection?

A design technique where dependencies are supplied from outside instead
of being created inside a class.

## What is IoC?

A design principle where control of object creation is transferred to
another component.

## What is DIP?

High-level modules should depend on abstractions rather than concrete
implementations.

## What is IServiceCollection?

A registration collection for services.

## What is IServiceProvider?

The runtime DI container that resolves and creates objects.

## Does AddScoped() create an object?

No. It only registers a ServiceDescriptor.

## When is the object created?

When IServiceProvider resolves the service.

## Who creates Controllers?

The ASP.NET Core framework requests IServiceProvider to create
controllers and inject dependencies.

------------------------------------------------------------------------

# Key Points to Remember

-   Dependency = Object required by another object.
-   Tight Coupling = Using `new` inside a class.
-   Loose Coupling = Depend on interfaces.
-   DIP = Depend on abstractions.
-   IoC = Someone else controls object creation.
-   DI = Dependencies are injected.
-   IServiceCollection = Registration book.
-   IServiceProvider = Factory / DI Container.
-   AddScoped() = Registers services only.
-   Build() = Creates the DI Container.
-   Objects are created only when requested.
