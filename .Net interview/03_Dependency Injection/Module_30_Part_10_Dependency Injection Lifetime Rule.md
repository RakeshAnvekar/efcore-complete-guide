# Dependency Injection Lifetime Rule

## A Longer-Lived Object Cannot Own a Shorter-Lived Object

---

# Introduction

One of the most confusing Dependency Injection (DI) concepts in .NET is understanding why:

```csharp
Singleton -> Scoped ❌
```

Many developers memorize this rule without understanding the reason behind it.

The actual concept is:

> A longer-lived object should not permanently hold a reference to a shorter-lived object.

Once you understand this principle, DI lifetimes become much easier.

---

# Service Lifetimes in .NET

ASP.NET Core supports three service lifetimes:

| Lifetime  | Duration                            |
| --------- | ----------------------------------- |
| Transient | New instance every time requested   |
| Scoped    | One instance per HTTP request       |
| Singleton | One instance for entire application |

Visual representation:

```text
Transient
    ↓
Scoped
    ↓
Singleton
```

```text
Transient  = Shortest Lifetime
Scoped     = Medium Lifetime
Singleton  = Longest Lifetime
```

---

# Real World Analogy

Imagine a company.

```text
Singleton = CEO
Scoped    = Project Manager
Transient = Consultant
```

### CEO (Singleton)

Exists throughout the company's life.

```text
Company Start
     |
     +---- CEO
     |
Company End
```

---

### Project Manager (Scoped)

Exists only for a specific project.

```text
Project A -> Manager A

Project B -> Manager B

Project C -> Manager C
```

Every project gets its own manager.

---

### Consultant (Transient)

Hired whenever needed.

```text
Need Help
   |
Hire Consultant
   |
Work Completed
   |
Consultant Gone
```

---

# Understanding the Problem

Suppose we have:

```csharp
services.AddSingleton<A>();
services.AddScoped<B>();
```

```csharp
public class A
{
    private readonly B _b;

    public A(B b)
    {
        _b = b;
    }
}
```

At first glance this looks fine.

However, it creates a lifetime mismatch.

---

# How Scoped Services Work

Scoped services are created per request.

Request 1:

```text
Request 1
   |
   +---- B1
```

Request 2:

```text
Request 2
   |
   +---- B2
```

Request 3:

```text
Request 3
   |
   +---- B3
```

Every request gets its own instance.

---

# How Singleton Services Work

Singleton services are created once.

```text
Application Start
     |
     +---- A
     |
Application End
```

Only one instance exists.

---

# The Big Question

When .NET creates A, it sees:

```text
A needs B
```

But which B?

```text
Request 1 -> B1

Request 2 -> B2

Request 3 -> B3
```

Should A store:

```text
B1 ?
```

or

```text
B2 ?
```

or

```text
B3 ?
```

There is no correct answer.

Therefore the container throws an exception.

---

# Runtime Error

```text
Cannot consume scoped service 'B'
from singleton 'A'
```

This happens because ASP.NET Core detects the lifetime mismatch.

---

# Why This Is Dangerous

Imagine .NET allowed it.

Request 1:

```text
Application
|
+---- A (Singleton)
        |
        +---- B1 (Scoped)
```

Now Request 1 completes.

```text
B1 Destroyed
```

But A still exists.

```text
Application
|
+---- A
        |
        +---- Reference to B1
```

A is alive.

B1 is gone.

A now holds a reference to an object whose lifetime has ended.

This is exactly what ASP.NET Core prevents.

---

# The Core Principle

A service with a longer lifetime should not permanently own a service with a shorter lifetime.

Bad:

```text
Singleton
    |
    +---- Scoped
```

Reason:

```text
Singleton lives longer than Scoped
```

---

# Why Singleton → Transient Works

This confuses many developers.

Example:

```csharp
services.AddSingleton<A>();
services.AddTransient<B>();
```

```csharp
public class A
{
    private readonly B _b;

    public A(B b)
    {
        _b = b;
    }
}
```

---

# What Happens Internally

When A is created:

```text
Create A
   |
   +---- Create B
```

Result:

```text
A
|
+---- B
```

Since A is created only once, B is also created once for that constructor injection.

---

# Important Interview Point

Although B is registered as Transient:

```csharp
services.AddTransient<B>();
```

The instance injected into A effectively lives as long as A.

```text
Application Start
|
+---- A
       |
       +---- B
|
Application End
```

No scope-related problem exists.

Therefore ASP.NET Core allows:

```text
Singleton -> Transient ✅
```

---

# Why Scoped Is Different

Scoped services require a request scope.

```text
Request 1 -> B1

Request 2 -> B2

Request 3 -> B3
```

A Singleton does not belong to any request.

```text
Singleton
|
Application Lifetime
```

Because there is no request context, ASP.NET Core cannot determine which Scoped instance should be injected.

---

# Lifetime Dependency Matrix

| From ↓ / To → | Singleton | Scoped | Transient |
| ------------- | --------- | ------ | --------- |
| Singleton     | ✅         | ❌      | ✅         |
| Scoped        | ✅         | ✅      | ✅         |
| Transient     | ✅         | ✅      | ✅         |

---

# Interview Answer

### Why can't a Singleton depend on a Scoped service?

A Singleton lives for the entire application, while a Scoped service exists only for a single request.

If a Singleton were allowed to hold a Scoped service, it could end up referencing an object that has already been disposed after the request completed.

To prevent this lifetime mismatch, ASP.NET Core throws:

```text
Cannot consume scoped service from singleton
```

---

# Easy Memory Trick

Remember only this:

```text
Transient  -> Short Lifetime

Scoped     -> Medium Lifetime

Singleton  -> Long Lifetime
```

And:

```text
Singleton -> Scoped ❌
```

Everything else is allowed.

---

# Final Takeaway

Do not memorize the table.

Understand the principle:

> A longer-lived object should not permanently hold a reference to a shorter-lived object whose lifetime may end first.

Once this concept is clear, Dependency Injection lifetimes become easy to understand and explain in interviews.
