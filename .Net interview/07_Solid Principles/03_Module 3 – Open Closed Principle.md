# Module 3 – Open Closed Principle (OCP)

> **Course:** SOLID Principles – Complete Master Roadmap

**Difficulty:** ⭐⭐⭐☆☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. Introduction
3. Definition of OCP
4. Why Do We Need OCP?
5. Extension vs Modification
6. Before OCP Example
7. Problems with the Before OCP Design
8. After OCP Example
9. Strategy Pattern
10. Payment Gateway Example
11. Notification Example
12. ASP.NET Core Middleware Example
13. Benefits of OCP
14. Common Mistakes
15. Best Practices
16. Interview Questions
17. Interview Answers
18. Summary
19. Key Takeaways
20. Next Lesson

---

# Learning Objectives

By the end of this lesson, you will understand:

- What the Open Closed Principle (OCP) is
- Why OCP is important
- Difference between Extension and Modification
- How to identify OCP violations
- How to implement OCP using Interfaces and Polymorphism
- How Strategy Pattern follows OCP
- Real-world examples using Payment Gateways, Notifications and ASP.NET Core Middleware

---

# Introduction

Imagine you built an application that supports only **Credit Card** payments.

A few months later your manager says:

- Add UPI
- Add PayPal
- Add Apple Pay
- Add Google Pay

Every time a new payment method is introduced, you modify the same class.

Eventually, the class becomes huge and difficult to maintain.

This is exactly the problem that the **Open Closed Principle** solves.

---

# Definition of OCP

The **Open Closed Principle (OCP)** states:

> **"Software entities (classes, modules, functions, etc.) should be open for extension but closed for modification."**
>
> — Robert C. Martin (Uncle Bob)

---

## Simple Meaning

Your application should allow new functionality to be added **without changing existing, tested code.**

Instead of modifying old classes, we create **new classes** that extend the application's behavior.

---

# Why Do We Need OCP?

Imagine your application is already running in Production.

Every modification to existing code introduces risks:

- Existing features may break.
- New bugs may appear.
- Regression testing becomes necessary.
- Maintenance cost increases.

Instead of modifying working code repeatedly, OCP encourages us to extend the application by adding new implementations.

---

# Extension vs Modification

This is the most important concept in OCP.

## Modification (Bad)

```
PaymentService

↓

Modify Existing Code

↓

New Bugs

↓

Regression Testing
```

Every new feature changes the same class.

---

## Extension (Good)

```
PaymentService

↓

Add New Class

↓

Existing Code Remains Untouched

↓

Low Risk
```

The original class remains stable.

New functionality is added separately.

---

# Before OCP Example

Suppose an e-commerce application supports only Credit Card payments.

```csharp
public class PaymentService
{
    public void ProcessPayment(string paymentType)
    {
        if (paymentType == "CreditCard")
        {
            Console.WriteLine("Processing Credit Card Payment");
        }
    }
}
```

Everything works.

---

## New Requirement

Business now wants to support:

- UPI
- PayPal
- Net Banking
- Apple Pay

A beginner usually writes:

```csharp
public class PaymentService
{
    public void ProcessPayment(string paymentType)
    {
        if (paymentType == "CreditCard")
        {
            Console.WriteLine("Credit Card");
        }
        else if (paymentType == "UPI")
        {
            Console.WriteLine("UPI");
        }
        else if (paymentType == "PayPal")
        {
            Console.WriteLine("PayPal");
        }
        else if (paymentType == "NetBanking")
        {
            Console.WriteLine("Net Banking");
        }
        else if (paymentType == "ApplePay")
        {
            Console.WriteLine("Apple Pay");
        }
    }
}
```

The application still works.

But the design is poor.

---

# Problems with the Before OCP Design

Every new payment method requires changing the existing class.

```
PaymentService

├── Credit Card

├── UPI

├── PayPal

├── Net Banking

├── Apple Pay

├── Google Pay

└── Crypto
```

Problems:

- Huge if-else chain
- Difficult to maintain
- Hard to test
- Violates OCP
- Higher chance of bugs

---

# After OCP Example

Instead of modifying the same class repeatedly, create an abstraction.

---

## Step 1 – Create an Interface

```csharp
public interface IPaymentMethod
{
    void Pay(decimal amount);
}
```

---

## Step 2 – Credit Card Implementation

```csharp
public class CreditCardPayment : IPaymentMethod
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid {amount} using Credit Card");
    }
}
```

---

## Step 3 – UPI Implementation

```csharp
public class UpiPayment : IPaymentMethod
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid {amount} using UPI");
    }
}
```

---

## Step 4 – PayPal Implementation

```csharp
public class PayPalPayment : IPaymentMethod
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid {amount} using PayPal");
    }
}
```

---

## Payment Service

```csharp
public class PaymentService
{
    private readonly IPaymentMethod paymentMethod;

    public PaymentService(IPaymentMethod paymentMethod)
    {
        this.paymentMethod = paymentMethod;
    }

    public void Process(decimal amount)
    {
        paymentMethod.Pay(amount);
    }
}
```

Now, when a new payment method is required, simply create another class implementing `IPaymentMethod`.

The `PaymentService` remains unchanged.

This is OCP.

---

# Strategy Pattern

The Strategy Pattern is one of the best implementations of OCP.

Instead of writing multiple if-else statements, different algorithms are placed inside different classes.

```
                 IPaymentMethod
                      ▲
       ┌──────────────┼──────────────┐
       │              │              │
CreditCard       UPI Payment      PayPal

                ↓

          PaymentService
```

The payment strategy changes, but the PaymentService does not.

---

# Payment Gateway Example

Imagine an online shopping website.

Without OCP

```
PaymentService

↓

if Credit Card

↓

if UPI

↓

if Razorpay

↓

if Stripe

↓

if PayPal
```

Every payment provider requires code changes.

---

With OCP

```
IPaymentGateway

├── StripeGateway

├── RazorpayGateway

├── PayPalGateway

├── SquareGateway

└── FutureGateway
```

Adding another gateway only requires creating a new implementation.

No existing code changes.

---

# Notification Example

Suppose your application sends notifications.

---

## Without OCP

```csharp
if(type=="Email")
{
}
else if(type=="SMS")
{
}
else if(type=="Push")
{
}
else if(type=="WhatsApp")
{
}
```

Every notification type modifies the same class.

---

## With OCP

```csharp
public interface INotification
{
    void Send(string message);
}
```

Implementations

```text
EmailNotification

SMSNotification

PushNotification

WhatsAppNotification
```

The Notification Service depends only on the interface.

Whenever a new notification type is introduced, no existing code changes.

---

# ASP.NET Core Middleware Example

ASP.NET Core Middleware is a perfect example of OCP.

Default Pipeline

```csharp
app.UseRouting();

app.UseAuthentication();

app.UseAuthorization();

app.MapControllers();
```

Suppose we need Request Logging.

We don't modify ASP.NET Core.

We simply create another middleware.

```csharp
public class LoggingMiddleware
{
    private readonly RequestDelegate next;

    public LoggingMiddleware(RequestDelegate next)
    {
        this.next = next;
    }

    public async Task Invoke(HttpContext context)
    {
        Console.WriteLine("Request Started");

        await next(context);

        Console.WriteLine("Request Completed");
    }
}
```

Register it.

```csharp
app.UseMiddleware<LoggingMiddleware>();
```

ASP.NET Core itself remains untouched.

We extend it.

This is OCP.

---

# Benefits of OCP

Following OCP provides several advantages:

- Easy to extend applications
- Reduces risk of breaking existing code
- Improves maintainability
- Supports Dependency Injection
- Encourages loose coupling
- Makes unit testing easier
- Improves scalability
- Easier team collaboration

---

# Common Mistakes

## Mistake 1

Adding new if-else blocks for every feature.

---

## Mistake 2

Using switch statements for every new requirement.

---

## Mistake 3

Changing existing classes every sprint.

---

## Mistake 4

Creating unnecessary interfaces for classes that will never change.

Use OCP where future extensions are likely.

---

# Best Practices

- Program to interfaces, not concrete classes.
- Use Dependency Injection.
- Prefer composition over modification.
- Keep existing classes stable.
- Use Strategy Pattern where multiple behaviors exist.
- Avoid large if-else chains.

---

# Interview Questions

## Question 1

What is the Open Closed Principle?

---

## Question 2

What is the difference between Extension and Modification?

---

## Question 3

Why do large if-else statements violate OCP?

---

## Question 4

How does Strategy Pattern implement OCP?

---

## Question 5

Give a real-world example of OCP.

---

## Question 6

How does ASP.NET Core Middleware follow OCP?

---

# Interview Answers

## What is OCP?

The Open Closed Principle states that software entities should be open for extension but closed for modification.

Existing code should remain unchanged while new functionality is added through extensions.

---

## What is Extension?

Adding new functionality without changing existing classes.

Example:

Adding a new payment class implementing `IPaymentMethod`.

---

## What is Modification?

Changing already tested code whenever a new requirement arrives.

This increases the chance of introducing bugs.

---

## Why do if-else chains violate OCP?

Because every new feature requires modifying existing logic.

As the application grows, the class becomes difficult to maintain and test.

---

## How does Strategy Pattern support OCP?

Strategy Pattern places different algorithms into separate classes implementing a common interface.

New algorithms are added through new classes instead of modifying existing ones.

---

## How does ASP.NET Core Middleware follow OCP?

ASP.NET Core allows developers to extend the request pipeline by adding custom middleware without modifying the framework itself.

---

# Summary

The Open Closed Principle teaches us to design software so that new features can be added through **extension** rather than **modification**.

Instead of changing stable classes every time business requirements evolve, we create new implementations using interfaces and polymorphism.

This approach leads to cleaner, safer, and more maintainable applications.

---

# Key Takeaways

✔ Open for Extension

✔ Closed for Modification

✔ Prefer Interfaces over if-else chains

✔ Strategy Pattern is a classic implementation of OCP

✔ ASP.NET Core Middleware demonstrates OCP beautifully

✔ OCP reduces bugs and protects existing code

✔ Dependency Injection and OCP work together

---

# Next Lesson

## Module 4 – Liskov Substitution Principle (LSP)

Topics Covered

- Definition of LSP
- IS-A Relationship
- Bad Inheritance Example
- Bird Example
- Rectangle-Square Problem
- Correct Design using Interfaces
- Real-world ASP.NET Core Examples
- Interview Questions