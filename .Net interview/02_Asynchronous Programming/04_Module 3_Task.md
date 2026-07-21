# 3.1 What is Task? (C# / .NET Interview Notes)

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# What is a Task?

A **Task** represents a **unit of work (an operation)** that executes **asynchronously**.

It is an object that represents work that is happening now or will happen in the future.

A Task can:

- Execute asynchronously
- Return a value
- Not return a value
- Complete successfully
- Fail with an exception
- Be cancelled

A Task is part of the **Task Parallel Library (TPL)** introduced in **.NET Framework 4.0**.

Namespace

```csharp
using System.Threading.Tasks;
```

---

# Simple Definition

> **Task is an object that represents an asynchronous operation. It allows work to run without blocking the calling thread.**

---

# Why Was Task Introduced?

Before Tasks, developers mainly used

- Thread
- ThreadPool
- BackgroundWorker

These approaches had several problems.

- Creating threads was expensive.
- Managing thread lifetime was difficult.
- Returning values required extra code.
- Exception handling was complicated.
- Coordinating multiple threads was difficult.

To simplify asynchronous programming, Microsoft introduced the **Task Parallel Library (TPL)**.

---

# Why Do We Need Task?

Imagine an application that performs the following operations.

- Download a file
- Read data from a database
- Call a Web API
- Send an email

These operations may take several seconds.

Without Tasks, the application waits until each operation completes.

With Tasks, the application can continue doing other work while the operation executes in the background.

---

# Real-Life Example

Imagine you order food at a restaurant.

## Without Task (Synchronous)

```text
Order Food

↓

Wait

↓

Wait

↓

Food Ready

↓

Take Food
```

You cannot do anything else while waiting.

---

## With Task (Asynchronous)

```text
Order Food

↓

Receive Token

↓

Talk to Friends

↓

Use Mobile

↓

Food Ready

↓

Collect Food
```

The **token** represents a **Task**.

The work is happening in the background while you continue with other activities.

---

# Is Task a Thread?

**No.**

This is one of the most common interview questions.

A **Task is NOT a thread**.

A Task is an object that represents **work**.

Usually, the work runs on a **ThreadPool Worker Thread**, but not always.

Example

```csharp
await File.ReadAllTextAsync("data.txt");
```

This method returns a **Task**, but no thread waits for the file to finish reading. The operating system performs the I/O operation asynchronously, and the Task simply represents that operation.

---

# Task vs Thread

| Thread | Task |
|---------|------|
| Actual OS Thread | Represents a unit of work |
| Heavyweight | Lightweight |
| Created manually | Managed by Task Parallel Library |
| Expensive | More efficient |
| Manual management | Automatic management |
| Difficult to coordinate | Easy with async/await |

---

# Memory Trick

```text
Thread = Worker

Task = Work
```

A worker performs work.

The Task represents the work.

---

# Types of Task

## Task

Represents work that **does not return a value**.

Example

```csharp
Task task = Task.Run(() =>
{
    Console.WriteLine("Processing...");
});
```

---

## Task<TResult>

Represents work that **returns a value**.

Example

```csharp
Task<int> task = Task.Run(() =>
{
    return 100;
});
```

We'll study `Task<TResult>` in detail later.

---

# Internal Working

When we write

```csharp
Task.Run(() =>
{
    Console.WriteLine("Downloading...");
});
```

Internally

```text
Main Thread

↓

Create Task

↓

Task Scheduler

↓

ThreadPool

↓

Worker Thread Executes Task

↓

Task Completed
```

Notice that the Task is **scheduled** first, and then the ThreadPool chooses a worker thread to execute it.

---

# Common Uses of Task

Tasks are commonly used for

- Calling Web APIs
- Database Operations
- Reading Files
- Writing Files
- Sending Emails
- Downloading Files
- Background Processing
- Parallel Execution

---

# Simple Example

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        Console.WriteLine("Start");

        await Task.Delay(3000);

        Console.WriteLine("End");
    }
}
```

Output

```text
Start

(Wait 3 Seconds)

End
```

During the delay, the thread is **not blocked**.

---

# Advantages

- Easy asynchronous programming.
- Integrates with `async` and `await`.
- Uses ThreadPool efficiently.
- Supports return values.
- Supports exception handling.
- Supports cancellation.
- Easy to run multiple operations together.

---

# Disadvantages

- Incorrect usage may cause deadlocks.
- Blocking with `.Wait()` or `.Result` reduces performance.
- Fire-and-forget tasks may hide exceptions.
- Requires understanding of asynchronous programming.

---

# Common Mistakes

## Mistake 1

Thinking Task is a Thread.

Wrong

```text
Task = Thread
```

Correct

```text
Task = Work

Thread = Worker
```

---

## Mistake 2

Thinking every Task creates a new thread.

Wrong.

Most Tasks use **ThreadPool threads**.

Some asynchronous operations (like file or network I/O) do not occupy a thread while waiting.

---

## Mistake 3

Using `.Wait()` or `.Result` unnecessarily.

Prefer

```csharp
await
```

instead of

```csharp
.Wait()

.Result
```

---

# Interview Questions

## Q1. What is Task?

Task represents an asynchronous operation or unit of work.

---

## Q2. Is Task a Thread?

No.

Task represents work.

Thread executes the work.

---

## Q3. Which namespace contains Task?

```csharp
System.Threading.Tasks
```

---

## Q4. What is the difference between Task and Thread?

Thread is an actual operating system thread.

Task is a higher-level abstraction that represents work and is usually executed by ThreadPool threads.

---

## Q5. Why is Task preferred over Thread?

Because it

- Uses ThreadPool efficiently
- Supports async/await
- Can return values
- Supports cancellation
- Supports exception handling
- Is easier to manage

---

## Q6. Can a Task return a value?

Yes.

Using

```csharp
Task<TResult>
```

Example

```csharp
Task<int>

Task<string>

Task<Employee>
```

---

# Interview Answer

> A Task is an object that represents an asynchronous operation or unit of work. It is part of the Task Parallel Library (TPL) and simplifies asynchronous programming in .NET. A Task is not a thread; instead, it represents work that is typically executed by ThreadPool threads or asynchronous I/O operations. Tasks integrate with `async` and `await`, can return values using `Task<TResult>`, support exception handling and cancellation, and make it easier to coordinate multiple asynchronous operations.

---

# Key Points to Remember

- Task represents **work**, not a thread.
- Namespace: `System.Threading.Tasks`
- Part of the **Task Parallel Library (TPL)**.
- Foundation of **async** and **await**.
- Usually executes on **ThreadPool** threads.
- Can return values using `Task<TResult>`.
- Supports exceptions and cancellation.
- Preferred over manually creating threads.
- Used extensively in ASP.NET Core, Web APIs, file I/O, database operations, and network communication.

# 3.2 Task Lifecycle (C# / .NET Interview Notes)

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# What is Task Lifecycle?

A **Task Lifecycle** is the sequence of states that a **Task** goes through from the moment it is created until it finishes.

A Task can

- Be created
- Wait to execute
- Start running
- Complete successfully
- Fail with an exception
- Be cancelled

Every Task moves through one or more of these states during its lifetime.

---

# Why Do We Need to Understand Task Lifecycle?

Understanding the Task Lifecycle helps us

- Debug asynchronous applications
- Track task execution
- Monitor task progress
- Handle failures correctly
- Understand Task.Status

---

# Task Lifecycle Diagram

```text
                    Create Task
                         │
                         ▼
                     Created
                         │
                         ▼
                  WaitingToRun
                         │
                         ▼
                     Running
                ┌────────┼────────┐
                ▼        ▼        ▼
      RanToCompletion  Faulted  Canceled
```

---

# Task States

## 1. Created

The Task object has been created but has **not started execution**.

Example

```csharp
Task task = new Task(() =>
{
    Console.WriteLine("Task Started");
});
```

Current Status

```text
Created
```

The task starts only after calling

```csharp
task.Start();
```

---

### Visualization

```text
Task Created

↓

Waiting for Start()
```

---

## 2. WaitingToRun

After calling

```csharp
task.Start();
```

the Task is sent to the **Task Scheduler**.

It waits until a ThreadPool worker thread becomes available.

Current Status

```text
WaitingToRun
```

---

### Visualization

```text
Task Created

↓

Task Scheduler

↓

Queue

↓

WaitingToRun
```

---

## 3. Running

The Task Scheduler assigns a ThreadPool worker thread.

The Task begins executing.

Current Status

```text
Running
```

Example

```csharp
Task.Run(() =>
{
    Console.WriteLine("Downloading...");
});
```

---

### Visualization

```text
ThreadPool

↓

Worker Thread

↓

Executing Task

↓

Running
```

---

## 4. RanToCompletion

The Task completed successfully without throwing an exception.

Current Status

```text
RanToCompletion
```

Example

```csharp
Task task = Task.Run(() =>
{
    Console.WriteLine("Completed");
});

await task;
```

---

### Visualization

```text
Running

↓

Completed Successfully

↓

RanToCompletion
```

---

## 5. Faulted

The Task throws an exception while executing.

Current Status

```text
Faulted
```

Example

```csharp
Task task = Task.Run(() =>
{
    throw new Exception("Something went wrong");
});
```

---

### Visualization

```text
Running

↓

Exception

↓

Faulted
```

---

## 6. Canceled

The Task is cancelled using a **CancellationToken**.

Current Status

```text
Canceled
```

Example

```csharp
CancellationTokenSource cts = new CancellationTokenSource();

Task task = Task.Run(() =>
{
    cts.Token.ThrowIfCancellationRequested();
}, cts.Token);

cts.Cancel();
```

---

### Visualization

```text
Running

↓

Cancellation Requested

↓

Canceled
```

---

# Complete Task Lifecycle

```text
Task Created

↓

Created

↓

Start()

↓

WaitingToRun

↓

ThreadPool Picks Worker Thread

↓

Running

↓

Completed Successfully

↓

RanToCompletion
```

---

If an exception occurs

```text
Running

↓

Exception

↓

Faulted
```

---

If cancellation is requested

```text
Running

↓

Cancellation

↓

Canceled
```

---

# How to Check Task Status

Use the **Status** property.

```csharp
Console.WriteLine(task.Status);
```

Example

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        Task task = Task.Run(async () =>
        {
            await Task.Delay(2000);
        });

        Console.WriteLine("Current Status : " + task.Status);

        await task;

        Console.WriteLine("Final Status : " + task.Status);
    }
}
```

Possible Output

```text
Current Status : WaitingForActivation

Final Status : RanToCompletion
```

> **Note:** When using `async` methods, you may see **WaitingForActivation** instead of **WaitingToRun**. This is expected behavior because asynchronous operations often wait for completion of I/O or other asynchronous work.

---

# Dry Run

Suppose we execute

```csharp
Task task = Task.Run(() =>
{
    Console.WriteLine("Downloading File...");
});
```

### Step 1

Task is created.

```text
Status = Created
```

↓

### Step 2

Task Scheduler receives the Task.

```text
Status = WaitingToRun
```

↓

### Step 3

ThreadPool assigns a worker thread.

```text
Status = Running
```

↓

### Step 4

Task finishes successfully.

```text
Status = RanToCompletion
```

---

# Dry Run Table

| Step | Task State | Description |
|------|------------|-------------|
| 1 | Created | Task object created |
| 2 | WaitingToRun | Waiting for ThreadPool thread |
| 3 | Running | Task is executing |
| 4 | RanToCompletion | Task completed successfully |

---

# Advantages

- Easy to monitor task execution.
- Helps debug asynchronous code.
- Useful for handling errors.
- Enables tracking long-running operations.

---

# Common Mistakes

## Mistake 1

Assuming every Task starts immediately.

Wrong

```text
Create Task

↓

Running
```

Correct

```text
Create Task

↓

Task Scheduler

↓

Running
```

---

## Mistake 2

Thinking every Task enters the **Created** state.

Tasks created using

```csharp
Task.Run()
```

usually start immediately, so you may never observe the **Created** state.

---

## Mistake 3

Ignoring the **Faulted** state.

Always handle exceptions using

```csharp
try
{
    await task;
}
catch(Exception ex)
{
    Console.WriteLine(ex.Message);
}
```

---

# Interview Questions

## Q1. What is Task Lifecycle?

Task Lifecycle is the sequence of states through which a Task passes from creation until completion, failure, or cancellation.

---

## Q2. What are the different Task states?

- Created
- WaitingToRun
- Running
- RanToCompletion
- Faulted
- Canceled

---

## Q3. Which property returns the current Task state?

```csharp
task.Status
```

---

## Q4. What is the difference between Created and WaitingToRun?

**Created**

- Task object exists.
- Task has not started.

**WaitingToRun**

- Task has been started.
- Waiting for a ThreadPool worker thread.

---

## Q5. What is the difference between RanToCompletion and Faulted?

**RanToCompletion**

- Task completed successfully.

**Faulted**

- Task completed with an unhandled exception.

---

## Q6. Can every Task enter every state?

No.

For example, a Task created using `Task.Run()` usually starts immediately, so you may never observe the **Created** state.

---

# Interview Answer

> A Task Lifecycle describes the different states a Task passes through during its execution. A Task is typically **Created**, then moves to **WaitingToRun**, then **Running**, and finally ends in **RanToCompletion**, **Faulted**, or **Canceled**. We can determine the current state of a Task using the `Status` property.

---

# Key Points to Remember

- Every Task has a lifecycle.
- Typical flow:

```text
Created

↓

WaitingToRun

↓

Running

↓

RanToCompletion
```

- A Task may also end in:
  - **Faulted**
  - **Canceled**
- Use `task.Status` to determine the current state.
- `Task.Run()` tasks often skip the observable **Created** state because they are scheduled immediately.
- Understanding the Task Lifecycle is essential for debugging and writing reliable asynchronous applications.

# 3.4 Task.CompletedTask (C# / .NET Interview Notes)

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# What is Task.CompletedTask?

`Task.CompletedTask` is a **pre-completed Task** that has already finished successfully.

It is used when an asynchronous method returns **`Task`** but **does not have any asynchronous work to perform**.

Instead of creating a new `Task`, .NET returns an already completed Task.

---

# Simple Definition

> **Task.CompletedTask is a static property that returns a Task which has already completed successfully.**

---

# Why Do We Need Task.CompletedTask?

Consider a method that returns `Task`.

```csharp
public Task SaveAsync()
{
    // No asynchronous work

    return ???;
}
```

Since the method must return a `Task`, what should it return?

One option is to create a new Task.

```csharp
return new Task(() => { });
```

❌ This is unnecessary because creating a new Task consumes memory and requires scheduling.

Instead, return

```csharp
return Task.CompletedTask;
```

This returns an already completed Task.

---

# Syntax

```csharp
Task.CompletedTask
```

---

# Real-Life Example

Imagine your manager asks,

> "Did you complete today's report?"

You already finished it yesterday.

Instead of doing the work again, you simply reply,

```text
Already Completed
```

`Task.CompletedTask` is exactly that.

The work is already complete.

---

# When Should We Use Task.CompletedTask?

Use it when

- The method returns `Task`.
- There is **no value to return**.
- There is **no asynchronous work** to perform.
- You need to satisfy the method's return type.

---

# Example 1 - Simple Method

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        await PrintMessageAsync();

        Console.WriteLine("Finished");
    }

    static Task PrintMessageAsync()
    {
        Console.WriteLine("Hello");

        return Task.CompletedTask;
    }
}
```

Output

```text
Hello

Finished
```

---

# Dry Run

```text
Main()

↓

Call PrintMessageAsync()

↓

Print "Hello"

↓

Return CompletedTask

↓

Task Already Completed

↓

Main Continues

↓

Print "Finished"
```

---

# Dry Run Table

| Step | Operation | Status |
|------|-----------|--------|
| 1 | Call PrintMessageAsync() | Running |
| 2 | Print "Hello" | Executed |
| 3 | Return CompletedTask | Completed |
| 4 | await finishes immediately | Completed |
| 5 | Print "Finished" | Done |

---

# Example 2 - Repository Pattern

```csharp
public Task DeleteEmployeeAsync(int id)
{
    Console.WriteLine("Employee Deleted");

    return Task.CompletedTask;
}
```

Even though the method returns `Task`, there is no asynchronous work.

---

# Example 3 - Interface Implementation

Suppose an interface requires

```csharp
public interface ILogger
{
    Task LogAsync(string message);
}
```

Implementation

```csharp
public class ConsoleLogger : ILogger
{
    public Task LogAsync(string message)
    {
        Console.WriteLine(message);

        return Task.CompletedTask;
    }
}
```

No asynchronous work is performed, but the interface requires a `Task`.

---

# Internal Working

```text
Method Called

↓

Execute Synchronous Code

↓

Return CompletedTask

↓

Task Already Finished

↓

Caller Continues Immediately
```

No new Task is created.

No ThreadPool thread is used.

---

# Task vs Task.CompletedTask

| Task | Task.CompletedTask |
|------|--------------------|
| Represents work to be completed | Already completed |
| May take time | Completes immediately |
| Can be Running | Always RanToCompletion |
| May use ThreadPool | No new thread or scheduling |

---

# Task.CompletedTask vs Task.FromResult()

| Task.CompletedTask | Task.FromResult() |
|--------------------|-------------------|
| No return value | Returns a value |
| Returns `Task` | Returns `Task<TResult>` |
| Already completed | Already completed with a result |

Example

```csharp
return Task.CompletedTask;
```

Example

```csharp
return Task.FromResult(100);
```

---

# Advantages

- No unnecessary Task creation.
- Better performance.
- Less memory allocation.
- Cleaner code.
- Ideal for methods with no asynchronous work.

---

# Common Mistakes

## Mistake 1

Creating a new Task unnecessarily.

Wrong

```csharp
return new Task(() => { });
```

Correct

```csharp
return Task.CompletedTask;
```

---

## Mistake 2

Using `Task.CompletedTask` when a value must be returned.

Wrong

```csharp
Task<int> GetNumber()
{
    return Task.CompletedTask;
}
```

Correct

```csharp
Task<int> GetNumber()
{
    return Task.FromResult(100);
}
```

---

## Mistake 3

Adding `async` when it isn't needed.

Wrong

```csharp
public async Task SaveAsync()
{
    Console.WriteLine("Saved");
}
```

Correct

```csharp
public Task SaveAsync()
{
    Console.WriteLine("Saved");

    return Task.CompletedTask;
}
```

If there is no `await`, you usually don't need the `async` keyword.

---

# Interview Questions

## Q1. What is Task.CompletedTask?

`Task.CompletedTask` is a Task that has already completed successfully.

---

## Q2. When should we use Task.CompletedTask?

When a method returns `Task` but has no asynchronous work and no value to return.

---

## Q3. Does Task.CompletedTask create a new Task?

No.

It returns a pre-completed Task provided by .NET.

---

## Q4. What is the difference between Task.CompletedTask and Task.FromResult()?

**Task.CompletedTask**

- Returns `Task`
- No value

**Task.FromResult()**

- Returns `Task<TResult>`
- Returns a value

---

## Q5. Does Task.CompletedTask use a ThreadPool thread?

No.

It simply returns an already completed Task.

---

# Interview Answer

> `Task.CompletedTask` is a static property that returns a Task that has already completed successfully. It is used when a method returns `Task` but performs no asynchronous work and does not return a value. It avoids creating unnecessary Task objects, improves performance, and is commonly used in interface implementations, repositories, and methods that must return a `Task`.

---

# Key Points to Remember

- `Task.CompletedTask` returns an **already completed Task**.
- Used for methods returning **`Task`** with **no result**.
- No new Task is created.
- No ThreadPool thread is used.
- Improves performance by avoiding unnecessary allocations.
- Do **not** use it when a value must be returned.
- Use `Task.FromResult<TResult>()` when you need to return a value asynchronously.

# 3.5 Task.FromResult() (C# / .NET Interview Notes)

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# What is Task.FromResult()?

`Task.FromResult()` is a static method that creates a **completed Task<TResult>** containing a specified result.

It is used when **the result is already available**, but the method signature requires returning `Task<TResult>`.

In simple words,

> **Task.FromResult() wraps an already available value inside a completed Task.**

---

# Simple Definition

> **Task.FromResult() returns a completed Task<TResult> with an already available result without performing any asynchronous work.**

---

# Why Do We Need Task.FromResult()?

Suppose you have a method like this:

```csharp
public Task<int> GetAgeAsync()
{
    return ???;
}
```

The method promises to return a **Task<int>**.

But suppose you already know the answer.

```text
Age = 25
```

Can you write

```csharp
return 25;
```

❌ No.

Because the compiler expects

```text
Task<int>
```

not

```text
int
```

Instead, wrap the value inside a completed Task.

```csharp
return Task.FromResult(25);
```

---

# When Should We Use Task.FromResult()?

Use `Task.FromResult()` when

- The method returns `Task<TResult>`
- The result is already available
- No database call is required
- No Web API call is required
- No file operation is required
- No asynchronous work is needed

---

# When NOT to Use Task.FromResult()

Do **not** use it when the method performs real asynchronous work.

Examples

- Database operations
- Web API calls
- Reading files
- Sending emails
- Network communication

Instead, use

```csharp
await
```

---

# Decision Tree

```text
Does the method return a value?

        │
       Yes
        │
        ▼

Do you already have the value?

      ┌──────────────┐
      │              │
     Yes            No
      │              │
      ▼              ▼

Task.FromResult()   await Database/API/File
```

---

# Real-Life Example

Imagine your manager asks,

> "Can you send me today's attendance report?"

### Situation 1

The report is already on your desk.

You immediately hand it over.

```text
Manager Requests Report

↓

Report Already Available

↓

Give Report
```

This is exactly how **Task.FromResult()** works.

---

### Situation 2

The report has not been prepared yet.

You need to

- Collect attendance
- Generate report
- Verify report

Only then can you return it.

This is similar to

```csharp
await
```

---

# Syntax

```csharp
Task.FromResult<TResult>(result)
```

Examples

```csharp
Task.FromResult(100);

Task.FromResult("Rakesh");

Task.FromResult(true);

Task.FromResult(employee);
```

---

# Example 1 - Returning an Integer

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        int age = await GetAgeAsync();

        Console.WriteLine(age);
    }

    static Task<int> GetAgeAsync()
    {
        return Task.FromResult(25);
    }
}
```

Output

```text
25
```

---

# Dry Run

```text
Main()

↓

Call GetAgeAsync()

↓

Value Already Available

↓

Wrap 25 inside Task<int>

↓

Return Completed Task

↓

await Receives 25

↓

Print 25
```

---

# Dry Run Table

| Step | Operation | Result |
|------|-----------|--------|
| 1 | Call GetAgeAsync() | Task<int> |
| 2 | Value already available | 25 |
| 3 | Task.FromResult(25) | Completed Task<int> |
| 4 | await | 25 |
| 5 | Print | 25 |

---

# Example 2 - Returning an Object

```csharp
using System.Threading.Tasks;

class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }
}

class Repository
{
    public Task<Employee> GetEmployeeAsync()
    {
        Employee employee = new Employee
        {
            Id = 101,
            Name = "Rakesh"
        };

        return Task.FromResult(employee);
    }
}
```

---

# Example 3 - Returning a List

```csharp
using System.Collections.Generic;
using System.Threading.Tasks;

class Repository
{
    public Task<List<string>> GetCitiesAsync()
    {
        List<string> cities = new()
        {
            "Bangalore",
            "Hyderabad",
            "Chennai"
        };

        return Task.FromResult(cities);
    }
}
```

---

# Internal Working

```text
Method Called

↓

Result Already Exists

↓

Task.FromResult()

↓

Completed Task<TResult>

↓

Caller Gets Result Immediately
```

No ThreadPool thread is created.

No asynchronous work is performed.

---

# Real-World Use Cases

`Task.FromResult()` is commonly used in

- Repository Pattern
- Mock Repositories
- Unit Testing
- In-memory Cache
- Configuration Data
- Returning Static Data

---

# Task.FromResult() vs Task.CompletedTask

| Task.FromResult() | Task.CompletedTask |
|-------------------|--------------------|
| Returns Task<TResult> | Returns Task |
| Returns a value | No return value |
| Result already available | No result |

Example

```csharp
return Task.FromResult(100);
```

Example

```csharp
return Task.CompletedTask;
```

---

# Task.FromResult() vs Task.Run()

| Task.FromResult() | Task.Run() |
|-------------------|------------|
| Result already available | Performs actual work |
| No ThreadPool thread | Uses ThreadPool thread |
| Completes immediately | Executes asynchronously |
| No scheduling | Scheduled by Task Scheduler |

Example

```csharp
return Task.FromResult(100);
```

Example

```csharp
return Task.Run(() =>
{
    return CalculateSalary();
});
```

---

# Advantages

- Very fast.
- No ThreadPool usage.
- No unnecessary Task creation.
- Cleaner code.
- Ideal for mock implementations.
- Preserves asynchronous method signatures.

---

# Common Mistakes

## Mistake 1

Using `Task.Run()` for an already available value.

Wrong

```csharp
return Task.Run(() => 25);
```

Correct

```csharp
return Task.FromResult(25);
```

---

## Mistake 2

Returning the value directly.

Wrong

```csharp
return 25;
```

Correct

```csharp
return Task.FromResult(25);
```

---

## Mistake 3

Using `Task.CompletedTask` when returning data.

Wrong

```csharp
Task<int> GetAgeAsync()
{
    return Task.CompletedTask;
}
```

Correct

```csharp
Task<int> GetAgeAsync()
{
    return Task.FromResult(25);
}
```

---

# Interview Questions

## Q1. What is Task.FromResult()?

It creates a completed `Task<TResult>` that contains an already available result.

---

## Q2. When should we use Task.FromResult()?

Use it when

- The method returns `Task<TResult>`
- The result is already available
- No asynchronous work is required

---

## Q3. Does Task.FromResult() create a ThreadPool thread?

No.

It simply wraps the value inside a completed Task.

---

## Q4. What is the difference between Task.FromResult() and Task.Run()?

**Task.FromResult()**

- Result already exists.
- No background thread.
- Returns immediately.

**Task.Run()**

- Performs actual work.
- Usually runs on a ThreadPool thread.

---

## Q5. What is the difference between Task.CompletedTask and Task.FromResult()?

**Task.CompletedTask**

- Returns `Task`
- No value

**Task.FromResult()**

- Returns `Task<TResult>`
- Returns a value

---

# Interview Answer

> `Task.FromResult()` is a static method that creates a completed `Task<TResult>` containing an already available result. It is used when a method must return `Task<TResult>`, but no asynchronous work is required because the result is already known. It avoids unnecessary thread creation and is commonly used in repositories, caching, mock services, and unit testing.

---

# Key Points to Remember

- `Task.FromResult()` returns a **completed `Task<TResult>`**.
- It **wraps an already available value** inside a Task.
- No ThreadPool thread is created.
- No asynchronous work is performed.
- Use it when the method signature requires `Task<TResult>`, but the result is already known.
- Prefer it over `Task.Run()` when no background work is needed.
- Commonly used in repositories, caching, mock services, and unit testing.

# 3.6 Task.Run() (C# / .NET Interview Notes)

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# What is Task.Run()?

`Task.Run()` is a static method that **creates and starts a Task** on a **ThreadPool worker thread**.

It is used to execute work **asynchronously** without blocking the calling thread.

Unlike `Task.FromResult()`, `Task.Run()` performs **actual work**.

---

# Simple Definition

> **Task.Run() executes a piece of code asynchronously on a ThreadPool worker thread and returns a Task representing that operation.**

---

# Why Do We Need Task.Run()?

Imagine your application needs to perform a time-consuming operation.

Examples:

- Generate a PDF
- Resize an image
- Perform a complex calculation
- Compress a large file
- Process millions of records

If this work runs on the main thread, the application becomes unresponsive.

Instead, we move the work to a ThreadPool thread using `Task.Run()`.

---

# Real-Life Example

Imagine you're cooking dinner.

You need to

- Prepare vegetables
- Cook rice
- Wash dishes

Cooking rice takes 20 minutes.

Instead of standing near the stove doing nothing, you start cooking the rice and continue preparing vegetables.

```text
Start Cooking Rice

↓

Rice Cooks in Background

↓

Prepare Vegetables

↓

Wash Dishes

↓

Rice Ready
```

`Task.Run()` works the same way.

It starts the work in the background so the calling thread can continue.

---

# Syntax

## Without Return Value

```csharp
Task.Run(() =>
{
    // Work
});
```

---

## With Return Value

```csharp
Task.Run(() =>
{
    return 100;
});
```

Returns

```csharp
Task<int>
```

---

# Example 1 - Running Background Work

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        Console.WriteLine("Main Thread Started");

        await Task.Run(() =>
        {
            Thread.Sleep(3000);

            Console.WriteLine("Background Work Completed");
        });

        Console.WriteLine("Main Thread Finished");
    }
}
```

Output

```text
Main Thread Started

(Wait 3 Seconds)

Background Work Completed

Main Thread Finished
```

---

# Dry Run

```text
Main()

↓

Call Task.Run()

↓

Task Scheduler

↓

ThreadPool Worker Thread

↓

Execute Work

↓

Task Completes

↓

Main Continues
```

---

# Dry Run Table

| Step | Operation | Status |
|------|-----------|--------|
| 1 | Main calls Task.Run() | Running |
| 2 | Task Scheduler receives task | Queued |
| 3 | ThreadPool assigns worker thread | Running |
| 4 | Work completes | Completed |
| 5 | await resumes Main | Finished |

---

# Example 2 - Returning a Value

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        int result = await Task.Run(() =>
        {
            return 50 + 50;
        });

        Console.WriteLine(result);
    }
}
```

Output

```text
100
```

---

# Internal Working

When we call

```csharp
Task.Run(() =>
{
    Console.WriteLine("Processing...");
});
```

Internally

```text
Main Thread

↓

Create Task

↓

Task Scheduler

↓

ThreadPool Queue

↓

Worker Thread Executes Task

↓

Task Completed
```

The important point is that **Task.Run() uses the ThreadPool**.

It does **not** create a new operating system thread every time.

---

# Task.Run() vs Task.FromResult()

| Task.Run() | Task.FromResult() |
|-------------|-------------------|
| Performs actual work | Result already available |
| Uses ThreadPool | No ThreadPool |
| Executes asynchronously | Returns immediately |
| May take time | Completes instantly |

Example

```csharp
return Task.Run(() =>
{
    return CalculateSalary();
});
```

Example

```csharp
return Task.FromResult(50000);
```

---

# Task.Run() vs Task.CompletedTask

| Task.Run() | Task.CompletedTask |
|-------------|--------------------|
| Executes work | No work |
| Uses ThreadPool | Already completed |
| Takes time | Immediate completion |

---

# When Should We Use Task.Run()?

Use `Task.Run()` for

- CPU-intensive calculations
- Image processing
- File compression
- Data conversion
- Report generation
- Background processing

---

# When NOT to Use Task.Run()

Avoid `Task.Run()` for

- Database queries
- Web API calls
- File I/O
- Network communication
- Entity Framework Core operations

These APIs already provide asynchronous methods.

Instead of

```csharp
await Task.Run(() =>
{
    dbContext.SaveChanges();
});
```

Use

```csharp
await dbContext.SaveChangesAsync();
```

Similarly,

Instead of

```csharp
await Task.Run(() =>
{
    File.ReadAllText(path);
});
```

Use

```csharp
await File.ReadAllTextAsync(path);
```

---

# Advantages

- Keeps the calling thread responsive.
- Uses ThreadPool efficiently.
- Supports `async` and `await`.
- Can return values.
- Easy to execute CPU-bound work.

---

# Disadvantages

- Not suitable for I/O-bound work.
- Uses a ThreadPool thread.
- Overusing `Task.Run()` can exhaust the ThreadPool.
- Adds unnecessary overhead if no background work is required.

---

# Common Mistakes

## Mistake 1

Using `Task.Run()` for an already available value.

Wrong

```csharp
return Task.Run(() => 100);
```

Correct

```csharp
return Task.FromResult(100);
```

---

## Mistake 2

Wrapping asynchronous methods inside `Task.Run()`.

Wrong

```csharp
await Task.Run(() =>
{
    return dbContext.SaveChangesAsync();
});
```

Correct

```csharp
await dbContext.SaveChangesAsync();
```

The async API already performs asynchronous work.

---

## Mistake 3

Using `Task.Run()` for simple operations.

Wrong

```csharp
await Task.Run(() =>
{
    Console.WriteLine("Hello");
});
```

There is no benefit for such a small operation.

---

# Real-World Use Cases

`Task.Run()` is commonly used for

- Image resizing
- PDF generation
- Encryption
- Data conversion
- Complex mathematical calculations
- Background processing
- CPU-intensive business logic

---

# Interview Questions

## Q1. What is Task.Run()?

`Task.Run()` executes work asynchronously on a ThreadPool worker thread.

---

## Q2. Does Task.Run() create a new thread?

No.

It usually uses an available **ThreadPool worker thread**.

---

## Q3. When should we use Task.Run()?

Use it for **CPU-bound** operations that should not block the calling thread.

---

## Q4. Should Task.Run() be used for database operations?

No.

Use asynchronous database APIs such as

```csharp
await dbContext.SaveChangesAsync();

await dbContext.Employees.ToListAsync();
```

---

## Q5. What is the difference between Task.Run() and Task.FromResult()?

| Task.Run() | Task.FromResult() |
|-------------|-------------------|
| Executes work | Wraps an existing value |
| Uses ThreadPool | No ThreadPool |
| Takes time | Immediate completion |

---

# Interview Answer

> `Task.Run()` is a static method that queues work to the .NET ThreadPool and returns a Task representing that operation. It is primarily used for CPU-bound operations that should execute in the background without blocking the calling thread. It should not be used for I/O-bound operations like database or file access because .NET already provides asynchronous APIs for those scenarios.

---

# Key Points to Remember

- `Task.Run()` executes work on a **ThreadPool worker thread**.
- Used for **CPU-bound** operations.
- Returns `Task` or `Task<TResult>`.
- Supports `async` and `await`.
- Avoid using it for database, network, or file I/O operations.
- Prefer built-in asynchronous APIs (`ReadAsync`, `SaveChangesAsync`, `ToListAsync`) for I/O-bound work.
- `Task.Run()` performs work, whereas `Task.FromResult()` simply wraps an already available value.

# 3.7 Task.Factory.StartNew() (C# / .NET Interview Notes)

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# What is Task.Factory.StartNew()?

`Task.Factory.StartNew()` is a method that **creates and starts a new Task**.

Like `Task.Run()`, it schedules work for execution, but it provides **more control** over how the task is created and executed.

It allows you to specify:

- Task Scheduler
- Cancellation Token
- Task Creation Options
- Task Continuation Options

---

# Simple Definition

> **Task.Factory.StartNew() creates and starts a Task with advanced configuration options such as scheduler, cancellation, and task creation behavior.**

---

# Why Was StartNew Introduced?

Before `Task.Run()` was introduced in .NET 4.5, developers used

```csharp
Task.Factory.StartNew()
```

to execute background work.

Later, Microsoft introduced

```csharp
Task.Run()
```

because it is

- Simpler
- Safer
- Easier to read

Today, **Task.Run() is recommended for most applications**.

---

# Syntax

## Basic Syntax

```csharp
Task.Factory.StartNew(() =>
{
    // Work
});
```

---

## Returning a Value

```csharp
Task<int> task = Task.Factory.StartNew(() =>
{
    return 100;
});
```

---

# Real-Life Example

Imagine you are hiring a delivery company.

## Task.Run()

You simply say,

```text
Deliver this package.
```

The company decides

- Driver
- Route
- Vehicle

Everything is automatic.

---

## Task.Factory.StartNew()

You say,

```text
Deliver this package

Use Truck

Use Driver A

Take Highway Route

Deliver Before Noon
```

You have much more control.

---

# Example 1 - Simple Task

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        Task task = Task.Factory.StartNew(() =>
        {
            Console.WriteLine("Task Executing...");
        });

        task.Wait();

        Console.WriteLine("Completed");
    }
}
```

Output

```text
Task Executing...

Completed
```

---

# Dry Run

```text
Main()

↓

StartNew()

↓

Task Created

↓

Task Scheduler

↓

ThreadPool Worker Thread

↓

Execute Work

↓

Completed
```

---

# Dry Run Table

| Step | Operation | Status |
|------|-----------|--------|
| 1 | StartNew() called | Task Created |
| 2 | Task Scheduler | Queued |
| 3 | ThreadPool executes | Running |
| 4 | Work completed | Completed |
| 5 | Wait() returns | Finished |

---

# Example 2 - Returning a Value

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        Task<int> task = Task.Factory.StartNew(() =>
        {
            return 200;
        });

        Console.WriteLine(task.Result);
    }
}
```

Output

```text
200
```

---

# Example 3 - Using CancellationToken

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        CancellationTokenSource cts = new();

        Task task = Task.Factory.StartNew(() =>
        {
            Console.WriteLine("Running...");
        },
        cts.Token);

        task.Wait();
    }
}
```

---

# Internal Working

```text
StartNew()

↓

Create Task

↓

Task Scheduler

↓

ThreadPool Queue

↓

Worker Thread Executes Task

↓

Task Completed
```

---

# Task.Factory.StartNew() vs Task.Run()

| Task.Run() | Task.Factory.StartNew() |
|-------------|-------------------------|
| Simple API | Advanced API |
| Recommended for most applications | Used for advanced scenarios |
| Automatically unwraps async delegates | Does not automatically unwrap async delegates |
| Uses default Task Scheduler | Can use custom Task Scheduler |
| Less configuration | More configuration |

---

# Important Difference with async Methods

Consider this code:

```csharp
Task<Task> task = Task.Factory.StartNew(async () =>
{
    await Task.Delay(1000);
});
```

Notice the return type:

```text
Task<Task>
```

This happens because `StartNew()` does **not automatically unwrap** an async delegate.

With `Task.Run()`:

```csharp
Task task = Task.Run(async () =>
{
    await Task.Delay(1000);
});
```

The return type is simply

```text
Task
```

This is one of the biggest reasons Microsoft recommends **Task.Run()** for asynchronous code.

---

# When Should We Use Task.Factory.StartNew()?

Use it only when you need

- Custom Task Scheduler
- CancellationToken during creation
- LongRunning tasks
- Attached child tasks
- Advanced TaskCreationOptions

Example

```csharp
Task.Factory.StartNew(
    Work,
    CancellationToken.None,
    TaskCreationOptions.LongRunning,
    TaskScheduler.Default);
```

---

# When NOT to Use StartNew()

Avoid it for normal asynchronous programming.

Instead of

```csharp
Task.Factory.StartNew(() =>
{
    ProcessData();
});
```

Prefer

```csharp
Task.Run(() =>
{
    ProcessData();
});
```

`Task.Run()` is simpler and less error-prone.

---

# Advantages

- More configuration options.
- Supports custom Task Scheduler.
- Supports TaskCreationOptions.
- Supports LongRunning tasks.
- Supports AttachedToParent tasks.

---

# Disadvantages

- More complex API.
- Easy to misuse.
- Does not automatically unwrap async delegates.
- Less readable.
- Usually unnecessary in modern applications.

---

# Common Mistakes

## Mistake 1

Using `StartNew()` instead of `Task.Run()` for simple tasks.

Wrong

```csharp
Task.Factory.StartNew(() =>
{
    Console.WriteLine("Hello");
});
```

Preferred

```csharp
Task.Run(() =>
{
    Console.WriteLine("Hello");
});
```

---

## Mistake 2

Using `StartNew()` with async methods.

Wrong

```csharp
Task.Factory.StartNew(async () =>
{
    await Task.Delay(1000);
});
```

This returns

```text
Task<Task>
```

Preferred

```csharp
Task.Run(async () =>
{
    await Task.Delay(1000);
});
```

---

## Mistake 3

Using `StartNew()` without needing advanced options.

If you don't need custom scheduling or creation options, use `Task.Run()`.

---

# Real-World Use Cases

`Task.Factory.StartNew()` is used for

- Custom Task Scheduler
- Long-running background services
- Parallel libraries
- Advanced framework development
- Specialized task scheduling

It is **rarely used in everyday ASP.NET Core or Web API applications**.

---

# Interview Questions

## Q1. What is Task.Factory.StartNew()?

It creates and starts a Task with advanced configuration options.

---

## Q2. What is the difference between Task.Run() and Task.Factory.StartNew()?

`Task.Run()`

- Simpler
- Recommended
- Automatically unwraps async delegates

`Task.Factory.StartNew()`

- More configurable
- Supports custom schedulers
- Does not automatically unwrap async delegates

---

## Q3. Which one should we use in modern .NET applications?

Use

```csharp
Task.Run()
```

unless you specifically need advanced features offered by `Task.Factory.StartNew()`.

---

## Q4. Does StartNew() use the ThreadPool?

Yes.

By default, it uses the ThreadPool, unless a different scheduler or options are specified.

---

## Q5. Why is Task.Run() preferred?

Because it

- Is easier to read.
- Handles async delegates correctly.
- Requires less configuration.
- Reduces the chance of mistakes.

---

# Interview Answer

> `Task.Factory.StartNew()` is an advanced API for creating and starting Tasks with additional configuration options such as custom schedulers, cancellation tokens, and task creation options. Although it provides more flexibility, it is more complex and does not automatically unwrap async delegates. In modern .NET applications, `Task.Run()` is preferred for most scenarios, while `Task.Factory.StartNew()` is reserved for advanced task scheduling requirements.

---

# Key Points to Remember

- `Task.Factory.StartNew()` creates and starts a Task.
- Provides advanced configuration options.
- Supports custom `TaskScheduler`.
- Supports `TaskCreationOptions` such as `LongRunning`.
- Does **not** automatically unwrap async delegates.
- More complex than `Task.Run()`.
- **Task.Run()** is the recommended choice for most applications.
- Use `StartNew()` only for advanced task scheduling scenarios.

# 3.8 Task.Wait() (C# Interview Notes)

## What is Task.Wait()?

`Task.Wait()` blocks the **current thread** until the Task completes.

It is a **synchronous** way of waiting for an asynchronous operation to finish.

---

## Syntax

```csharp
task.Wait();
```

---

## Example

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        Task task = Task.Run(() =>
        {
            Thread.Sleep(3000);
            Console.WriteLine("Task Completed");
        });

        Console.WriteLine("Waiting...");

        task.Wait();

        Console.WriteLine("Main Method Completed");
    }
}
```

### Output

```text
Waiting...

(Wait 3 Seconds)

Task Completed

Main Method Completed
```

---

## How It Works

```text
Main Thread

↓

Task Starts

↓

Main Thread Calls Wait()

↓

Main Thread Blocks

↓

Task Completes

↓

Main Thread Continues
```

---

## Task.Wait() vs await

| Task.Wait() | await |
|-------------|-------|
| Blocks the thread | Doesn't block the thread |
| Synchronous | Asynchronous |
| Can cause deadlocks | Recommended |
| Not preferred in ASP.NET Core | Preferred |

---

## Advantages

- Easy to use.
- Useful in console applications.
- Waits until the Task completes.

---

## Disadvantages

- Blocks the calling thread.
- Can reduce performance.
- May cause deadlocks.
- Not recommended in ASP.NET Core.

---

## Best Practice

✅ Prefer

```csharp
await task;
```

❌ Avoid

```csharp
task.Wait();
```

especially in ASP.NET Core and UI applications.

---

## Interview Questions

### Q1. What does `Task.Wait()` do?

It blocks the current thread until the Task completes.

---

### Q2. Does `Task.Wait()` block the thread?

Yes.

---

### Q3. Which is better: `Wait()` or `await`?

`await` is better because it doesn't block the calling thread.

---

## Key Points

- `Task.Wait()` blocks the current thread.
- It waits synchronously for the Task to finish.
- Prefer `await` in modern .NET applications.
- Avoid using `Wait()` in ASP.NET Core.

# 3.9 Task.Result (C# Interview Notes)

## What is Task.Result?

`Task.Result` is a property of `Task<TResult>` that **waits for the task to complete and then returns its result**.

If the task is still running, it **blocks the current thread** until the task finishes.

---

## Syntax

```csharp
TResult value = task.Result;
```

---

## Example

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        Task<int> task = Task.Run(() =>
        {
            return 10 + 20;
        });

        int result = task.Result;

        Console.WriteLine(result);
    }
}
```

### Output

```text
30
```

---

## How It Works

```text
Task Starts

↓

Task Executes

↓

task.Result

↓

Waits if Task is Running

↓

Returns Result
```

---

## Task.Result vs Task.Wait()

| Task.Result | Task.Wait() |
|--------------|-------------|
| Waits for the task to complete | Waits for the task to complete |
| Returns the result | Does not return a result |
| Blocks the thread | Blocks the thread |

---

## Task.Result vs await

| Task.Result | await |
|--------------|-------|
| Blocks the thread | Doesn't block the thread |
| Returns the result | Returns the result |
| Not recommended in async code | Recommended |

---

## Advantages

- Simple way to get a task's result.
- Useful in console or legacy applications.

---

## Disadvantages

- Blocks the calling thread.
- Can cause deadlocks.
- Not recommended in ASP.NET Core or UI applications.

---

## Best Practice

✅ Preferred

```csharp
int result = await task;
```

❌ Avoid

```csharp
int result = task.Result;
```

unless synchronous waiting is required.

---

## Interview Questions

### Q1. What is `Task.Result`?

It waits for a `Task<TResult>` to complete and returns its result.

### Q2. Does `Task.Result` block the thread?

Yes. It blocks the calling thread until the task completes.

### Q3. What is the difference between `Task.Result` and `Task.Wait()`?

`Task.Result` waits and returns the value, while `Task.Wait()` only waits for completion.

### Q4. Which is better: `Task.Result` or `await`?

`await` is better because it doesn't block the calling thread.

---

## Key Points

- `Task.Result` is used with `Task<TResult>`.
- It waits for the task to finish and returns the result.
- It blocks the calling thread.
- Prefer `await` in modern .NET applications.
- **Remember:** `Result = Wait + Return Value`.

# 3.11 Task.WhenAll() (C# Interview Notes)

## What is Task.WhenAll()?

`Task.WhenAll()` is a static method that **asynchronously waits for multiple tasks to complete**.

It **does not start the tasks**; it only waits until **all the supplied tasks have finished**.

---

## Syntax

```csharp
await Task.WhenAll(task1, task2, task3);
```

---

## Example

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        Task task1 = Task.Delay(2000);
        Task task2 = Task.Delay(3000);
        Task task3 = Task.Delay(1000);

        Console.WriteLine("Waiting for all tasks...");

        await Task.WhenAll(task1, task2, task3);

        Console.WriteLine("All Tasks Completed");
    }
}
```

### Output

```text
Waiting for all tasks...

(All tasks run together)

All Tasks Completed
```

---

## How It Works

```text
Start Task1

Start Task2

Start Task3

↓

All Tasks Run Concurrently

↓

Wait Until ALL Complete

↓

Continue Execution
```

---

## Returning Results

If the tasks return values (`Task<TResult>`), `Task.WhenAll()` returns an array of results.

```csharp
Task<int> t1 = Task.Run(() => 10);
Task<int> t2 = Task.Run(() => 20);
Task<int> t3 = Task.Run(() => 30);

int[] results = await Task.WhenAll(t1, t2, t3);

Console.WriteLine(string.Join(", ", results));
```

### Output

```text
10, 20, 30
```

---

## Task.WhenAll() vs Multiple await

| Multiple `await` | `Task.WhenAll()` |
|------------------|------------------|
| Runs tasks one after another | Waits for all tasks together |
| Takes more time | Faster for independent tasks |
| Sequential execution | Concurrent execution |

---

## Advantages

- Waits for multiple tasks together.
- Improves performance for independent tasks.
- Supports tasks that return values.
- Works well with `async` and `await`.

---

## Disadvantages

- If one task fails, the returned task is faulted.
- Waits until all tasks finish (unless a failure/cancellation ends the combined task).

---

## Best Practice

✅ Use `Task.WhenAll()` when tasks are **independent** and can run concurrently.

Examples:

- Multiple Web API calls
- Reading multiple files
- Downloading multiple images
- Fetching data from different services

---

## Interview Questions

### Q1. What is `Task.WhenAll()`?

It asynchronously waits for multiple tasks to complete.

---

### Q2. Does `Task.WhenAll()` start the tasks?

No. It only waits for the tasks that are already started.

---

### Q3. Can `Task.WhenAll()` return values?

Yes. If the tasks are `Task<TResult>`, it returns an array of results.

---

### Q4. When should we use `Task.WhenAll()`?

When multiple tasks are independent and can run at the same time.

---

## Key Points

- `Task.WhenAll()` waits for **all** tasks to complete.
- It does **not** start the tasks.
- It is used for **concurrent** execution.
- It can return an array of results for `Task<TResult>`.
- Prefer `Task.WhenAll()` over sequential `await` calls for independent tasks.
- **Remember:** **WhenAll = Wait for ALL Tasks**.

# 3.12 Task.WhenAny() (C# Interview Notes)

## What is Task.WhenAny()?

`Task.WhenAny()` is a static method that **asynchronously waits for the first task to complete**.

It returns the **completed task**, while the remaining tasks continue running.

---

## Syntax

```csharp
Task completedTask = await Task.WhenAny(task1, task2, task3);
```

---

## Example

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        Task task1 = Task.Delay(5000);
        Task task2 = Task.Delay(2000);
        Task task3 = Task.Delay(4000);

        await Task.WhenAny(task1, task2, task3);

        Console.WriteLine("First Task Completed");
    }
}
```

### Output

```text
(After about 2 seconds)

First Task Completed
```

---

## How It Works

```text
Start Task1

Start Task2

Start Task3

↓

First Task Completes

↓

WhenAny Returns

↓

Other Tasks Continue Running
```

---

## Returning Results

```csharp
Task<int> t1 = Task.Run(async () =>
{
    await Task.Delay(3000);
    return 10;
});

Task<int> t2 = Task.Run(async () =>
{
    await Task.Delay(1000);
    return 20;
});

Task<int> completedTask = await Task.WhenAny(t1, t2);

int result = await completedTask;

Console.WriteLine(result);
```

### Output

```text
20
```

---

## Task.WhenAll() vs Task.WhenAny()

| Task.WhenAll() | Task.WhenAny() |
|----------------|----------------|
| Waits for all tasks | Waits for the first task |
| Returns when all complete | Returns when one completes |
| Used when all results are needed | Used when only the first result is needed |

---

## Advantages

- Returns the fastest completed task.
- Improves responsiveness.
- Useful for timeouts and parallel requests.
- Works with `async` and `await`.

---

## Disadvantages

- Other tasks continue running unless canceled.
- Additional logic may be needed to cancel remaining tasks.

---

## Best Practice

Use `Task.WhenAny()` when:

- Multiple servers can provide the same data.
- You need the fastest response.
- Implementing timeout scenarios.
- Racing multiple independent operations.

---

## Interview Questions

### Q1. What is `Task.WhenAny()`?

It asynchronously waits for the first task to complete and returns that completed task.

### Q2. Does `Task.WhenAny()` stop the remaining tasks?

No. The remaining tasks continue running unless they are explicitly canceled.

### Q3. What does `Task.WhenAny()` return?

It returns the task that completed first.

### Q4. When should we use `Task.WhenAny()`?

When only the first completed task or fastest response is needed.

---

## Key Points

- `Task.WhenAny()` waits for **any one** task to complete.
- It returns the completed task.
- Remaining tasks continue running.
- Ideal for timeout and fastest-response scenarios.
- **Remember:** **WhenAny = Wait for ANY One Task**.

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        CancellationTokenSource cts = new();

        Task t1 = DownloadAsync("Server A", 5000, cts.Token);
        Task t2 = DownloadAsync("Server B", 2000, cts.Token);
        Task t3 = DownloadAsync("Server C", 4000, cts.Token);

        Task completedTask = await Task.WhenAny(t1, t2, t3);

        Console.WriteLine("First task finished.");

        // Cancel the remaining tasks
        cts.Cancel();
    }

    static async Task DownloadAsync(string server, int delay, CancellationToken token)
    {
        try
        {
            Console.WriteLine($"{server} started");

            await Task.Delay(delay, token);

            Console.WriteLine($"{server} completed");
        }
        catch (OperationCanceledException)
        {
            Console.WriteLine($"{server} cancelled");
        }
    }
}
```

# 3.13 Continuations (C# Interview Notes)

## What are Continuations?

A **Continuation** is a task that **automatically starts after another task completes**.

Continuations are created using the **`ContinueWith()`** method.

---

## Syntax

```csharp
task.ContinueWith(t =>
{
    // Code to execute after the first task completes
});
```

---

## Example

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        Task task = Task.Run(() =>
        {
            Console.WriteLine("Downloading File...");
        });

        task.ContinueWith(t =>
        {
            Console.WriteLine("Processing File...");
        }).Wait();
    }
}
```

### Output

```text
Downloading File...

Processing File...
```

---

## How It Works

```text
Task 1 Starts

↓

Task 1 Completes

↓

Continuation Starts

↓

Continuation Completes
```

---

## Accessing the Previous Task's Result

```csharp
Task<int> task = Task.Run(() => 100);

task.ContinueWith(t =>
{
    Console.WriteLine(t.Result);
}).Wait();
```

### Output

```text
100
```

---

## ContinueWith() vs await

| ContinueWith() | await |
|----------------|-------|
| Uses continuation tasks | Uses async/await |
| More complex | Simpler and more readable |
| Used in advanced or legacy scenarios | Recommended for modern .NET applications |

---

## Advantages

- Executes code automatically after a task completes.
- Useful for chaining tasks.
- Can access the previous task's result.

---

## Disadvantages

- Less readable than `async`/`await`.
- More complex to write and maintain.
- Rarely used in modern applications.

---

## Best Practice

✅ Prefer `async`/`await` for most applications.

Use `ContinueWith()` only for advanced task scheduling or legacy code.

---

## Interview Questions

### Q1. What is a Continuation?

A continuation is a task that starts automatically after another task completes.

### Q2. Which method is used to create a continuation?

`ContinueWith()`.

### Q3. Can a continuation access the previous task's result?

Yes, using `t.Result`.

### Q4. Which is preferred: `ContinueWith()` or `await`?

`await` is preferred because it is simpler, more readable, and easier to maintain.

---

## Key Points

- A continuation runs **after** another task completes.
- Created using **`ContinueWith()`**.
- Can access the previous task's result using `t.Result`.
- Mostly replaced by **`async`/`await`** in modern .NET applications.
- **Remember:** **Continuation = "Run the next task after the current task finishes."**

# 3.14 Fire-and-Forget (C# Interview Notes)

## What is Fire-and-Forget?

**Fire-and-Forget** is a programming technique where a task is **started but not awaited**.

The calling code continues execution immediately while the task runs in the background.

---

## Syntax

```csharp
_ = SendEmailAsync();
```

or

```csharp
Task.Run(() => SendEmail());
```

---

## Example

```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        _ = SendEmailAsync();

        Console.WriteLine("Request Completed");

        await Task.Delay(3000); // Keep the application alive
    }

    static async Task SendEmailAsync()
    {
        await Task.Delay(2000);

        Console.WriteLine("Email Sent");
    }
}
```

### Output

```text
Request Completed

(After 2 seconds)

Email Sent
```

---

## How It Works

```text
Start Background Task

↓

Continue Execution Immediately

↓

Background Task Completes Later
```

---

## When to Use

Use Fire-and-Forget for **non-critical background tasks**, such as:

- Sending emails
- Logging
- Analytics
- Notifications
- Cache refresh

---

## When NOT to Use

Avoid Fire-and-Forget for:

- Database transactions
- Payment processing
- Critical business operations
- Tasks that must complete before returning a response

---

## Fire-and-Forget in ASP.NET Core

❌ Avoid starting Fire-and-Forget tasks that use **scoped services** (like `DbContext`) directly from a controller.

Instead, use:

- `BackgroundService`
- `IHostedService`
- Message queues (RabbitMQ, Azure Service Bus, etc.)

---

## Advantages

- Doesn't block the calling thread.
- Improves response time.
- Suitable for background work.

---

## Disadvantages

- Exceptions may go unobserved if not handled.
- No guarantee that the task completes successfully.
- Can cause issues with scoped services in ASP.NET Core.

---

## Best Practice

- Use Fire-and-Forget only for **non-critical** operations.
- Handle exceptions inside the background task.
- Prefer `BackgroundService` or a queue for long-running background work in ASP.NET Core.

---

## Interview Questions

### Q1. What is Fire-and-Forget?

It is a technique where a task is started without waiting (`await`) for it to complete.

---

### Q2. Does Fire-and-Forget wait for the task to finish?

No. The caller continues execution immediately.

---

### Q3. When should Fire-and-Forget be used?

For non-critical background operations like logging, notifications, and analytics.

---

### Q4. Why is Fire-and-Forget risky in ASP.NET Core?

Because the HTTP request may end, causing scoped services (such as `DbContext`) used by the background task to be disposed before the task finishes.

---

## Key Points

- Fire-and-Forget = **Start the task and don't wait for it.**
- The task runs in the background.
- Use only for **non-critical** work.
- Handle exceptions inside the background task.
- Avoid using it directly with scoped services in ASP.NET Core.
- **Remember:** **Fire-and-Forget = Fire → Continue → Task Finishes Later.**

# 3.15 Task Best Practices (C# Interview Notes)

## What are Task Best Practices?

Task Best Practices are recommended guidelines for writing **efficient, scalable, and maintainable asynchronous code** in .NET.

Following these practices helps avoid deadlocks, improve performance, and make applications more responsive.

---

## Best Practices

### 1. Prefer `async`/`await`

✅ Recommended

```csharp
await GetDataAsync();
```

❌ Avoid

```csharp
GetDataAsync().Wait();
```

---

### 2. Avoid `Task.Wait()` and `Task.Result`

These methods block the calling thread and can cause deadlocks.

✅ Recommended

```csharp
int result = await GetValueAsync();
```

❌ Avoid

```csharp
int result = GetValueAsync().Result;
```

---

### 3. Use `Task.WhenAll()` for Independent Tasks

Instead of

```csharp
await Task1();
await Task2();
await Task3();
```

Use

```csharp
Task t1 = Task1();
Task t2 = Task2();
Task t3 = Task3();

await Task.WhenAll(t1, t2, t3);
```

This improves performance by running tasks concurrently.

---

### 4. Use `Task.Run()` Only for CPU-Bound Work

✅ Good

- Image processing
- Report generation
- Complex calculations

❌ Avoid using `Task.Run()` for:

- Database operations
- File I/O
- Web API calls

Use asynchronous APIs like:

```csharp
await dbContext.SaveChangesAsync();
```

---

### 5. Use `Task.FromResult()` for Existing Values

If the result is already available,

```csharp
return Task.FromResult(employee);
```

Don't use

```csharp
return Task.Run(() => employee);
```

---

### 6. Handle Exceptions

Always handle exceptions when using tasks.

```csharp
try
{
    await ProcessAsync();
}
catch (Exception ex)
{
    Console.WriteLine(ex.Message);
}
```

---

### 7. Avoid Unnecessary Fire-and-Forget

Fire-and-Forget tasks may hide exceptions and fail silently.

For long-running background work in ASP.NET Core, prefer:

- `BackgroundService`
- `IHostedService`
- Message Queues

---

### 8. Support Cancellation

Use `CancellationToken` for long-running tasks.

```csharp
await Task.Delay(5000, cancellationToken);
```

This allows tasks to stop gracefully when cancellation is requested.

---

## Advantages

- Improves application performance.
- Prevents deadlocks.
- Makes applications more responsive.
- Increases scalability.
- Produces cleaner and more maintainable code.

---

## Common Mistakes

- Using `Task.Result` instead of `await`.
- Using `Task.Wait()` in ASP.NET Core.
- Wrapping async methods inside `Task.Run()`.
- Ignoring exceptions in Fire-and-Forget tasks.
- Running independent tasks sequentially instead of using `Task.WhenAll()`.

---

## Interview Questions

### Q1. What is the recommended way to wait for a task?

Use `await`.

---

### Q2. Why should we avoid `Task.Result` and `Task.Wait()`?

Because they block the calling thread and may cause deadlocks.

---

### Q3. When should `Task.Run()` be used?

For CPU-bound operations.

---

### Q4. When should `Task.WhenAll()` be used?

When multiple independent tasks can run concurrently.

---

### Q5. Why should we use `CancellationToken`?

To allow long-running tasks to be canceled gracefully.

---

## Key Points

- ✅ Prefer **`async`/`await`**.
- ✅ Use **`Task.WhenAll()`** for independent tasks.
- ✅ Use **`Task.Run()`** only for CPU-bound work.
- ✅ Use **`Task.FromResult()`** for existing values.
- ✅ Handle exceptions properly.
- ✅ Support task cancellation using `CancellationToken`.
- ❌ Avoid `Task.Wait()` and `Task.Result` in modern .NET applications.
- ❌ Avoid unnecessary Fire-and-Forget tasks.