# Module 30 -- Dependency Injection

# Part 7 -- Factory Pattern in ASP.NET Core (Complete Example)

## Learning Objectives

-   Understand why Factory Pattern is needed
-   Build a complete Factory example
-   Learn how DI works with Factory
-   Understand why `new` should be avoided
-   Learn interview best practices

------------------------------------------------------------------------

# Problem Statement

Suppose an e-commerce application supports multiple payment methods:

-   Credit Card
-   UPI
-   PayPal

The user selects a payment method at runtime.

Instead of writing `if`/`switch` statements throughout the application,
we centralize the object creation logic inside a Factory.

------------------------------------------------------------------------

# Project Structure

``` text
PaymentDemo
│
├── Interfaces
│   └── IPaymentService.cs
│
├── Services
│   ├── CreditCardPaymentService.cs
│   ├── UpiPaymentService.cs
│   └── PayPalPaymentService.cs
│
├── Factory
│   ├── IPaymentFactory.cs
│   └── PaymentFactory.cs
│
├── Controllers
│   └── PaymentController.cs
│
└── Program.cs
```

------------------------------------------------------------------------

# Step 1 - Interface

``` csharp
public interface IPaymentService
{
    void Pay(decimal amount);
}
```

------------------------------------------------------------------------

# Step 2 - Implementations

## Credit Card

``` csharp
public class CreditCardPaymentService : IPaymentService
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid ₹{amount} using Credit Card");
    }
}
```

## UPI

``` csharp
public class UpiPaymentService : IPaymentService
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid ₹{amount} using UPI");
    }
}
```

## PayPal

``` csharp
public class PayPalPaymentService : IPaymentService
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Paid ₹{amount} using PayPal");
    }
}
```

------------------------------------------------------------------------

# Step 3 - Factory Interface

``` csharp
public interface IPaymentFactory
{
    IPaymentService GetPaymentService(string paymentType);
}
```

------------------------------------------------------------------------

# Step 4 - Factory Implementation

``` csharp
public class PaymentFactory : IPaymentFactory
{
    private readonly IServiceProvider _serviceProvider;

    public PaymentFactory(IServiceProvider serviceProvider)
    {
        _serviceProvider = serviceProvider;
    }

    public IPaymentService GetPaymentService(string paymentType)
    {
        switch (paymentType.ToLower())
        {
            case "creditcard":
                return _serviceProvider
                    .GetRequiredService<CreditCardPaymentService>();

            case "upi":
                return _serviceProvider
                    .GetRequiredService<UpiPaymentService>();

            case "paypal":
                return _serviceProvider
                    .GetRequiredService<PayPalPaymentService>();

            default:
                throw new Exception("Invalid Payment Type");
        }
    }
}
```

------------------------------------------------------------------------

# Step 5 - Register Services

``` csharp
builder.Services.AddScoped<CreditCardPaymentService>();
builder.Services.AddScoped<UpiPaymentService>();
builder.Services.AddScoped<PayPalPaymentService>();

builder.Services.AddScoped<IPaymentFactory, PaymentFactory>();
```

------------------------------------------------------------------------

# Step 6 - Controller

``` csharp
[ApiController]
[Route("api/payment")]
public class PaymentController : ControllerBase
{
    private readonly IPaymentFactory _factory;

    public PaymentController(IPaymentFactory factory)
    {
        _factory = factory;
    }

    [HttpPost]
    public IActionResult Pay(string paymentType, decimal amount)
    {
        var paymentService = _factory.GetPaymentService(paymentType);

        paymentService.Pay(amount);

        return Ok("Payment Successful");
    }
}
```

------------------------------------------------------------------------

# Request Flow

``` text
Client
   │
   ▼
PaymentController
   │
   ▼
PaymentFactory
   │
   ▼
IServiceProvider
   │
   ├──────────────┬──────────────┐
   ▼              ▼              ▼
CreditCard     UpiPayment     PayPalPayment
Service         Service         Service
```

------------------------------------------------------------------------

# Why Not Use new?

Bad:

``` csharp
return new UpiPaymentService();
```

Problems:

-   Bypasses Dependency Injection
-   Lifetimes are ignored
-   Constructor dependencies are not injected
-   Hard to unit test

Good:

``` csharp
return _serviceProvider.GetRequiredService<UpiPaymentService>();
```

The DI container creates the object and injects all dependencies.

------------------------------------------------------------------------

# When to Use Factory Pattern

Use a Factory when:

-   Implementation depends on user input
-   Implementation depends on configuration
-   Business rules determine which implementation to use
-   Object selection happens at runtime

------------------------------------------------------------------------

# Interview Questions

## Why use a Factory?

To select the correct implementation at runtime without scattering
`if`/`switch` logic throughout the application.

## Why inject IServiceProvider only into the Factory?

Using `IServiceProvider` everywhere becomes the Service Locator
anti-pattern. Restrict it to infrastructure components like factories.

## Why not use new?

It bypasses the DI container, making dependency management, lifetimes,
and testing more difficult.

------------------------------------------------------------------------

# Quick Revision

-   Create an interface.
-   Create multiple implementations.
-   Create a factory interface.
-   Resolve the correct implementation using `IServiceProvider`.
-   Register everything with DI.
-   Inject the factory into controllers/services.

**Remember:** Constructor Injection is the default. Use a Factory only
when the implementation must be chosen dynamically at runtime.
