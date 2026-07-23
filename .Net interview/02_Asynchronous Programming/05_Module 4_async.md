# Module 4 -- async Keyword

# 4.1 What `async` Really Does

> Interview Level: ⭐⭐⭐⭐⭐

## Learning Objectives

-   What is `async`?
-   Why was it introduced?
-   Does `async` create a thread?
-   What happens internally?
-   Why can't `await` be used without `async`?

------------------------------------------------------------------------

# What is `async`?

The `async` keyword **does not create a new thread**.

Its real purpose is to tell the **C# compiler** to transform the method
into an **asynchronous state machine** so it can pause at an `await`
statement and later resume execution.

> **Simple Definition**
>
> `async` is a compiler feature that enables the use of the `await`
> keyword.

------------------------------------------------------------------------

# Why Was `async` Introduced?

Before C# 5.0, asynchronous code relied on callbacks and continuations.

``` csharp
Task task = DownloadFile();

task.ContinueWith(t =>
{
    Console.WriteLine("Download Completed");
});
```

This style was difficult to read and maintain.

`async` and `await` allow asynchronous code to be written in a clean,
sequential style.

------------------------------------------------------------------------

# Example

Without `async`

``` csharp
public Task Download()
{
    return Task.Delay(3000);
}
```

With `async`

``` csharp
public async Task Download()
{
    await Task.Delay(3000);
}
```

------------------------------------------------------------------------

# Does `async` Create a New Thread?

**No.**

`async` does **not**:

-   Create a thread
-   Start background work
-   Run code in parallel

It only enables compiler-generated pause and resume logic.

------------------------------------------------------------------------

# Real-Life Analogy

## Synchronous

``` text
Cook rice
↓
Wait beside the stove
↓
Rice ready
```

## Asynchronous

``` text
Cook rice
↓
Set timer
↓
Do other work
↓
Timer rings
↓
Return to kitchen
```

No extra person watches the rice.

Similarly, `async` does not create another thread.

------------------------------------------------------------------------

# What Does `async` Actually Do?

The compiler prepares the method to:

1.  Execute normally.
2.  Pause at `await`.
3.  Save its current state.
4.  Return a `Task`.
5.  Resume later.

------------------------------------------------------------------------

# Internal Flow

``` text
Call async method
      │
      ▼
Execute code
      │
      ▼
Reach await
      │
      ▼
Save state
      │
      ▼
Return Task
      │
      ▼
Awaited operation completes
      │
      ▼
Resume execution
      │
      ▼
Finish
```

------------------------------------------------------------------------

# What If We Remove `async`?

Valid:

``` csharp
public async Task Test()
{
    await Task.Delay(1000);
}
```

Invalid:

``` csharp
public Task Test()
{
    await Task.Delay(1000);
}
```

Compiler Error:

    The 'await' operator can only be used within an async method.

------------------------------------------------------------------------

# async Without await

``` csharp
public async Task Test()
{
    Console.WriteLine("Hello");
}
```

This compiles but produces a warning because there is no `await`.

------------------------------------------------------------------------

# Does `async` Improve Performance?

No.

It does **not** make the database or network faster.

It allows the current thread to do other work while waiting for I/O to
complete, improving scalability.

------------------------------------------------------------------------

# ASP.NET Core Example

``` csharp
public async Task<IActionResult> GetOrders()
{
    var orders = await _repository.GetOrdersAsync();
    return Ok(orders);
}
```

Execution:

``` text
Request
↓
Controller
↓
Database call
↓
await
↓
Thread released
↓
Database finishes
↓
Method resumes
↓
Response
```

------------------------------------------------------------------------

# Common Misconceptions

-   ❌ `async` creates a new thread.
-   ❌ `async` automatically makes code faster.
-   ❌ `async` runs code in parallel.
-   ❌ Every Task-returning method needs `async`.

------------------------------------------------------------------------

# Interview Questions

## What does `async` do?

It tells the compiler to generate a state machine so the method can
pause and resume around `await`.

## Does `async` create a new thread?

No.

## Can a method return `Task` without `async`?

Yes.

``` csharp
public Task Delay()
{
    return Task.Delay(1000);
}
```

## Why can't `await` be used without `async`?

Because the compiler only generates the required pause/resume
infrastructure for methods marked `async`.

------------------------------------------------------------------------

# Quick Revision

-   `async` is a compiler feature.
-   It enables `await`.
-   It does not create threads.
-   It does not directly improve performance.
-   It prepares the method to pause and resume.

------------------------------------------------------------------------

# Key Takeaways

-   `async` is not a threading keyword.
-   It works together with `await`.
-   The compiler converts async methods into a state machine.
-   Understanding this concept is essential for senior .NET interviews.

# Module 4 -- async Keyword

# Part 4.1 -- What `async` Really Does

> Interview Level: ⭐⭐⭐⭐⭐

## Learning Objectives

-   Why Microsoft introduced `async`
-   What `async` really does
-   Does `async` create a new thread?
-   Does `async` improve performance?
-   How `async` works with `await`

------------------------------------------------------------------------

# Why Was `async` Introduced?

Before `async/await`, asynchronous programming relied on callbacks and
continuations, making code difficult to read.

The goal of `async` is to avoid blocking threads while waiting for I/O
operations like database calls, HTTP requests, or file access.

------------------------------------------------------------------------

# The Problem

``` csharp
public void DownloadFile()
{
    Thread.Sleep(5000);
}
```

`Thread.Sleep()` blocks the current thread.

In ASP.NET Core, many blocked threads can lead to **Thread Pool
Starvation**.

------------------------------------------------------------------------

# What is `async`?

`async` **does not create a new thread**.

Instead, it tells the **C# compiler** to transform the method into a
**state machine** so it can pause at `await` and resume later.

> **Definition**
>
> `async` is a compiler feature that enables the use of `await`.

------------------------------------------------------------------------

# Does `async` Create a New Thread?

**No.**

``` csharp
public async Task Test()
{
    await Task.Delay(3000);
}
```

-   `async` creates no thread.
-   `await` creates no thread.
-   The awaited operation determines how the work is performed.

------------------------------------------------------------------------

# What Does `async` Actually Do?

When the compiler encounters:

``` csharp
public async Task SaveData()
{
    Console.WriteLine("Start");
    await Task.Delay(5000);
    Console.WriteLine("End");
}
```

It prepares the method to:

1.  Execute normally.
2.  Pause at `await`.
3.  Save its state.
4.  Return a `Task`.
5.  Resume later.

------------------------------------------------------------------------

# Internal Flow

``` text
Call async method
        │
        ▼
Execute code
        │
        ▼
Reach await
        │
        ▼
Save current state
        │
        ▼
Return Task
        │
        ▼
Awaited operation completes
        │
        ▼
Resume execution
        │
        ▼
Method completes
```

------------------------------------------------------------------------

# What If We Remove `async`?

Valid:

``` csharp
public async Task Download()
{
    await Task.Delay(1000);
}
```

Invalid:

``` csharp
public Task Download()
{
    await Task.Delay(1000);
}
```

Compiler Error:

    The 'await' operator can only be used within an async method.

------------------------------------------------------------------------

# Can We Return Task Without async?

Yes.

``` csharp
public Task Download()
{
    return Task.Delay(1000);
}
```

------------------------------------------------------------------------

# Does `async` Improve Performance?

No.

It does not make the database or network faster.

It improves **scalability** by releasing the current thread while
waiting for I/O operations.

------------------------------------------------------------------------

# ASP.NET Core Example

``` csharp
public async Task<IActionResult> GetOrders()
{
    var orders = await _repository.GetOrdersAsync();
    return Ok(orders);
}
```

Execution Flow:

    Request
       ↓
    Controller
       ↓
    Database Call
       ↓
    await
       ↓
    Thread Released
       ↓
    Database Completes
       ↓
    Resume Method
       ↓
    Return Response

------------------------------------------------------------------------

# Common Misconceptions

-   `async` creates a thread ❌
-   `async` makes code faster ❌
-   `async` runs code in parallel ❌

------------------------------------------------------------------------

# Interview Questions

## What does `async` do?

It tells the compiler to generate a state machine so the method can
pause and resume around `await`.

## Does `async` create a new thread?

No.

## Can a method return `Task` without `async`?

Yes.

## Does `async` improve performance?

No. It improves scalability.

## Why can't `await` be used without `async`?

Because the compiler only generates the required pause/resume
infrastructure for methods marked `async`.

------------------------------------------------------------------------

# Quick Revision

-   `async` is a compiler feature.
-   `async` enables `await`.
-   `async` does not create threads.
-   `async` does not directly improve performance.
-   `async` prepares a method to pause and resume.

------------------------------------------------------------------------

# Key Takeaways

-   `async` is not a threading keyword.
-   It is a compiler keyword.
-   It works together with `await`.
-   The compiler transforms async methods into a state machine.
-   This is one of the most important concepts for senior .NET
    interviews.

