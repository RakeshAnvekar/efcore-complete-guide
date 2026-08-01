# Module 4 – Liskov Substitution Principle (LSP)

> **Course:** SOLID Principles – Complete Master Roadmap

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. Introduction
3. Definition of LSP
4. Understanding Substitution
5. Real Meaning of LSP
6. Payment System Example
7. LSP Violation in Payment System
8. File Storage Example
9. LSP Violation in File Storage
10. Notification System Example
11. LSP Violation in Notification System
12. How to Prevent LSP Violations
13. Relationship Between LSP and ISP
14. Best Practices
15. Interview Questions
16. Interview Answers
17. Summary
18. Key Takeaways

---

# Learning Objectives

By the end of this lesson you will understand:

- What Liskov Substitution Principle (LSP) is
- What substitution means
- How LSP applies in real software
- How to identify LSP violations
- How to prevent LSP violations
- Enterprise examples using ASP.NET Core

---

# Introduction

LSP is one of the most misunderstood SOLID principles.

Most tutorials explain it using Birds and Penguins.

However, in real software development, we work with:

- Payment Systems
- Notification Systems
- Storage Providers
- Authentication Providers
- Logging Providers

Let's learn LSP using real software examples.

---

# Definition

Barbara Liskov defined the principle as:

> Objects of a derived class should be replaceable with objects of the base class without affecting the correctness of the application.

---

# Simple Meaning

Whenever we create an interface or base class, we are making a promise.

Example

```csharp
public interface IPayment
{
    void Pay(decimal amount);
}
```

The promise is:

> Every class implementing IPayment must be able to perform a payment.

If one implementation cannot perform payment, it violates LSP.

---

# What is Substitution?

Suppose we have:

```csharp
public interface IPayment
{
    void Pay(decimal amount);
}
```

Implementations

```
CreditCardPayment

UPIPayment

PayPalPayment
```

Payment Service

```csharp
public class PaymentService
{
    public void MakePayment(IPayment payment)
    {
        payment.Pay(1000);
    }
}
```

Usage

```csharp
PaymentService service = new PaymentService();

service.MakePayment(new CreditCardPayment());

service.MakePayment(new UpiPayment());

service.MakePayment(new PayPalPayment());
```

Output

```
Paid using Credit Card

Paid using UPI

Paid using PayPal
```

Notice something.

PaymentService doesn't care which implementation it receives.

Every implementation works correctly.

This is called **Substitution**.

Any implementation can replace another.

LSP is satisfied.

---

# Payment System Example

Interface

```csharp
public interface IPayment
{
    void Pay(decimal amount);
}
```

---

Credit Card

```csharp
public class CreditCardPayment : IPayment
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid {amount} using Credit Card");
    }
}
```

---

UPI

```csharp
public class UpiPayment : IPayment
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid {amount} using UPI");
    }
}
```

---

PayPal

```csharp
public class PayPalPayment : IPayment
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid {amount} using PayPal");
    }
}
```

---

Payment Service

```csharp
public class PaymentService
{
    public void MakePayment(IPayment payment)
    {
        payment.Pay(1000);
    }
}
```

Everything works.

This follows LSP.

---

# LSP Violation in Payment System

Suppose another developer writes

```csharp
public class CashPayment : IPayment
{
    public void Pay(decimal amount)
    {
        throw new NotSupportedException();
    }
}
```

Usage

```csharp
service.MakePayment(new CashPayment());
```

Output

```
Exception

NotSupportedException
```

Why?

Because PaymentService expected every IPayment to successfully perform payment.

CashPayment broke that promise.

This violates LSP.

---

# File Storage Example

Interface

```csharp
public interface IFileStorage
{
    void Upload(string fileName);
}
```

---

Azure Storage

```csharp
public class AzureStorage : IFileStorage
{
    public void Upload(string fileName)
    {
        Console.WriteLine("Uploaded to Azure");
    }
}
```

---

Amazon Storage

```csharp
public class AmazonStorage : IFileStorage
{
    public void Upload(string fileName)
    {
        Console.WriteLine("Uploaded to AWS");
    }
}
```

---

Google Storage

```csharp
public class GoogleStorage : IFileStorage
{
    public void Upload(string fileName)
    {
        Console.WriteLine("Uploaded to Google Cloud");
    }
}
```

---

Service

```csharp
public class FileService
{
    public void UploadFile(IFileStorage storage)
    {
        storage.Upload("Resume.pdf");
    }
}
```

Usage

```csharp
service.UploadFile(new AzureStorage());

service.UploadFile(new AmazonStorage());

service.UploadFile(new GoogleStorage());
```

Everything works.

LSP is satisfied.

---

# LSP Violation in File Storage

```csharp
public class ReadOnlyStorage : IFileStorage
{
    public void Upload(string fileName)
    {
        throw new NotSupportedException();
    }
}
```

Now

```csharp
service.UploadFile(new ReadOnlyStorage());
```

The application crashes.

Reason

ReadOnlyStorage cannot fulfill the contract of IFileStorage.

Therefore,

LSP is violated.

---

# Notification System Example

Interface

```csharp
public interface INotification
{
    void Send(string message);
}
```

---

Email

```csharp
public class EmailNotification : INotification
{
    public void Send(string message)
    {
        Console.WriteLine("Email Sent");
    }
}
```

---

SMS

```csharp
public class SmsNotification : INotification
{
    public void Send(string message)
    {
        Console.WriteLine("SMS Sent");
    }
}
```

---

WhatsApp

```csharp
public class WhatsAppNotification : INotification
{
    public void Send(string message)
    {
        Console.WriteLine("WhatsApp Sent");
    }
}
```

---

Notification Service

```csharp
public class NotificationService
{
    public void Notify(INotification notification)
    {
        notification.Send("Hello");
    }
}
```

Usage

```csharp
Notify(new EmailNotification());

Notify(new SmsNotification());

Notify(new WhatsAppNotification());
```

Works correctly.

LSP is satisfied.

---

# LSP Violation in Notification System

```csharp
public class DisabledNotification : INotification
{
    public void Send(string message)
    {
        throw new NotSupportedException();
    }
}
```

Usage

```csharp
Notify(new DisabledNotification());
```

Application crashes.

Again,

LSP is violated.

---

# How Do We Prevent LSP Violations?

There are three common approaches.

---

## 1. Do Not Implement an Interface That Doesn't Fit

Bad

```csharp
public class CashPayment : IPayment
{
    public void Pay(decimal amount)
    {
        throw new NotSupportedException();
    }
}
```

Better

```csharp
public class CashPayment
{
    public void ReceiveCash(decimal amount)
    {
        Console.WriteLine("Cash Received");
    }
}
```

If a class cannot fulfill the contract, it should not implement the interface.

---

## 2. Split Large Interfaces

Instead of

```csharp
public interface IPayment
{
    void Pay(decimal amount);

    void Refund(decimal amount);
}
```

Create

```csharp
public interface IPayment
{
    void Pay(decimal amount);
}

public interface IRefundable
{
    void Refund(decimal amount);
}
```

Now

```csharp
CreditCardPayment

↓

IPayment

↓

IRefundable
```

Gift Card

```
GiftCardPayment

↓

IPayment
```

Nobody is forced to implement unsupported methods.

---

## 3. Use Composition

Instead of inheritance

```
PaymentProcessor

↓

Credit Card

↓

UPI

↓

PayPal
```

Inject behavior.

```csharp
public interface IPaymentGateway
{
    void Pay(decimal amount);
}
```

Then

```csharp
public class PaymentProcessor
{
    private readonly IPaymentGateway gateway;

    public PaymentProcessor(IPaymentGateway gateway)
    {
        this.gateway = gateway;
    }

    public void Process(decimal amount)
    {
        gateway.Pay(amount);
    }
}
```

This is more flexible and avoids incorrect inheritance.

---

# Relationship Between LSP and ISP

Notice something.

We solved one LSP violation by splitting interfaces.

```
IPayment

↓

Pay()
```

```
IRefundable

↓

Refund()
```

This is actually the next SOLID principle.

**Interface Segregation Principle (ISP)** helps prevent LSP violations by creating small, focused interfaces.

---

# Best Practices

✔ Every implementation should honor the interface contract.

✔ Avoid throwing NotSupportedException inside interface implementations.

✔ Prefer interfaces over incorrect inheritance.

✔ Use composition when inheritance is not appropriate.

✔ If one implementation cannot support a method, redesign the abstraction.

---

# Interview Questions

## Question 1

What is the Liskov Substitution Principle?

---

## Question 2

What does substitution mean?

---

## Question 3

Give a real software example of LSP.

---

## Question 4

Why does NotSupportedException often indicate an LSP violation?

---

## Question 5

How do you prevent LSP violations?

---

## Question 6

What is the relationship between LSP and ISP?

---

# Interview Answers

## What is LSP?

LSP states that every derived class or interface implementation should be replaceable with its base type without changing the correctness of the application.

---

## What is substitution?

Substitution means replacing one implementation with another without changing client code.

---

## Why does NotSupportedException indicate an LSP violation?

Because the implementation is not honoring the contract promised by the interface or base class.

---

## How do you prevent LSP violations?

- Design better abstractions.
- Split large interfaces.
- Avoid incorrect inheritance.
- Prefer composition where appropriate.

---

## Relationship between LSP and ISP?

ISP creates small interfaces.

Smaller interfaces reduce the chances of classes implementing methods they cannot support.

This naturally helps satisfy LSP.

---

# Summary

The Liskov Substitution Principle is about **keeping promises**.

Whenever you create an interface or base class, every implementation must fully honor its contract.

If one implementation behaves differently, throws exceptions, or cannot perform the required operation, the design should be reconsidered.

LSP encourages predictable, reliable, and maintainable software by ensuring that all implementations can safely replace one another.

---

# Key Takeaways

✔ LSP means **keep the promise of the interface or base class**.

✔ Every implementation should be replaceable.

✔ Never implement an interface if the class cannot support all its methods.

✔ Avoid `NotSupportedException` in interface implementations.

✔ Use better abstractions, smaller interfaces, and composition to avoid LSP violations.

✔ LSP and ISP work together to create clean, maintainable software.