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

# Module 4.3 – async Methods

> **.NET Asynchronous Programming Roadmap**

**Module:** 4.3 – async Methods

**Difficulty:** ⭐⭐⭐☆☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Introduction
2. What is an async Method?
3. Why Do We Need async Methods?
4. Synchronous vs Asynchronous Execution
5. Syntax of async Methods
6. How async Methods Work
7. Execution Flow
8. Code Example
9. Dry Run
10. Visualization
11. Does async Create a New Thread?
12. Thread Behavior
13. Return Types
14. Rules of async Methods
15. Real World Example
16. Common Mistakes
17. Best Practices
18. Interview Questions
19. Interview Answers
20. Key Takeaways

---

# Introduction

Modern applications constantly communicate with external resources such as:

- Databases
- Web APIs
- File Systems
- Cloud Services

These operations take time.

If a thread waits for these operations to finish, it becomes blocked and cannot perform any other work.

To solve this problem, C# introduced **Asynchronous Programming** using the **async** and **await** keywords.

---

# What is an async Method?

An **async method** is a method marked with the **async** keyword.

It allows the use of the **await** keyword inside the method.

Example

```csharp
public async Task DownloadFileAsync()
{
    await Task.Delay(2000);
}
```

The **async** keyword tells the compiler:

> "This method contains asynchronous operations. Convert it into an asynchronous state machine."

---

# Why Do We Need async Methods?

Suppose a database query takes **5 seconds**.

Without async

```
Start Query

↓

Thread Waits

↓

5 Seconds

↓

Continue
```

The thread remains blocked for the entire duration.

---

Using async

```
Start Query

↓

Database Processing

↓

Thread Becomes Free

↓

Do Other Work

↓

Database Completes

↓

Continue Execution
```

The thread is not wasted while waiting.

---

# Synchronous vs Asynchronous Execution

## Synchronous

```
Start

↓

Task 1

↓

Wait

↓

Task 2

↓

Wait

↓

Task 3

↓

Finish
```

Every operation waits for the previous one.

---

## Asynchronous

```
Start

↓

Task Starts

↓

Thread Released

↓

Other Work Executes

↓

Task Completes

↓

Continue
```

Waiting time is utilized efficiently.

---

# Syntax of async Methods

General Syntax

```csharp
public async Task MethodName()
{
    await SomeAsyncMethod();
}
```

Returning a Value

```csharp
public async Task<int> GetAgeAsync()
{
    await Task.Delay(1000);

    return 25;
}
```

---

# How async Methods Work

When the compiler sees

```csharp
async
```

it prepares the method for asynchronous execution.

When it encounters

```csharp
await
```

the method pauses without blocking the current thread.

After the awaited operation completes, execution resumes from the next statement.

---

# Execution Flow

```
Method Starts

↓

Execute Code

↓

await Found

↓

Pause Method

↓

Return Thread

↓

Operation Completes

↓

Resume Method

↓

Method Ends
```

---

# Example Program

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        Console.WriteLine("Before");

        await PrintMessageAsync();

        Console.WriteLine("After");
    }

    static async Task PrintMessageAsync()
    {
        Console.WriteLine("Inside Method");

        await Task.Delay(3000);

        Console.WriteLine("Completed");
    }
}
```

---

# Output

```
Before

Inside Method

(Wait 3 Seconds)

Completed

After
```

---

# Dry Run

Execution begins

```
Main()
```

↓

Print

```
Before
```

↓

Call

```csharp
await PrintMessageAsync();
```

↓

Print

```
Inside Method
```

↓

Encounter

```csharp
await Task.Delay(3000);
```

↓

Method pauses

↓

Current thread becomes available

↓

Timer completes

↓

Execution resumes

↓

Print

```
Completed
```

↓

Return to Main

↓

Print

```
After
```

---

# Visualization

```
Main()

↓

Before

↓

PrintMessageAsync()

↓

Inside Method

↓

await Task.Delay()

↓

Method Suspended

↓

Thread Released

↓

3 Seconds Later

↓

Method Continues

↓

Completed

↓

Return

↓

After
```

---

# Does async Create a New Thread?

One of the biggest misconceptions is:

```
async

↓

Creates New Thread
```

❌ Incorrect

The **async** keyword **does not create a new thread**.

Instead, it allows the compiler to pause execution at **await** points and resume later.

---

# Thread Behavior

Suppose

```csharp
await Task.Delay(5000);
```

Execution

```
Thread Starts

↓

Starts Timer

↓

Leaves Method

↓

Thread Returns to Thread Pool

↓

Timer Completes

↓

Continuation Scheduled

↓

Method Continues
```

The original thread is not blocked.

---

# Return Types of async Methods

## async Task

No value is returned.

```csharp
public async Task SaveAsync()
{
}
```

---

## async Task<T>

Returns a value.

```csharp
public async Task<int> GetCountAsync()
{
    return 10;
}
```

---

## async void

Reserved only for event handlers.

```csharp
public async void Button_Click(object sender, EventArgs e)
{
}
```

Avoid using **async void** in normal application code.

---

# Rules of async Methods

## Rule 1

A method using **await** must also use **async**.

Correct

```csharp
public async Task Demo()
{
    await Task.Delay(1000);
}
```

Incorrect

```csharp
public Task Demo()
{
    await Task.Delay(1000);
}
```

Compiler Error

---

## Rule 2

Prefer returning

```
Task
```

instead of

```
void
```

---

## Rule 3

Follow Microsoft's naming convention.

Examples

```
ReadAsync()

SaveAsync()

DeleteAsync()

DownloadAsync()
```

---

# Real World ASP.NET Core Example

```csharp
[HttpGet]
public async Task<IActionResult> GetUsers()
{
    var users = await _repository.GetUsersAsync();

    return Ok(users);
}
```

Execution

```
HTTP Request

↓

Controller

↓

Repository

↓

Database

↓

Thread Released

↓

Database Responds

↓

Controller Continues

↓

Return Response
```

This allows ASP.NET Core to process other incoming requests while waiting for the database.

---

# Common Mistakes

## Mistake 1

Forgetting async

```csharp
public Task Demo()
{
    await Task.Delay(1000);
}
```

Compiler Error

---

## Mistake 2

Using Thread.Sleep()

```csharp
Thread.Sleep(5000);
```

Blocks the thread.

Instead use

```csharp
await Task.Delay(5000);
```

---

## Mistake 3

Returning async void

```csharp
public async void Save()
```

Prefer

```csharp
public async Task Save()
```

---

## Mistake 4

Thinking async creates a new thread.

It does not.

---

# Best Practices

✔ Use **async** only when the method contains **await**.

✔ Return **Task** or **Task<T>**.

✔ Follow the **Async** naming convention.

✔ Use asynchronous APIs whenever available.

✔ Avoid **async void** except for event handlers.

✔ Avoid **Thread.Sleep()** inside async methods.

---

# Interview Questions

### Question 1

What is an async method?

---

### Question 2

Why do we use async methods?

---

### Question 3

Does async create a new thread?

---

### Question 4

What happens when await is encountered?

---

### Question 5

Why should async methods return Task instead of void?

---

### Question 6

Can we use await without async?

---

### Question 7

What are the valid return types of async methods?

---

# Interview Answers

### What is an async method?

An async method is a method marked with the **async** keyword that enables the use of **await**. It allows long-running operations to execute without blocking the calling thread.

---

### Why use async?

To improve application responsiveness and scalability by avoiding blocked threads during I/O operations.

---

### Does async create a thread?

No.

The async keyword only enables asynchronous execution. It does not create a new thread.

---

### What happens at await?

The method pauses execution, returns control to the caller, and resumes automatically when the awaited operation completes.

---

### Why return Task?

Task allows callers to await completion, observe exceptions, and compose asynchronous operations.

---

### Can await exist without async?

No.

The compiler generates an error if await is used in a non-async method.

---

### Valid return types?

- Task
- Task<T>
- void (only for event handlers)

---

# Key Takeaways

- **async** enables asynchronous programming.
- **await** pauses a method without blocking the thread.
- **async** does **not** create a new thread.
- Use **Task** or **Task<T>** as return types.
- Avoid **async void** except for UI event handlers.
- Follow the **Async** naming convention.
- Async methods improve scalability and responsiveness in .NET applications.

---

# Next Module

➡ **Module 4.4 – async without await**

Topics Covered:

- Compiler Warning CS1998
- Why async without await is allowed
- When it is useful
- When it is a bad practice
- Interview Scenarios
- Best Practices

# Module 4.4 – async without await

> **.NET Asynchronous Programming Roadmap**

**Module:** 4.4 – async without await

**Difficulty:** ⭐⭐☆☆☆

**Interview Importance:** ⭐⭐⭐⭐☆

---

# Table of Contents

1. Introduction
2. What is async without await?
3. Why is it Allowed?
4. Compiler Warning CS1998
5. How the Compiler Treats It
6. Execution Flow
7. Examples
8. When Should We Use It?
9. When Should We Avoid It?
10. async vs async without await
11. Real World Examples
12. Common Mistakes
13. Best Practices
14. Interview Questions
15. Interview Answers
16. Key Takeaways

---

# Introduction

In the previous module, we learned that:

```csharp
async
```

and

```csharp
await
```

are usually used together.

However, C# allows us to write an **async method without using await**.

Many beginners think this is an error.

It is **not an error**.

It is only a **compiler warning**.

---

# What is async without await?

Consider this method.

```csharp
public async Task SayHelloAsync()
{
    Console.WriteLine("Hello");
}
```

Notice something?

There is

```
No await
```

inside the method.

Yet the method compiles successfully.

---

# Output

```
Hello
```

The method works.

But the compiler shows a warning.

---

# Compiler Warning (CS1998)

The compiler displays

```
CS1998

This async method lacks 'await' operators and will run synchronously.
Consider using the 'await' operator to await non-blocking API calls,
or remove the 'async' modifier.
```

This is **not an error**.

The program still runs.

---

# Why Does the Compiler Show This Warning?

Suppose we write

```csharp
public async Task DisplayAsync()
{
    Console.WriteLine("Welcome");
}
```

There is no asynchronous operation.

Everything executes immediately.

So the compiler says

```
Why is this method marked async?

There is nothing to await.
```

---

# How Does the Method Execute?

Example

```csharp
public async Task PrintAsync()
{
    Console.WriteLine("Hello");
}
```

Execution

```
Method Starts

↓

Print Hello

↓

Method Ends
```

No waiting.

No suspension.

No asynchronous behavior.

---

# Does async Make It Asynchronous?

Many beginners think

```
async

↓

Method becomes asynchronous
```

❌ Wrong

The **async** keyword alone does not make a method asynchronous.

Only an **await** on an asynchronous operation causes the method to pause and resume later.

---

# Example 1

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        await PrintAsync();
    }

    static async Task PrintAsync()
    {
        Console.WriteLine("Hello");
    }
}
```

Output

```
Hello
```

Compiler Warning

```
CS1998
```

---

# Example 2

Now add await.

```csharp
static async Task PrintAsync()
{
    Console.WriteLine("Before");

    await Task.Delay(2000);

    Console.WriteLine("After");
}
```

Execution

```
Before

↓

Pause

↓

2 Seconds

↓

After
```

Now the warning disappears because the method actually performs asynchronous work.

---

# Dry Run

Example

```csharp
public async Task Demo()
{
    Console.WriteLine("A");
}
```

Execution

```
Call Demo()

↓

Print A

↓

Return Completed Task
```

Nothing is suspended.

No thread is released.

---

# Visualization

## Without await

```
Method Starts

↓

Execute Statement

↓

Return
```

---

## With await

```
Method Starts

↓

Execute Statement

↓

await

↓

Pause

↓

Thread Released

↓

Operation Completes

↓

Resume

↓

Return
```

---

# Why Does It Return Task?

Consider

```csharp
public async Task Demo()
{
    Console.WriteLine("Hello");
}
```

Even without await,

the compiler still creates a **Task**.

The returned task is already completed.

Equivalent to

```csharp
public Task Demo()
{
    Console.WriteLine("Hello");

    return Task.CompletedTask;
}
```

This version is actually better because it avoids the unnecessary async state machine.

---

# Should We Write async without await?

Usually

```
NO
```

Because

- Extra compiler-generated state machine
- Compiler warning
- Unnecessary overhead

Instead write

```csharp
public Task Demo()
{
    Console.WriteLine("Hello");

    return Task.CompletedTask;
}
```

---

# When is async without await Useful?

Very rarely.

One example:

Suppose today

```csharp
public async Task SaveAsync()
{
    Console.WriteLine("Saving...");
}
```

Tomorrow

```
Save to Database

↓

await database.SaveAsync();
```

Sometimes developers temporarily keep **async** because they know asynchronous code will be added soon.

However, this is generally discouraged in production code because it leaves unnecessary compiler warnings.

---

# Better Alternative

Instead of

```csharp
public async Task LogAsync()
{
    Console.WriteLine("Logged");
}
```

Use

```csharp
public Task LogAsync()
{
    Console.WriteLine("Logged");

    return Task.CompletedTask;
}
```

No warning.

No unnecessary state machine.

---

# async vs async without await

| async with await | async without await |
|------------------|---------------------|
| Truly asynchronous | Runs synchronously |
| Method pauses | Method never pauses |
| Thread can be released | Thread never released |
| No compiler warning | CS1998 warning |
| Recommended | Usually avoid |

---

# Real World ASP.NET Core Example

Suppose

```csharp
public async Task<IActionResult> Get()
{
    return Ok();
}
```

No await.

Compiler warning.

Better

```csharp
public IActionResult Get()
{
    return Ok();
}
```

Or if the interface requires a `Task`:

```csharp
public Task<IActionResult> Get()
{
    return Task.FromResult<IActionResult>(Ok());
}
```

---

# Common Mistakes

## Mistake 1

Writing

```csharp
public async Task SaveAsync()
{
}
```

without await.

---

## Mistake 2

Thinking async creates asynchronous execution.

It does not.

---

## Mistake 3

Ignoring compiler warning CS1998.

Warnings should be understood and addressed, not ignored.

---

## Mistake 4

Returning Task but still using async unnecessarily.

---

# Best Practices

✔ Remove async if there is no await.

✔ Return `Task.CompletedTask` when no asynchronous work is required.

✔ Return `Task.FromResult(value)` for synchronous methods that need to return a value.

✔ Add async only when genuine asynchronous work exists.

✔ Do not ignore CS1998 warnings.

---

# Interview Questions

### Question 1

Can an async method exist without await?

---

### Question 2

Will it compile?

---

### Question 3

What warning is generated?

---

### Question 4

Does async alone create asynchronous execution?

---

### Question 5

What should be returned instead of using async without await?

---

### Question 6

Which is better?

```csharp
async Task Demo()
```

or

```csharp
Task Demo()
```

---

# Interview Answers

### Can async exist without await?

Yes.

It compiles successfully.

However, the compiler generates warning **CS1998** because the method runs synchronously.

---

### Does async make a method asynchronous?

No.

Only an awaited asynchronous operation introduces asynchronous behaviour.

---

### What warning is generated?

```
CS1998

This async method lacks 'await' operators and will run synchronously.
```

---

### What should we do?

If there is no asynchronous work,

remove **async** and return

```csharp
Task.CompletedTask
```

or

```csharp
Task.FromResult(value)
```

depending on whether a value is returned.

---

### Which version is preferred?

Preferred

```csharp
public Task SaveAsync()
{
    Console.WriteLine("Saving");

    return Task.CompletedTask;
}
```

Avoid

```csharp
public async Task SaveAsync()
{
    Console.WriteLine("Saving");
}
```

because it generates warning **CS1998** and creates unnecessary compiler overhead.

---

# Key Takeaways

- An **async** method can compile without **await**.
- The compiler generates **CS1998** warning.
- Without **await**, the method executes synchronously.
- `async` alone does **not** create asynchronous behaviour.
- If no asynchronous work exists, prefer `Task.CompletedTask` or `Task.FromResult()` instead of marking the method `async`.
- Use `async` only when the method actually performs asynchronous operations.

---

# Next Module

➡ **Module 4.5 – async Task**

Topics Covered:

- What is `Task`?
- Why `Task` is the preferred return type
- How exceptions are propagated
- Awaiting a `Task`
- Fire-and-forget vs awaited tasks
- Real ASP.NET Core examples

# Module 4.5 – async Task

> **.NET Asynchronous Programming Roadmap**

**Module:** 4.5 – async Task

**Difficulty:** ⭐⭐⭐☆☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Introduction
2. What is Task?
3. Why Do We Need Task?
4. Why async Methods Return Task
5. Task Life Cycle
6. How async Task Works
7. Execution Flow
8. Example 1 – Simple async Task
9. Example 2 – Multiple Tasks
10. await vs Calling Directly
11. Task vs Thread
12. Task vs Task<T>
13. Exception Handling
14. Real World ASP.NET Core Example
15. Common Mistakes
16. Best Practices
17. Interview Questions
18. Interview Answers
19. Key Takeaways

---

# Introduction

Before C# 5.0, developers used:

- Thread
- ThreadPool
- BackgroundWorker
- Begin/End Pattern

These approaches made asynchronous programming difficult.

Microsoft introduced **Task** to simplify asynchronous programming.

Today, almost every asynchronous API in .NET returns a **Task**.

Examples

```csharp
await Task.Delay();

await File.ReadAllTextAsync();

await HttpClient.GetAsync();

await dbContext.Users.ToListAsync();
```

All these methods return a **Task**.

---

# What is Task?

A **Task** represents an operation that will complete **now or sometime in the future**.

Think of it as a promise.

```
Start Work

↓

Task Created

↓

Work Running

↓

Task Completes

↓

Caller Continues
```

Task itself is **not the work**.

Task only represents that work.

---

# Real Life Example

Imagine ordering food online.

```
Place Order

↓

Receive Token

↓

Wait

↓

Food Ready

↓

Collect Food
```

The token is like a **Task**.

It represents the work being done.

---

# Why Do We Need Task?

Suppose downloading a file takes 5 seconds.

Without Task

```
Download Starts

↓

Thread Waits

↓

5 Seconds

↓

Continue
```

Thread is blocked.

---

Using Task

```
Download Starts

↓

Task Returned

↓

Thread Free

↓

Download Completes

↓

Continue
```

No thread is wasted.

---

# Why async Methods Return Task

Example

```csharp
public async Task SaveAsync()
{
    await Task.Delay(2000);

    Console.WriteLine("Saved");
}
```

The caller can do

```csharp
await SaveAsync();
```

The caller waits asynchronously until the operation completes.

---

# Task Life Cycle

A Task usually goes through these stages.

```
Created

↓

Running

↓

Waiting

↓

Completed
```

Sometimes

```
Created

↓

Running

↓

Faulted
```

Or

```
Created

↓

Canceled
```

---

# Simple async Task Example

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        Console.WriteLine("Main Started");

        await PrintAsync();

        Console.WriteLine("Main Finished");
    }

    static async Task PrintAsync()
    {
        Console.WriteLine("Inside Method");

        await Task.Delay(3000);

        Console.WriteLine("Method Completed");
    }
}
```

---

# Output

```
Main Started

Inside Method

(Wait 3 Seconds)

Method Completed

Main Finished
```

---

# Dry Run

Execution starts

```
Main()
```

↓

Print

```
Main Started
```

↓

Call

```csharp
await PrintAsync();
```

↓

Enter

```
PrintAsync()
```

↓

Print

```
Inside Method
```

↓

Execute

```csharp
await Task.Delay(3000);
```

↓

Method pauses

↓

Thread returns to Thread Pool

↓

Timer completes

↓

Method resumes

↓

Print

```
Method Completed
```

↓

Return to Main

↓

Print

```
Main Finished
```

---

# Visualization

```
Main()

↓

PrintAsync()

↓

await Task.Delay()

↓

Pause Method

↓

Thread Released

↓

Timer Completes

↓

Resume Method

↓

Return

↓

Main Continues
```

---

# Calling Method Without await

Suppose

```csharp
PrintAsync();

Console.WriteLine("Done");
```

Output may become

```
Inside Method

Done

Method Completed
```

Because Main does not wait.

---

# Using await

```csharp
await PrintAsync();

Console.WriteLine("Done");
```

Output

```
Inside Method

Method Completed

Done
```

await waits for completion.

---

# Multiple Tasks

Example

```csharp
static async Task Main()
{
    Task task1 = Task.Delay(2000);

    Task task2 = Task.Delay(3000);

    await task1;

    await task2;

    Console.WriteLine("Finished");
}
```

Both tasks execute asynchronously.

---

# Task vs Thread

| Task | Thread |
|-------|--------|
| Logical Unit of Work | Physical OS Thread |
| Lightweight | Heavyweight |
| Uses Thread Pool | Direct OS Thread |
| Recommended | Rarely used directly |
| Supports async/await | Does not |

Remember

```
Task

≠

Thread
```

A Task **may** use a thread.

A thread can execute many Tasks.

---

# Task vs Task<T>

Task

```
Returns nothing
```

Example

```csharp
public async Task SaveAsync()
```

Task<T>

```
Returns a value
```

Example

```csharp
public async Task<int> GetAgeAsync()
```

---

# Exception Handling

Suppose

```csharp
public async Task Demo()
{
    throw new Exception();
}
```

Caller

```csharp
try
{
    await Demo();
}
catch(Exception ex)
{
    Console.WriteLine(ex.Message);
}
```

Exceptions propagate naturally when awaited.

---

# Real World ASP.NET Core Example

```csharp
[HttpGet]
public async Task<IActionResult> GetUsers()
{
    var users = await _repository.GetUsersAsync();

    return Ok(users);
}
```

Execution

```
HTTP Request

↓

Controller

↓

Repository

↓

Database

↓

Waiting

↓

Thread Released

↓

Database Responds

↓

Controller Continues

↓

Response Returned
```

This improves scalability because request threads are available to serve other users while waiting for the database.

---

# Common Mistakes

## Mistake 1

Using

```csharp
Thread.Sleep()
```

inside async methods.

Wrong

```csharp
Thread.Sleep(3000);
```

Correct

```csharp
await Task.Delay(3000);
```

---

## Mistake 2

Ignoring returned Task

Wrong

```csharp
SaveAsync();
```

Better

```csharp
await SaveAsync();
```

---

## Mistake 3

Returning void

Wrong

```csharp
async void Save()
```

Correct

```csharp
async Task Save()
```

---

## Mistake 4

Thinking Task creates a thread.

Task represents work.

It does not guarantee a new thread.

---

# Best Practices

✔ Return Task for asynchronous methods that do not return a value.

✔ Always await Tasks unless there is a valid reason not to.

✔ Prefer async APIs provided by .NET.

✔ Handle exceptions with try/catch around awaited Tasks.

✔ Avoid Thread.Sleep() in async code.

---

# Interview Questions

### Q1. What is Task?

### Q2. Why do async methods return Task?

### Q3. Does Task create a thread?

### Q4. What happens when a Task is awaited?

### Q5. What is the difference between Task and Thread?

### Q6. What is the difference between Task and Task<T>?

### Q7. Can a Task fail?

---

# Interview Answers

### What is Task?

Task is an object that represents an asynchronous operation. It allows the caller to monitor, await, or retrieve the result of that operation.

---

### Why return Task?

Returning Task allows the caller to await completion, observe exceptions, and compose asynchronous operations.

---

### Does Task create a thread?

No.

A Task represents work. The .NET runtime decides whether a thread is needed. Many asynchronous I/O operations complete without dedicating a thread while waiting.

---

### What happens when await is used?

The method pauses, returns control to the caller, and resumes execution automatically when the Task completes.

---

### Difference between Task and Thread?

A Thread is an operating system resource that executes code.

A Task is a higher-level abstraction representing a unit of work that may run on a thread.

---

### Can Task fail?

Yes.

A Task can complete successfully, fault with an exception, or be cancelled.

---

# Key Takeaways

- Task represents an asynchronous operation.
- async methods that don't return a value should return Task.
- await pauses the method without blocking the thread.
- Task is **not** a thread.
- Tasks improve application scalability by freeing threads during I/O waits.
- Always await Tasks unless you intentionally want fire-and-forget behavior.
- Prefer Task over async void for asynchronous methods.

---

# Next Module

➡ **Module 4.6 – async Task<T>**

You'll learn:

- Returning values asynchronously
- Difference between Task and Task<T>
- Generic Tasks
- Repository and ASP.NET Core examples
- Interview questions and best practices