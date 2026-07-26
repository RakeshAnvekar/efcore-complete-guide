# Module 35.2 – try-catch-finally in C#

> **Course:** ASP.NET Core – Complete Interview Roadmap
>
> **Module:** 35.2 – try-catch-finally
>
> **Level:** Beginner → Advanced
>
> **Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Why Do We Need Exception Handling?
2. What is try?
3. What is catch?
4. Catching Specific Exceptions
5. Exception Object
6. Multiple catch Blocks
7. Catch Block Order
8. What is finally?
9. Why finally is Important
10. Execution Flow
11. try-finally
12. Nested try-catch
13. Exception Filters (when)
14. Internal CLR Working
15. Best Practices
16. Common Mistakes
17. Interview Questions
18. Summary

---

# 1. Why Do We Need Exception Handling?

Applications interact with databases, files, APIs, and user input.

Unexpected situations can occur at runtime.

Example:

```csharp
int number = 10;

int result = number / 0;
```

Output

```
System.DivideByZeroException
```

Without exception handling, the application terminates unexpectedly.

Exception handling allows the application to recover gracefully.

---

# 2. What is try?

The **try** block contains code that may throw an exception.

Syntax

```csharp
try
{
    // Risky code
}
```

Example

```csharp
try
{
    int number = 10;
    int result = number / 0;
}
```

The CLR monitors every statement inside the try block.

If no exception occurs, execution continues normally.

---

# 3. What is catch?

The **catch** block executes only if an exception occurs inside the associated try block.

Example

```csharp
try
{
    int number = 10;
    int result = number / 0;
}
catch
{
    Console.WriteLine("Something went wrong.");
}
```

Output

```
Something went wrong.
```

Instead of crashing, the application handles the exception.

---

# 4. Catching Specific Exceptions

Instead of catching every exception, catch only the ones you can handle.

Example

```csharp
try
{
    int result = 10 / 0;
}
catch (DivideByZeroException)
{
    Console.WriteLine("Cannot divide by zero.");
}
```

This catch block only handles `DivideByZeroException`.

---

# 5. Exception Object

When an exception occurs, the CLR creates an exception object.

```csharp
try
{
    int result = 10 / 0;
}
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
    Console.WriteLine(ex.StackTrace);
}
```

Useful properties:

| Property | Description |
|----------|-------------|
| Message | Error message |
| StackTrace | Location where exception occurred |
| Source | Assembly that generated the exception |
| InnerException | Original exception if wrapped |
| HelpLink | Documentation link (optional) |

---

# 6. Multiple catch Blocks

A try block can have multiple catch blocks.

```csharp
try
{
    // Risky Code
}
catch (DivideByZeroException)
{
    Console.WriteLine("Divide by zero.");
}
catch (FormatException)
{
    Console.WriteLine("Invalid format.");
}
catch (Exception)
{
    Console.WriteLine("Unknown exception.");
}
```

The CLR executes the **first matching catch block**.

---

# 7. Catch Block Order

Incorrect

```csharp
catch(Exception)
{
}

catch(DivideByZeroException)
{
}
```

Compiler Error

```
Unreachable catch block
```

Correct

```csharp
catch(DivideByZeroException)
{
}

catch(Exception)
{
}
```

Always place:

- Specific exceptions first
- General `Exception` last

---

# 8. What is finally?

The **finally** block contains cleanup code.

It executes whether an exception occurs or not.

Example

```csharp
try
{
    Console.WriteLine("Processing...");
}
catch
{
    Console.WriteLine("Error");
}
finally
{
    Console.WriteLine("Cleanup");
}
```

Output

```
Processing...

Cleanup
```

---

# 9. Why finally is Important

Suppose we open a file.

```csharp
File.Open(...);
```

The file must always be closed.

```
Open File

↓

Read File

↓

Exception?

↓

Yes / No

↓

Close File
```

The cleanup belongs in the finally block.

Real-world examples:

- Closing files
- Closing database connections
- Releasing locks
- Closing network streams
- Cleaning unmanaged resources

---

# 10. Execution Flow

Example

```csharp
try
{
    Console.WriteLine("A");

    int result = 10 / 0;

    Console.WriteLine("B");
}
catch
{
    Console.WriteLine("C");
}
finally
{
    Console.WriteLine("D");
}

Console.WriteLine("E");
```

Output

```
A

C

D

E
```

Execution Flow

```
Start

↓

try

↓

Statement A

↓

Exception?

↓

Yes

↓

Skip Remaining try

↓

catch

↓

finally

↓

Continue Program
```

---

# 11. try-finally

A catch block is optional.

Example

```csharp
try
{
    Console.WriteLine("Reading File");
}
finally
{
    Console.WriteLine("Closing File");
}
```

If an exception occurs:

- finally executes
- Exception continues propagating

Output

```
Closing File

Unhandled Exception
```

---

# 12. Nested try-catch

Example

```csharp
try
{
    try
    {
        int result = 10 / 0;
    }
    catch
    {
        Console.WriteLine("Inner Catch");
    }
}
catch
{
    Console.WriteLine("Outer Catch");
}
```

Output

```
Inner Catch
```

Since the inner catch handles the exception, the outer catch never executes.

---

# 13. Exception Filters (when)

C# allows conditional catch blocks.

Example

```csharp
try
{
    throw new Exception("Database Error");
}
catch(Exception ex) when (ex.Message.Contains("Database"))
{
    Console.WriteLine("Database Exception");
}
```

The catch block executes only when the condition evaluates to true.

---

# 14. Internal CLR Working

When no exception occurs

```
try

↓

Execute Statements

↓

finally

↓

Continue
```

When an exception occurs

```
try

↓

Exception Thrown

↓

Create Exception Object

↓

Stack Unwinding

↓

Find Matching catch

↓

Execute catch

↓

Execute finally

↓

Continue
```

---

# 15. Best Practices

✔ Catch only exceptions you can handle.

✔ Catch specific exception types whenever possible.

✔ Use finally only for cleanup.

✔ Prefer `using` or `await using` for disposable resources.

✔ Log exceptions before rethrowing or returning an error response.

✔ Let unexpected exceptions bubble up to the Global Exception Middleware in ASP.NET Core.

---

# 16. Common Mistakes

## Empty Catch Block

Bad

```csharp
catch
{
}
```

This hides the actual problem and makes debugging difficult.

---

## Catching Exception Everywhere

Bad

```csharp
catch(Exception)
{
}
```

Use specific exception types unless you are implementing global exception handling.

---

## Business Logic in finally

Bad

```csharp
finally
{
    SaveCustomer();
}
```

The finally block should only contain cleanup code.

---

## Swallowing Exceptions

Never silently ignore exceptions.

Always log or rethrow them when appropriate.

---

# 17. Interview Questions

## Q1. What is the purpose of a try block?

The try block contains code that may throw an exception.

---

## Q2. When does a catch block execute?

Only when a matching exception is thrown from the associated try block.

---

## Q3. Does finally always execute?

In normal execution, yes. It runs whether an exception occurs or not, making it ideal for cleanup. In rare situations such as abrupt process termination, it may not execute.

---

## Q4. Can we have try without catch?

Yes.

```csharp
try
{
}
finally
{
}
```

This is commonly used when cleanup is required but the exception should continue propagating.

---

## Q5. Why should specific catch blocks come before catch(Exception)?

The CLR selects the first compatible catch block.

If `catch(Exception)` comes first, all later specific catch blocks become unreachable.

---

## Q6. Why is finally used?

To release resources such as:

- Files
- Database Connections
- Network Streams
- Locks
- Unmanaged Resources

---

# 18. Summary

In this module you learned:

- ✅ Why exception handling is needed
- ✅ try block
- ✅ catch block
- ✅ finally block
- ✅ Exception object
- ✅ Multiple catch blocks
- ✅ Catch block ordering
- ✅ try-finally
- ✅ Nested try-catch
- ✅ Exception filters
- ✅ Internal CLR working
- ✅ Best practices
- ✅ Common interview questions

---

# Architecture Diagram

```
                Application Flow

               try Block
                   │
                   ▼
          Execute Statements
                   │
         ┌─────────┴─────────┐
         │                   │
         ▼                   ▼
 No Exception         Exception Occurs
         │                   │
         ▼                   ▼
     Skip catch      Create Exception Object
         │                   │
         │             Stack Unwinding
         │                   │
         │            Matching catch Found
         │                   │
         ▼                   ▼
             Execute catch
                   │
                   ▼
             Execute finally
                   │
                   ▼
          Continue Application
```

---

# Next Module

## Module 35.3 – throw, throw ex & Rethrowing Exceptions

Topics:

- `throw`
- `throw ex`
- `throw new Exception()`
- Stack Trace Preservation
- Exception Wrapping (`InnerException`)
- Rethrowing Best Practices
- CLR Internals
- Real-world Examples
- Senior-Level Interview Questions

> **Goal:** Understand how exceptions are propagated and why `throw;` is preferred over `throw ex;` in production code.