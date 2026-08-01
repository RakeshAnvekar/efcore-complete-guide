# Module 6 – Dependency Inversion Principle (DIP)

> **Course:** SOLID Principles – Complete Master Roadmap

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. Introduction
3. What Problem Does DIP Solve?
4. High-Level vs Low-Level Modules
5. Tight Coupling
6. Definition of DIP
7. Solving DIP using Abstraction
8. Notification System Example
9. Constructor Injection
10. Dependency Inversion vs Dependency Injection
11. ASP.NET Core Dependency Injection
12. Service Lifetimes
13. Enterprise Example
14. Benefits of DIP
15. Common Mistakes
16. Best Practices
17. Interview Questions
18. Interview Answers
19. Summary
20. Key Takeaways

---

# Learning Objectives

By the end of this module you will understand:

- What Dependency Inversion Principle (DIP) is
- High-Level and Low-Level modules
- Tight Coupling vs Loose Coupling
- Constructor Injection
- Dependency Injection (DI)
- Inversion of Control (IoC)
- ASP.NET Core Dependency Injection
- Service Lifetimes
- Enterprise examples
- Interview questions

---

# Introduction

Dependency Inversion Principle (DIP) is the foundation of modern ASP.NET Core development.

Almost every ASP.NET Core application uses Dependency Injection (DI), which is based on the Dependency Inversion Principle.

Without DIP:

- Classes become tightly coupled.
- Code becomes difficult to maintain.
- Unit testing becomes difficult.
- Every new feature requires modifying existing classes.

DIP solves these problems by making classes depend on abstractions instead of concrete implementations.

---

# What Problem Does DIP Solve?

Suppose you are building an Order Management System.

Whenever an order is placed, you want to send a confirmation email.

A beginner may write:

```csharp
public class EmailService
{
    public void Send(string message)
    {
        Console.WriteLine($"Email Sent : {message}");
    }
}
```

Order Service

```csharp
public class OrderService
{
    private EmailService emailService = new EmailService();

    public void PlaceOrder()
    {
        Console.WriteLine("Order Placed");

        emailService.Send("Order Confirmation");
    }
}
```

---

# What's the Problem?

The dependency graph looks like this.

```
OrderService
      │
      ▼
EmailService
```

OrderService is directly creating EmailService.

This is called **Tight Coupling**.

---

# New Requirement

Business says:

> Don't send Email anymore.

Instead send SMS.

Now you modify OrderService.

```csharp
private SmsService smsService = new SmsService();
```

Next month

Business says

> Send WhatsApp instead.

Again,

You modify OrderService.

Every new notification provider requires changes in OrderService.

This violates the Open Closed Principle.

---

# High-Level Module

A High-Level Module contains business logic.

Examples

```
OrderService

PaymentService

EmployeeService

InvoiceService

ReportService
```

These classes decide **what the application should do.**

---

# Low-Level Module

Low-Level Modules perform actual work.

Examples

```
EmailService

SmsService

WhatsAppService

PushNotificationService

AzureStorage

SQLRepository
```

These classes decide **how the work is done.**

---

# Definition of DIP

Dependency Inversion Principle states

> High-Level Modules should not depend on Low-Level Modules.

Both should depend on abstractions.

Also,

> Abstractions should not depend on details.

Details should depend on abstractions.

---

# Simple Meaning

Instead of this

```
OrderService

↓

EmailService
```

Use

```
             INotification
                  ▲
                  │
       ┌──────────┼──────────┐
       │          │          │
   EmailService SmsService WhatsAppService
                  ▲
                  │
             OrderService
```

Now OrderService knows only about INotification.

It doesn't know which notification provider is being used.

---

# Step 1 – Create an Abstraction

```csharp
public interface INotification
{
    void Send(string message);
}
```

---

# Step 2 – Email Service

```csharp
public class EmailService : INotification
{
    public void Send(string message)
    {
        Console.WriteLine($"Email : {message}");
    }
}
```

---

# Step 3 – SMS Service

```csharp
public class SmsService : INotification
{
    public void Send(string message)
    {
        Console.WriteLine($"SMS : {message}");
    }
}
```

---

# Step 4 – WhatsApp Service

```csharp
public class WhatsAppService : INotification
{
    public void Send(string message)
    {
        Console.WriteLine($"WhatsApp : {message}");
    }
}
```

---

# Step 5 – Order Service

```csharp
public class OrderService
{
    private readonly INotification notification;

    public OrderService(INotification notification)
    {
        this.notification = notification;
    }

    public void PlaceOrder()
    {
        Console.WriteLine("Order Placed");

        notification.Send("Order Confirmation");
    }
}
```

Notice something.

OrderService no longer depends on EmailService.

It depends only on INotification.

This is Dependency Inversion Principle.

---

# Constructor Injection

How does OrderService receive the notification object?

Through its constructor.

```csharp
public OrderService(INotification notification)
{
    this.notification = notification;
}
```

This is called **Constructor Injection**.

It is the most common form of Dependency Injection.

---

# Object Creation

Console Application

```csharp
INotification notification = new EmailService();

OrderService orderService = new OrderService(notification);

orderService.PlaceOrder();
```

Output

```
Order Placed

Email : Order Confirmation
```

Tomorrow

```csharp
INotification notification = new SmsService();

OrderService orderService = new OrderService(notification);

orderService.PlaceOrder();
```

Output

```
Order Placed

SMS : Order Confirmation
```

Notice

OrderService was never modified.

Only the implementation changed.

---

# Enterprise Example

Suppose your application supports multiple notification providers.

```
INotification

├── EmailService

├── SmsService

├── WhatsAppService

├── PushNotificationService

└── TeamsNotificationService
```

OrderService always depends on INotification.

Whenever a new provider is added

Create one new class.

OrderService never changes.

---

# Another Example – File Storage

Without DIP

```
DocumentService

↓

AzureStorage
```

Business says

Store files in AWS.

You modify DocumentService.

Again,

Business says

Store files locally.

Again,

Modify DocumentService.

---

# Better Design

```
            IStorage
                ▲
                │
     ┌──────────┼──────────┐
     │          │          │
AzureStorage AWSStorage LocalStorage
                ▲
                │
        DocumentService
```

Interface

```csharp
public interface IStorage
{
    void Upload(string fileName);
}
```

Azure

```csharp
public class AzureStorage : IStorage
{
    public void Upload(string fileName)
    {
        Console.WriteLine("Uploaded to Azure");
    }
}
```

AWS

```csharp
public class AwsStorage : IStorage
{
    public void Upload(string fileName)
    {
        Console.WriteLine("Uploaded to AWS");
    }
}
```

Document Service

```csharp
public class DocumentService
{
    private readonly IStorage storage;

    public DocumentService(IStorage storage)
    {
        this.storage = storage;
    }

    public void Save(string fileName)
    {
        storage.Upload(fileName);
    }
}
```

Now DocumentService doesn't care whether files are stored in Azure or AWS.

---

# Dependency Inversion vs Dependency Injection

Many developers confuse these terms.

| Dependency Inversion Principle (DIP) | Dependency Injection (DI) |
|--------------------------------------|---------------------------|
| Design Principle | Design Pattern / Technique |
| Says depend on abstractions | Injects dependencies into objects |
| Improves architecture | Improves object creation |
| Example: INotification | Example: Constructor Injection |

Easy way to remember

```
DIP tells WHAT to do.

DI tells HOW to do it.
```

---

# ASP.NET Core Example

Register service

```csharp
builder.Services.AddScoped<INotification, EmailService>();
```

Order Service

```csharp
public class OrderService
{
    private readonly INotification notification;

    public OrderService(INotification notification)
    {
        this.notification = notification;
    }
}
```

ASP.NET Core automatically creates EmailService and injects it into OrderService.

You never write

```csharp
new EmailService();
```

inside OrderService.

---

# Service Lifetimes

## Transient

```csharp
builder.Services.AddTransient<INotification, EmailService>();
```

New object every time.

Use for lightweight stateless services.

---

## Scoped

```csharp
builder.Services.AddScoped<INotification, EmailService>();
```

One object per HTTP request.

Most commonly used with DbContext.

---

## Singleton

```csharp
builder.Services.AddSingleton<INotification, EmailService>();
```

One object for the lifetime of the application.

Useful for configuration, caching, logging, etc.

---

# Benefits of DIP

✔ Loose Coupling

✔ Better Maintainability

✔ Easy Unit Testing

✔ Supports Mocking

✔ Easier Dependency Injection

✔ Better Scalability

✔ Follows Open Closed Principle

✔ More Flexible Architecture

---

# Common Mistakes

## Mistake 1

Creating dependencies using

```csharp
new EmailService();
```

inside business classes.

---

## Mistake 2

Depending on concrete classes instead of interfaces.

---

## Mistake 3

Thinking Dependency Injection and Dependency Inversion are the same.

---

## Mistake 4

Injecting too many services into one class.

This usually indicates a Single Responsibility Principle violation.

---

# Best Practices

✔ Always depend on interfaces.

✔ Prefer Constructor Injection.

✔ Register services in the DI Container.

✔ Keep High-Level Modules independent of implementation details.

✔ Avoid creating dependencies using new inside business logic.

✔ Follow SOLID principles together.

---

# Interview Questions

## Question 1

What is Dependency Inversion Principle?

---

## Question 2

What is Tight Coupling?

---

## Question 3

What are High-Level Modules?

---

## Question 4

What are Low-Level Modules?

---

## Question 5

Difference between DIP and DI?

---

## Question 6

What is Constructor Injection?

---

## Question 7

Explain Service Lifetimes.

---

## Question 8

Why shouldn't we use new inside business classes?

---

# Interview Answers

## What is DIP?

Dependency Inversion Principle states that High-Level Modules should depend on abstractions instead of concrete implementations.

---

## What is Tight Coupling?

When one class directly creates or depends on another concrete class, making it difficult to change, test, or extend.

---

## What are High-Level Modules?

Classes that contain business logic such as OrderService, PaymentService, and EmployeeService.

---

## What are Low-Level Modules?

Classes that perform implementation details such as EmailService, SmsService, AzureStorage, and SQLRepository.

---

## Difference between DIP and DI?

DIP is a design principle.

DI is a technique used to implement that principle by injecting dependencies.

---

## What is Constructor Injection?

Providing required dependencies through a class constructor instead of creating them inside the class.

---

## Explain Service Lifetimes.

- **Transient** – New instance every time.
- **Scoped** – One instance per HTTP request.
- **Singleton** – One instance for the application's lifetime.

---

## Why avoid using new inside business classes?

Using `new` tightly couples the class to a specific implementation, making it harder to replace, test, or extend.

---

# Summary

Dependency Inversion Principle encourages applications to depend on abstractions instead of concrete implementations.

Business classes should focus on business logic and remain independent of implementation details.

Dependency Injection is the mechanism used to provide those implementations at runtime.

Together, DIP and DI create applications that are flexible, maintainable, testable, and easy to extend.

---

# Key Takeaways

✔ High-Level Modules should depend on abstractions.

✔ Low-Level Modules should implement abstractions.

✔ Avoid Tight Coupling.

✔ Constructor Injection is the preferred Dependency Injection technique.

✔ DIP and DI are different concepts.

✔ ASP.NET Core uses Dependency Injection extensively.

✔ Service Lifetimes are:
- Transient
- Scoped
- Singleton

✔ DIP is one of the most frequently asked topics in senior .NET interviews.