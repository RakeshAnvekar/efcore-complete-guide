# Factory Design Pattern in C#

# README 1 – Factory Design Pattern Fundamentals

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is the Factory Pattern?
2. Why Do We Need Factory Pattern?
3. Problems Without Factory Pattern
4. How Factory Pattern Solves the Problem
5. Real-World Example
6. Factory Pattern Architecture
7. Step-by-Step Implementation
8. Factory + Dependency Injection
9. Real Enterprise Examples
10. Factory vs Dependency Injection
11. Factory vs Strategy Pattern
12. Advantages
13. Disadvantages
14. Common Interview Questions
15. Key Takeaways

---

# 1. What is the Factory Pattern?

The **Factory Pattern** is a **Creational Design Pattern**.

Its responsibility is to **create objects** and hide the object creation logic from the client.

Instead of:

```csharp
var payment = new CreditCardPayment();
```

the client asks a Factory to create the object.

```text
Client

↓

Factory

↓

Creates Object

↓

Returns Interface
```

The client doesn't know which concrete class was created.

---

# 2. Why Do We Need Factory Pattern?

Suppose we are building an E-Commerce application.

The application supports multiple payment methods.

* Credit Card
* UPI
* PayPal
* Net Banking

Without a Factory

```csharp
public class PaymentService
{
    public void Process(string paymentType)
    {
        if(paymentType == "CreditCard")
        {
            var payment = new CreditCardPayment();
            payment.Pay();
        }
        else if(paymentType == "UPI")
        {
            var payment = new UpiPayment();
            payment.Pay();
        }
        else if(paymentType == "PayPal")
        {
            var payment = new PaypalPayment();
            payment.Pay();
        }
    }
}
```

Initially this looks fine.

As more payment providers are added, the class becomes difficult to maintain.

---

# 3. Problems Without Factory Pattern

## Tight Coupling

`PaymentService` directly depends on:

* CreditCardPayment
* UpiPayment
* PaypalPayment

The service knows every concrete implementation.

---

## Too Many if/else Statements

As new payment methods are introduced:

```text
Credit Card

UPI

PayPal

Google Pay

Apple Pay

Stripe

Wallet

Net Banking
```

The business logic becomes filled with conditional statements.

---

## Violates Open/Closed Principle (OCP)

Adding a new payment method requires modifying `PaymentService`.

The class is not closed for modification.

---

## Object Creation Mixed with Business Logic

Example

```csharp
var payment = new CreditCardPayment();

payment.Pay();
```

The class performs two responsibilities:

* Creating objects
* Executing business logic

---

# 4. How Factory Pattern Solves the Problem

Move object creation into a separate class.

Architecture

```text
Client

↓

PaymentService

↓

PaymentFactory

↓

Creates Payment Object

↓

Returns IPayment
```

Now

* PaymentService executes business logic.
* PaymentFactory creates objects.

Responsibilities are separated.

---

# 5. Real-World Example

Imagine buying coffee.

You ask:

> "One Cappuccino please."

The cashier does not prepare the coffee.

The coffee machine prepares it.

```text
Customer

↓

Cashier

↓

Coffee Machine (Factory)

↓

Coffee
```

The coffee machine acts as the Factory.

---

# 6. Factory Pattern Architecture

```text
Client

↓

PaymentService

↓

PaymentFactory

↓

IPayment

↓

CreditCardPayment

UpiPayment

PaypalPayment
```

The client communicates only with the Factory and the interface.

---

# 7. Step-by-Step Implementation

## Step 1 – Create Interface

```csharp
public interface IPayment
{
    void Pay();
}
```

---

## Step 2 – Implement Payment Classes

### Credit Card

```csharp
public class CreditCardPayment : IPayment
{
    public void Pay()
    {
        Console.WriteLine("Credit Card Payment");
    }
}
```

---

### UPI

```csharp
public class UpiPayment : IPayment
{
    public void Pay()
    {
        Console.WriteLine("UPI Payment");
    }
}
```

---

### PayPal

```csharp
public class PaypalPayment : IPayment
{
    public void Pay()
    {
        Console.WriteLine("PayPal Payment");
    }
}
```

---

## Step 3 – Create Factory

```csharp
public class PaymentFactory
{
    public IPayment Create(string paymentType)
    {
        if(paymentType == "CreditCard")
            return new CreditCardPayment();

        if(paymentType == "UPI")
            return new UpiPayment();

        if(paymentType == "PayPal")
            return new PaypalPayment();

        throw new Exception("Invalid Payment Type");
    }
}
```

Only the Factory creates objects.

---

## Step 4 – Use Factory

```csharp
public class PaymentService
{
    private readonly PaymentFactory _factory;

    public PaymentService(PaymentFactory factory)
    {
        _factory = factory;
    }

    public void Process(string paymentType)
    {
        var payment = _factory.Create(paymentType);

        payment.Pay();
    }
}
```

Notice

`PaymentService` never creates objects directly.

---

# 8. Factory + Dependency Injection

Register the Factory.

```csharp
builder.Services.AddScoped<PaymentFactory>();
```

Inject it.

```csharp
public class PaymentService
{
    private readonly PaymentFactory _factory;

    public PaymentService(PaymentFactory factory)
    {
        _factory = factory;
    }
}
```

ASP.NET Core creates the Factory automatically.

---

# 9. Real Enterprise Examples

## Payment Processing

```text
PaymentFactory

↓

Credit Card

UPI

PayPal

Wallet
```

---

## Notification System

```text
NotificationFactory

↓

Email

SMS

Push Notification

WhatsApp
```

---

## File Import

```text
FileParserFactory

↓

CSV Parser

Excel Parser

JSON Parser

XML Parser
```

---

## Cloud Storage

```text
StorageFactory

↓

Azure Blob

AWS S3

Google Cloud Storage
```

---

# 10. Factory vs Dependency Injection

| Factory                              | Dependency Injection                           |
| ------------------------------------ | ---------------------------------------------- |
| Chooses which object to create       | Creates and injects registered objects         |
| Business decision                    | Lifetime management                            |
| Runtime selection                    | Object resolution                              |
| Can create different implementations | Usually resolves one configured implementation |

Example

```text
Payment Type

↓

Factory

↓

UPI Payment
```

DI simply provides the Factory.

---

# 11. Factory vs Strategy Pattern

| Factory                    | Strategy               |
| -------------------------- | ---------------------- |
| Creates an object          | Executes behavior      |
| Focuses on object creation | Focuses on algorithms  |
| Returns an implementation  | Uses an implementation |

Often they are used together.

Example

```text
PaymentFactory

↓

Returns UpiPaymentStrategy

↓

PaymentService

↓

Pay()
```

---

# 12. Advantages

* Encapsulates object creation.
* Reduces coupling.
* Improves maintainability.
* Supports Open/Closed Principle.
* Simplifies business services.
* Centralizes creation logic.
* Makes adding new implementations easier.

---

# 13. Disadvantages

* Adds additional classes.
* Factory may become large if it creates many unrelated objects.
* Can become a maintenance bottleneck if all creation logic is placed into a single class.

---

# 14. Common Interview Questions

### What is the Factory Pattern?

A creational design pattern that centralizes object creation and hides the creation logic from the client.

---

### Why use a Factory?

To separate object creation from business logic and reduce coupling.

---

### What problem does Factory solve?

It eliminates direct object creation (`new`) inside business classes and centralizes creation logic.

---

### Does Factory replace Dependency Injection?

No.

* Dependency Injection manages dependencies and lifetimes.
* Factory decides which implementation to create based on runtime conditions.

---

### Is Factory a Creational Pattern?

Yes.

It belongs to the **Creational Design Patterns** category defined by the Gang of Four (GoF).

---

### When should Factory Pattern be used?

When:

* Multiple implementations exist.
* Object creation depends on runtime input.
* Complex object creation should be centralized.
* Business classes should not know concrete implementations.

---

# 15. Key Takeaways

* Factory Pattern is a **Creational Design Pattern**.
* It centralizes object creation.
* It hides concrete implementation details from clients.
* It reduces coupling.
* It supports the Open/Closed Principle.
* It works very well with Dependency Injection.
* It is commonly used for payment providers, notification channels, file parsers, cloud storage providers, and other runtime-selected implementations.
* Factory and Strategy are often used together in enterprise applications.

---

# What's Next?

**README 2 – Factory Pattern + Strategy Pattern (Enterprise Implementation)**

Topics include:

* Payment Processing using Factory + Strategy
* Notification System
* Shipping Provider Selection
* Discount Engine
* Dependency Injection with Factories
* Eliminating `if/else` using dictionaries and DI
* Real enterprise architecture used in banking and e-commerce applications
