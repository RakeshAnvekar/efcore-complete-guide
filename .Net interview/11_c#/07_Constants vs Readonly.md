# 🚀 C# `const` vs `readonly`

> **Difficulty:** ⭐⭐⭐☆☆
>
> **Interview Level:** Mid → Senior → Lead .NET Developer
>
> **Key Concept:** Both `const` and `readonly` make values immutable, but **`const` is a compile-time constant**, whereas **`readonly` is a runtime constant**.

---

# 📚 Table of Contents

* 🎯 Overview
* 🔹 What is `const`?
* 🔹 What is `readonly`?
* ⚡ Compile Time vs Runtime
* 🧠 Memory Representation
* 💻 Code Examples
* 📊 `const` vs `readonly`
* 🌍 Real-World Examples
* ✅ Advantages & Disadvantages
* 💡 Best Practices
* 🎤 Common Interview Questions
* 📌 Summary
* 🎯 Interview One-Liner

---

# 🎯 Overview

C# provides two ways to create immutable values.

* **`const`** → Value is fixed at **compile time**.
* **`readonly`** → Value is assigned at **runtime** and cannot be changed afterward.

Although both prevent modification, they are designed for different scenarios.

```text
                  Immutable Values
                         │
          ┌──────────────┴──────────────┐
          │                             │
       const                       readonly
          │                             │
   Compile Time                  Runtime
```

---

# 🔹 What is `const`?

A **constant (`const`)** is a value that is known **during compilation**.

Once declared, its value can **never change**.

---

# 💻 Example

```csharp
public class MathHelper
{
    public const double PI = 3.14159;
}

Console.WriteLine(MathHelper.PI);
```

Output

```
3.14159
```

---

# ⚡ Compiler Behavior

```csharp
public const int Days = 7;

Console.WriteLine(Days);
```

The compiler converts it into

```csharp
Console.WriteLine(7);
```

### Compiler Flow

```text
Source Code

Days

      │

      ▼

Compiler

      │

      ▼

Literal Value (7)

      │

      ▼

Generated IL
```

The compiler replaces every usage of the constant with its actual value.

---

# ❌ Can We Modify a `const`?

```csharp
public const int Days = 7;

Days = 10;
```

Compile-Time Error

```
The left-hand side of an assignment must be a variable.
```

---

# ✅ Allowed Types

```csharp
const int Age = 25;

const string Company = "OpenAI";

const double PI = 3.14159;

const bool IsActive = true;
```

---

# ❌ Invalid Example

```csharp
const DateTime Today = DateTime.Now;
```

### Why?

Because `DateTime.Now` is determined **at runtime**, not during compilation.

---

# 🔹 What is `readonly`?

A **readonly** field is assigned either

* During declaration
* Inside the constructor

Once initialization is complete, it cannot be modified.

---

# 💻 Example

```csharp
public class Employee
{
    public readonly int EmployeeId;

    public Employee(int id)
    {
        EmployeeId = id;
    }
}

Employee emp = new Employee(101);

Console.WriteLine(emp.EmployeeId);
```

Output

```
101
```

---

# 💻 Runtime Initialization

```csharp
public class Employee
{
    public readonly DateTime CreatedOn;

    public Employee()
    {
        CreatedOn = DateTime.Now;
    }
}
```

This works because the value is assigned while the object is being created.

---

# ❌ Can We Modify a `readonly` Field?

```csharp
EmployeeId = 200;
```

Compile-Time Error

```
A readonly field cannot be assigned to except in a constructor.
```

---

# ⚡ Compile-Time vs Runtime

## const

```text
Source Code

↓

Compiler Knows Value

↓

Value Embedded Into Assembly

↓

Application Runs
```

---

## readonly

```text
Object Created

↓

Constructor Executes

↓

Field Gets Value

↓

Cannot Change
```

---

# 🧠 Memory Representation

## const

```text
Source Code

PI

↓

Compiler

↓

3.14159

(No field lookup)
```

The value is embedded into the compiled code.

---

## readonly

```text
Stack

Employee Reference

      │

      ▼

Heap

-------------------------
Employee Object
-------------------------
EmployeeId = 101
CreatedOn  = Current Time
-------------------------
```

The field exists as part of the object.

---

# 📊 `const` vs `readonly`

| Feature                  | `const`         | `readonly`        |
| ------------------------ | --------------- | ----------------- |
| Value Assigned           | Compile Time    | Runtime           |
| Can Change Later         | ❌ No            | ❌ No              |
| Constructor Assignment   | ❌ No            | ✅ Yes             |
| Supports Runtime Values  | ❌ No            | ✅ Yes             |
| Can Use `DateTime.Now`   | ❌ No            | ✅ Yes             |
| Can Use `Guid.NewGuid()` | ❌ No            | ✅ Yes             |
| Static by Default        | ✅ Yes           | ❌ No              |
| Instance Specific        | ❌ No            | ✅ Yes             |
| Memory                   | Value Inlined   | Stored as Field   |
| Best For                 | Fixed Constants | Runtime Constants |

---

# 🌍 Real-World Examples

## ✅ Use `const`

```csharp
public const double PI = 3.14159;

public const int DaysInWeek = 7;

public const string CompanyName = "OpenAI";
```

Examples

* Mathematical constants
* Error codes
* Number of months
* Number of days
* Fixed messages

---

## ✅ Use `readonly`

```csharp
public readonly Guid EmployeeId;

public readonly DateTime CreatedOn;

public readonly string ConnectionString;
```

Examples

* Employee ID
* Order ID
* Created Date
* API Key
* Configuration Value

---

## ✅ Use `static readonly`

```csharp
public static readonly DateTime ApplicationStarted =
    DateTime.Now;
```

Examples

* Application Start Time
* Configuration Cache
* Shared Runtime Values

---

# ✅ Advantages

## const

* Fast access
* Compile-time optimization
* No runtime initialization
* Prevents accidental modification
* Simple and efficient

---

## readonly

* Supports runtime values
* Constructor initialization
* Object-specific values
* More flexible than `const`

---

# ❌ Disadvantages

## const

* Supports only compile-time values
* Cannot use `DateTime.Now`
* Cannot use `Guid.NewGuid()`
* Implicitly static
* Updating a public `const` in a library requires dependent applications to be recompiled.

---

## readonly

* Requires memory for the field
* Slightly slower than `const` (field lookup)
* Cannot be modified after construction

---

# 💡 Best Practices

✅ Use **`const`** for values that will **never change**.

Examples:

* PI
* Days in Week
* Company Name
* Mathematical Constants

---

✅ Use **`readonly`** when the value is determined during object creation.

Examples:

* Employee ID
* Created Date
* Connection String
* Configuration Values

---

✅ Use **`static readonly`** when all objects should share one runtime value.

Examples:

* Application Start Time
* Cached Configuration
* Shared Settings

---

# 🎤 Common Interview Questions

### Q1. What is the biggest difference between `const` and `readonly`?

**Answer**

* `const` → Compile-time constant.
* `readonly` → Runtime constant.

---

### Q2. Why can't `DateTime.Now` be `const`?

Because its value is available only at runtime.

---

### Q3. Why is every `const` implicitly static?

Because a constant belongs to the type, not to an object.

---

### Q4. Can `readonly` be static?

Yes.

```csharp
public static readonly Guid ApplicationId = Guid.NewGuid();
```

---

### Q5. Which is faster?

`const` is slightly faster because the compiler replaces it with the actual value during compilation.

---

### Q6. When should I use `static readonly` instead of `const`?

When the value is:

* Known only at runtime
* Shared across all instances

Examples:

* `DateTime.Now`
* `Guid.NewGuid()`
* Configuration values

---

# 📌 Summary

| Scenario                                       | Recommended       |
| ---------------------------------------------- | ----------------- |
| Value of PI                                    | `const`           |
| Number of Days in a Week                       | `const`           |
| Mathematical Constants                         | `const`           |
| Fixed Error Codes                              | `const`           |
| Application Name (Never Changes)               | `const`           |
| Employee ID                                    | `readonly`        |
| Order ID                                       | `readonly`        |
| User ID                                        | `readonly`        |
| Object Creation Time                           | `readonly`        |
| `DateTime.Now`                                 | `readonly`        |
| `Guid.NewGuid()`                               | `readonly`        |
| Configuration Value Loaded at Startup          | `readonly`        |
| Connection String                              | `readonly`        |
| API Key Loaded from Configuration              | `readonly`        |
| Runtime Calculated Value                       | `readonly`        |
| Shared Runtime Value for All Objects           | `static readonly` |
| Application Start Time                         | `static readonly` |
| Database Connection Timeout Loaded from Config | `static readonly` |

---

# 🎯 Interview One-Liner

* **`const`** → A compile-time constant whose value is embedded into the compiled code.
* **`readonly`** → A runtime-initialized field that can be assigned only during declaration or inside the constructor.
* **`static readonly`** → A runtime constant shared across all instances of a class.

---

# ⭐ Lead Interview Tip

A common senior-level interview question is:

> **"Why shouldn't you expose `public const` values from a shared library?"**

Because `const` values are **inlined into the consuming assembly at compile time**. If the library changes the constant later, applications that reference the library **must be recompiled** to pick up the new value. For values that may change between library versions, prefer **`public static readonly`**.
