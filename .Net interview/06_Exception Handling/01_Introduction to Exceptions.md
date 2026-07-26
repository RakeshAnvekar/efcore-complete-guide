# Module 35.1 – Introduction to Exceptions in ASP.NET Core

> **Course:** ASP.NET Core – Complete Interview Roadmap
>
> **Module:** 35.1 – Introduction to Exceptions
>
> **Level:** Beginner → Advanced
>
> **Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is an Exception?
2. Exception vs Error
3. Types of Errors
4. Why Exceptions Occur
5. Real-World Analogy
6. Exception Hierarchy
7. How the CLR Handles Exceptions
8. Stack Unwinding
9. Cost of Exceptions
10. Checked vs Unchecked Exceptions
11. Exception Flow in ASP.NET Core
12. Best Practices
13. Common Mistakes
14. Interview Questions
15. Summary

---

# 1. What is an Exception?

An **Exception** is an unexpected event that occurs while a program is executing and interrupts the normal flow of the application.

It represents a runtime problem that the application cannot continue processing without intervention.

Examples:

- Dividing a number by zero
- Accessing a null object
- Database connection failure
- File not found
- Network timeout

Example:

```csharp
int number = 10;

int result = number / 0;
```

Output

```
System.DivideByZeroException
```

The application compiles successfully, but during execution the CLR throws a `DivideByZeroException`.

---

# 2. Exception vs Error

Although people often use these terms interchangeably, they are different.

| Error | Exception |
|--------|-----------|
| General term for a problem | Runtime problem represented by an object |
| May occur during compilation or execution | Occurs only while the program is running |
| May prevent compilation | Happens after successful compilation |
| Not always recoverable | Can often be handled using try-catch |

---

# 3. Types of Errors

## 3.1 Syntax Error

The compiler cannot understand the code.

```csharp
int number =
```

Result

```
Compilation Failed
```

---

## 3.2 Compile-Time Error

The syntax is correct, but the code violates C# language rules.

```csharp
string name = 100;
```

Compiler Error

```
Cannot implicitly convert type 'int' to 'string'
```

---

## 3.3 Runtime Error (Exception)

The application starts successfully but fails while executing.

```csharp
string text = null;

Console.WriteLine(text.Length);
```

Output

```
System.NullReferenceException
```

---

## 3.4 Logical Error

The application executes successfully but produces incorrect results.

```csharp
int total = price - quantity;
```

The intended calculation might have been:

```csharp
int total = price * quantity;
```

The compiler cannot detect logical mistakes.

---

# 4. Why Do Exceptions Occur?

Exceptions occur because of unexpected situations during execution.

Examples include:

- Divide by zero
- Null reference
- Invalid user input
- Database server unavailable
- File missing
- Network timeout
- Invalid index
- Access denied

These situations cannot always be predicted at compile time.

---

# 5. Real-World Analogy

Imagine an ATM.

Customer requests £100.

```
Customer

↓

ATM

↓

Check Balance

↓

Enough Balance?

      Yes
       ↓
 Dispense Money

      No
       ↓
 Show Error Message
```

Instead of crashing, the ATM detects the problem and informs the customer.

Applications behave similarly using exceptions.

---

# 6. Exception Hierarchy

Every exception in .NET inherits from the `System.Exception` class.

```
System.Object
        │
        ▼
System.Exception
        │
        ├── SystemException
        │      ├── NullReferenceException
        │      ├── DivideByZeroException
        │      ├── InvalidOperationException
        │      ├── FormatException
        │      ├── IndexOutOfRangeException
        │      └── OverflowException
        │
        └── ApplicationException
```

Because all exceptions inherit from `Exception`, a catch block for `Exception` can handle any exception.

---

# 7. How the CLR Handles Exceptions

Suppose we have the following methods.

```csharp
public void A()
{
    B();
}

public void B()
{
    C();
}

public void C()
{
    throw new Exception("Something went wrong.");
}
```

Execution Flow

```
Main()

↓

A()

↓

B()

↓

C()

↓

Exception Thrown

↓

CLR Searches for Catch Block

↓

B()

↓

A()

↓

Main()

↓

Unhandled Exception
```

When an exception occurs, execution stops immediately in the current method.

The CLR begins searching for an appropriate catch block.

---

# 8. Stack Unwinding

The CLR performs **Stack Unwinding** whenever an exception is thrown.

Process:

1. Current method stops execution.
2. CLR looks for a matching catch block.
3. If none exists, the current method exits.
4. Control returns to the calling method.
5. The CLR repeats the search.
6. If no handler exists anywhere, the application terminates (or ASP.NET Core global exception middleware handles it).

Visual Representation

```
Main()

↓

Controller()

↓

Service()

↓

Repository()

↓

SQL Query

↓

Exception

↑

Repository exits

↑

Service exits

↑

Controller exits

↑

Main exits
```

This backward movement through the call stack is called **Stack Unwinding**.

---

# 9. Cost of Exceptions

Exceptions are expensive because the CLR must:

- Create an exception object
- Capture the stack trace
- Perform stack unwinding
- Search for a matching catch block
- Transfer execution

For this reason, exceptions should never be used for normal application logic.

Bad Example

```csharp
try
{
    int number = int.Parse(input);
}
catch
{
    number = 0;
}
```

Better Approach

```csharp
if (int.TryParse(input, out int number))
{
    Console.WriteLine(number);
}
else
{
    Console.WriteLine("Invalid number.");
}
```

`TryParse()` avoids throwing exceptions for expected invalid input.

---

# 10. Checked vs Unchecked Exceptions

Some programming languages (such as Java) distinguish between:

- Checked Exceptions
- Unchecked Exceptions

C# does **not** support checked exceptions.

The compiler never forces you to catch or declare exceptions.

Handling exceptions is entirely optional.

---

# 11. Exception Flow in ASP.NET Core

```
Client

↓

Kestrel

↓

Middleware Pipeline

↓

Authentication Middleware

↓

Authorization Middleware

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

Exception Occurs

↓

CLR Stack Unwinding

↓

Global Exception Middleware

↓

ILogger

↓

JSON Error Response

↓

Client
```

Modern ASP.NET Core applications typically use **Global Exception Handling Middleware** instead of wrapping every controller action with try-catch blocks.

---

# 12. Best Practices

✔ Throw exceptions only for exceptional situations.

✔ Catch only exceptions you can handle.

✔ Use specific exception types whenever possible.

✔ Preserve the original stack trace by using:

```csharp
throw;
```

instead of:

```csharp
throw ex;
```

✔ Log exceptions before returning a response.

✔ Never expose sensitive information such as connection strings, passwords, or stack traces in production API responses.

---

# 13. Common Mistakes

## Swallowing Exceptions

Bad Practice

```csharp
try
{
}
catch
{
}
```

The exception disappears, making debugging very difficult.

---

## Using Exceptions for Validation

Bad

```csharp
try
{
    int age = int.Parse(input);
}
catch
{
}
```

Good

```csharp
if (int.TryParse(input, out int age))
{
    // Process value
}
```

---

## Returning Raw Exception Messages

Never expose internal details to API consumers.

Bad

```csharp
return ex.ToString();
```

Instead, return a safe error response.

---

## Catching Exception Everywhere

Avoid wrapping every method with try-catch.

Instead, allow exceptions to bubble up to the Global Exception Middleware.

---

# 14. Interview Questions

## Q1. What is an Exception?

An Exception is a runtime event that interrupts the normal flow of program execution due to an unexpected condition.

---

## Q2. What is the difference between an Error and an Exception?

Errors are general software problems.

Exceptions are runtime problems represented by objects derived from `System.Exception`.

---

## Q3. What is Stack Unwinding?

Stack Unwinding is the process where the CLR moves back through the call stack looking for a matching catch block.

---

## Q4. Why are Exceptions Expensive?

Because the CLR:

- Creates an exception object
- Captures the stack trace
- Unwinds the stack
- Searches for a handler

---

## Q5. Does C# Support Checked Exceptions?

No.

Unlike Java, C# does not require developers to catch or declare exceptions.

---

## Q6. Should Exceptions Be Used for Validation?

No.

Expected scenarios such as invalid user input should be handled using validation methods like `TryParse()` instead of exceptions.

---

## Q7. Why Should You Avoid Catching `Exception` Everywhere?

It hides the real problem, increases code duplication, and makes maintenance difficult.

Global Exception Handling Middleware is the recommended approach in ASP.NET Core.

---

# Key Takeaways

- Exceptions are runtime events that interrupt normal program execution.
- Every exception derives from `System.Exception`.
- The CLR performs stack unwinding to locate a matching catch block.
- Exceptions should only be used for exceptional situations.
- Expected scenarios should be handled using validation instead of exceptions.
- ASP.NET Core applications should rely on Global Exception Handling Middleware instead of repetitive try-catch blocks.

---

# Architecture Diagram

```
                Application Execution

Client Request
      │
      ▼
Middleware Pipeline
      │
      ▼
Controller
      │
      ▼
Service
      │
      ▼
Repository
      │
      ▼
Database

      ❌ Exception Occurs

      │
      ▼

CLR Stack Unwinding

      │
      ▼

Global Exception Middleware

      │
      ▼

ILogger

      │
      ▼

Standard Error Response (JSON)

      │
      ▼

Client
```

---

# Next Module

## Module 35.2 – try, catch, finally

Topics Covered:

- try Block
- catch Block
- Multiple catch Blocks
- finally Block
- Execution Flow
- Nested try-catch
- Exception Filters (`when`)
- Real-world Examples
- CLR Execution Flow
- Interview Questions

> **Goal:** Understand how C# catches, handles, and cleans up after exceptions before moving on to Global Exception Handling Middleware in ASP.NET Core.