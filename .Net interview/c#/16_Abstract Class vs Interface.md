# 🚀 C# Abstract Class vs Interface

## 📖 Overview

Both **Abstract Classes** and **Interfaces** are used to achieve **abstraction** in C#.

Although they may look similar, they solve different problems.

- **Abstract Class** → Used when multiple classes are **closely related** and can share common code and state.
- **Interface** → Used to define a **contract** that any class can implement, regardless of inheritance.

---

# 🎯 Why Do We Need Them?

Imagine you're building a Payment System.

You have multiple payment methods:

- Credit Card
- UPI
- Net Banking
- PayPal

All of them support **Payment**.

But each payment method implements it differently.

```
                Payment

                   │

      ┌────────────┼────────────┐

      │            │            │

 Credit Card      UPI      Net Banking
```

Instead of writing unrelated code, we define a common contract.

This is where **Interfaces** come in.

---

Now consider another example.

```
              Animal

      Name

      Age

      Weight

      Sleep()

      Breathe()

              │

      ┌───────┴────────┐

      │                │

     Dog             Cow

     Eat()          Eat()
```

Every animal has

- Name
- Age
- Sleep()
- Breathe()

But every animal eats differently.

Here we need both

- Shared implementation
- Mandatory methods

This is where **Abstract Classes** are useful.

---

# 🔹 What is an Abstract Class?

An **Abstract Class** is an incomplete class.

It **cannot be instantiated**.

It acts as a **base class** that provides:

- Common fields
- Common methods
- Constructors
- Abstract methods

Some methods can have implementation, while others must be implemented by derived classes.

---

## Example

```csharp
public abstract class Animal
{
    public void Sleep()
    {
        Console.WriteLine("Sleeping...");
    }

    public abstract void Eat();
}

public class Dog : Animal
{
    public override void Eat()
    {
        Console.WriteLine("Dog Eats Meat");
    }
}
```

Usage

```csharp
Animal animal = new Dog();

animal.Sleep();

animal.Eat();
```

Output

```
Sleeping...

Dog Eats Meat
```

---

# Why Use an Abstract Class?

Use an Abstract Class when:

- Classes are closely related.
- They share common code.
- They share fields.
- They share constructors.
- You want to provide default implementations.

Examples

- Animal
- Vehicle
- Employee
- Shape

---

# 🔹 What is an Interface?

An **Interface** is a contract.

It specifies **what a class must do**, but not **how it should do it**.

An interface contains method declarations that implementing classes must provide.

---

## Example

```csharp
public interface IPayment
{
    void Pay();
}

public class CreditCard : IPayment
{
    public void Pay()
    {
        Console.WriteLine("Credit Card Payment");
    }
}

public class UPI : IPayment
{
    public void Pay()
    {
        Console.WriteLine("UPI Payment");
    }
}
```

Usage

```csharp
IPayment payment = new UPI();

payment.Pay();
```

Output

```
UPI Payment
```

---

# Why Use an Interface?

Use an Interface when:

- Classes are unrelated.
- Only behavior matters.
- You want loose coupling.
- You need Dependency Injection.
- You want multiple inheritance.

Examples

- ILogger
- IRepository
- IDisposable
- IComparable
- IEnumerable

---

# 🧠 Visual Difference

## Abstract Class

```
             Animal

     Name

     Age

     Sleep()

     Breathe()

          │

     Dog

     Cow
```

Shared implementation + common state.

---

## Interface

```
          IPayment

          Pay()

             │

   ┌─────────┼─────────┐

   │         │         │

CreditCard  UPI    NetBanking
```

Only defines behavior.

---

# 📊 Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|----------|---------------|-----------|
| Constructor | ✅ Yes | ❌ No |
| Fields | ✅ Yes | ❌ No |
| Normal Methods | ✅ Yes | ✅ (Default implementations in modern C#) |
| Abstract Methods | ✅ Yes | ✅ |
| Multiple Inheritance | ❌ No | ✅ Yes |
| Shared Implementation | ✅ Yes | Limited |
| Instance State | ✅ Yes | ❌ No |
| Best Use | Base Class | Contract |

---

# 📌 Summary

| Scenario | Recommended |
|----------|-------------|
| Animal → Dog, Cat | Abstract Class |
| Vehicle → Car, Bike | Abstract Class |
| Employee Base Class | Abstract Class |
| Shape Hierarchy | Abstract Class |
| Payment System | Interface |
| Logging | Interface |
| Repository Pattern | Interface |
| Dependency Injection | Interface |
| Multiple Inheritance | Interface |
| Loose Coupling | Interface |

---

# 🎯 Interview One-Liner

- **Abstract Class** → Use when classes are closely related and need to share common state and implementation.
- **Interface** → Use when you want to define a contract that multiple unrelated classes can implement.