# 🚀 C# `throw` vs `throw ex`

> **Difficulty:** ⭐⭐⭐⭐☆
>
> **Interview Level:** Mid → Senior → Lead .NET Developer
>
> **Key Concept:** Both `throw` and `throw ex` rethrow exceptions, but **`throw`**** preserves the original stack trace**, whereas **`throw ex`**** resets the stack trace**, making debugging much harder.

---

# 📚 Table of Contents

* 🎯 Overview
* 🔹 What is `throw`?
* 🔹 What is `throw ex`?
* ⚡ Stack Trace
* 🧠 Internal Working
* 💻 Code Examples
* 📊 `throw` vs `throw ex`
* 🌍 Real-World Examples
* ✅ Best Practices
* ❌ Common Mistakes
* 🎤 Common Interview Questions
* 📌 Summary
* 🎯 Interview One-Liner

---

# 🎯 Overview

Exceptions help us identify and handle errors in an application.

Sometimes we catch an exception only to log it and then **rethrow** it.

There are two ways to rethrow an exception:

```csharp
throw;
```

or

```csharp
throw ex;
```

Although they look similar, they behave very differently.

The biggest difference is the **Stack Trace**.

---

# 🔹 What is `throw`?

`throw;` rethrows the **current exception**.

It **preserves the original stack trace**.

---

# 💻 Example

```csharp
try
{
    DoWork();
}
catch(Exception ex)
{
    Console.WriteLine(ex.Message);

    throw;
}
```

---

# ⚡ Execution Flow

```text
Method A

↓

Method B

↓

Method C

↓

Exception Occurs

↓

Catch Block

↓

throw;

↓

Original Exception Continues
```

The original exception information is preserved.

---

# 🔹 What is `throw ex`?

`throw ex;` throws the exception object **again as a new throw point**.

The stack trace starts from the `throw ex` statement.

---

# 💻 Example

```csharp
try
{
    DoWork();
}
catch(Exception ex)
{
    Console.WriteLine(ex.Message);

    throw ex;
}
```

---

# ⚡ Execution Flow

```text
Method A

↓

Method B

↓

Method C

↓

Exception

↓

Catch

↓

throw ex;

↓

New Stack Trace Starts Here
```

The original call stack is lost.

---

# 🧠 What is a Stack Trace?

A **Stack Trace** is the list of method calls that led to an exception.

Example

```text
Main()

↓

ProcessOrder()

↓

SaveOrder()

↓

InsertIntoDatabase()

↓

SqlException
```

The stack trace helps developers identify **where the error actually occurred**.

---

# 💻 Example

```csharp
static void Main()
{
    Method1();
}

static void Method1()
{
    Method2();
}

static void Method2()
{
    throw new Exception("Something went wrong");
}
```

Stack Trace

```text
Main()

↓

Method1()

↓

Method2()

↓

Exception
```

---

# 💻 Using `throw`

```csharp
try
{
    Method2();
}
catch(Exception)
{
    throw;
}
```

Stack Trace

```text
Main()

↓

Method1()

↓

Method2()

↓

Exception
```

The original call chain is preserved.

---

# 💻 Using `throw ex`

```csharp
try
{
    Method2();
}
catch(Exception ex)
{
    throw ex;
}
```

Stack Trace

```text
Catch Block

↓

throw ex

↓

Exception
```

The original methods (`Main`, `Method1`, `Method2`) are no longer shown.

---

# 📊 `throw` vs `throw ex`

| Feature                           | `throw` | `throw ex` |
| --------------------------------- | ------- | ---------- |
| Preserves Original Stack Trace    | ✅ Yes   | ❌ No       |
| Starts New Stack Trace            | ❌ No    | ✅ Yes      |
| Best for Debugging                | ✅ Yes   | ❌ No       |
| Original Error Location Available | ✅ Yes   | ❌ No       |
| Recommended                       | ✅ Yes   | ❌ No       |

---

# 🌍 Real-World Example

## Correct

```csharp
try
{
    SaveEmployee();
}
catch(Exception ex)
{
    logger.LogError(ex);

    throw;
}
```

The exception is logged and rethrown while preserving the original stack trace.

---

## Incorrect

```csharp
try
{
    SaveEmployee();
}
catch(Exception ex)
{
    logger.LogError(ex);

    throw ex;
}
```

The exception is logged, but the original stack trace is lost.

---

# ✅ Best Practices

✔ Use `throw;` when rethrowing the current exception.

✔ Log the exception before rethrowing if necessary.

✔ Preserve the original stack trace for debugging.

---

# ❌ Common Mistakes

❌ Using `throw ex;` inside a catch block.

❌ Losing the original stack trace.

❌ Making production issues harder to diagnose.

---

# 🎤 Common Interview Questions

## Q1. What is the difference between `throw` and `throw ex`?

**Answer**

* `throw` preserves the original stack trace.
* `throw ex` resets the stack trace.

---

## Q2. Which one should we use?

Always use **`throw;`** when rethrowing the current exception.

---

## Q3. Why is `throw ex` considered bad?

Because it hides the original location where the exception occurred.

---

## Q4. Can `throw ex` ever be used?

Generally, no.

If you need to throw a **different exception**, create a new exception and include the original exception as the **InnerException**.

Example

```csharp
catch(Exception ex)
{
    throw new ApplicationException(
        "Failed to process order.", ex);
}
```

---

# 📌 Summary

| Scenario                               | Recommended                      |
| -------------------------------------- | -------------------------------- |
| Rethrow Current Exception              | `throw`                          |
| Preserve Original Stack Trace          | `throw`                          |
| Log and Rethrow                        | `throw`                          |
| Wrap Exception with Additional Context | `throw new Exception("...", ex)` |
| Use `throw ex`                         | ❌ Avoid                          |

---

# 🎯 Interview One-Liner

* **`throw`** → Rethrows the current exception while preserving the original stack trace.
* **`throw ex`** → Rethrows the exception object but resets the stack trace, making debugging difficult.

---

# ⭐ Lead Interview Tip

A common senior interview question is:

> **"How do you add additional context to an exception without losing the original error?"**

**Answer:**

Create a new exception and pass the original exception as the **InnerException**.

```csharp
catch(Exception ex)
{
    throw new InvalidOperationException(
        "Unable to save employee data.", ex);
}
```

This preserves the original exception details while adding meaningful context for the caller.
