# Module 30 -- Dependency Injection

# Part 9 -- Keyed Services (.NET 8)

## Learning Objectives

-   Understand Keyed Services
-   Register multiple implementations using keys
-   Resolve services by key
-   Compare Factory Pattern vs Keyed Services
-   Learn interview questions

------------------------------------------------------------------------

# Problem Before .NET 8

``` csharp
public interface IPaymentService
{
    void Pay(decimal amount);
}
```

Implementations:

-   CreditCardPaymentService
-   UpiPaymentService
-   PayPalPaymentService

Traditional registration:

``` csharp
builder.Services.AddScoped<IPaymentService, CreditCardPaymentService>();
builder.Services.AddScoped<IPaymentService, UpiPaymentService>();
builder.Services.AddScoped<IPaymentService, PayPalPaymentService>();
```

Injecting `IPaymentService` returns the **last registered**
implementation.

------------------------------------------------------------------------

# What are Keyed Services?

Keyed Services allow multiple implementations of the same interface to
be registered with unique keys.

Example:

``` text
creditcard -> CreditCardPaymentService
upi        -> UpiPaymentService
paypal     -> PayPalPaymentService
```

------------------------------------------------------------------------

# Register Keyed Services

``` csharp
builder.Services.AddKeyedScoped<IPaymentService, CreditCardPaymentService>("creditcard");
builder.Services.AddKeyedScoped<IPaymentService, UpiPaymentService>("upi");
builder.Services.AddKeyedScoped<IPaymentService, PayPalPaymentService>("paypal");
```

------------------------------------------------------------------------

# Inject Keyed Services

``` csharp
public class PaymentController
{
    public PaymentController(
        [FromKeyedServices("upi")]
        IPaymentService paymentService)
    {
    }
}
```

The DI container injects `UpiPaymentService`.

------------------------------------------------------------------------

# Banking Example

``` csharp
builder.Services.AddKeyedScoped<ITransferService, NeftTransferService>("neft");
builder.Services.AddKeyedScoped<ITransferService, RtgsTransferService>("rtgs");
builder.Services.AddKeyedScoped<ITransferService, ImpsTransferService>("imps");
```

------------------------------------------------------------------------

# Internal Working

``` text
IPaymentService

creditcard -> CreditCardPaymentService
upi        -> UpiPaymentService
paypal     -> PayPalPaymentService
```

`[FromKeyedServices("upi")]` tells the container to resolve the service
registered with the `"upi"` key.

------------------------------------------------------------------------

# Factory Pattern vs Keyed Services

  Feature                   Factory   Keyed Services
  ------------------------- --------- ----------------
  Extra Factory Class       Yes       No
  Uses IServiceProvider     Usually   No
  Built into ASP.NET Core   No        Yes (.NET 8+)
  Boilerplate               More      Less

------------------------------------------------------------------------

# When to Use

Use Keyed Services for:

-   Payment providers
-   Notification providers
-   Shipping providers
-   Cloud providers
-   Tax calculators

Use Factory Pattern when runtime selection requires complex business
rules.

------------------------------------------------------------------------

# Interview Questions

## What are Keyed Services?

A .NET 8 feature that allows multiple implementations of the same
interface to be registered and resolved using keys.

## Registration Method

``` csharp
AddKeyedScoped()
```

Other methods:

-   AddKeyedSingleton()
-   AddKeyedTransient()

## Injection Attribute

``` csharp
[FromKeyedServices("key")]
```

## Which .NET Version?

.NET 8

## Do they replace Factory Pattern?

Not always. Factories are still useful for complex runtime selection.

------------------------------------------------------------------------

# Quick Revision

-   Register using `AddKeyedScoped()`
-   Resolve using `[FromKeyedServices()]`
-   Cleaner than Factory Pattern
-   Built into .NET 8

------------------------------------------------------------------------

# Key Takeaways

-   Keyed Services simplify multiple implementations.
-   They reduce the need for custom factories.
-   Use them when the implementation is selected by a known key.
