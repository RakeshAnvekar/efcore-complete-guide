# Module 1 -- Why Asynchronous Programming Exists (Detailed Notes)

> These notes capture the detailed explanations covered during our
> lessons for:
>
> -   1.1 What is Synchronous Programming?
> -   1.2 What is Asynchronous Programming?
> -   1.3 Blocking vs Non-Blocking
> -   1.4 Sequential vs Concurrent Execution

------------------------------------------------------------------------

# Module Goal

Understand **why asynchronous programming exists before learning `Task`,
`async`, and `await`.**

The biggest idea to remember:

> Async was introduced **to avoid wasting threads while waiting for slow
> I/O operations.**

It was **not** introduced to make the CPU, SQL Server, or network
faster.

------------------------------------------------------------------------

# 1.1 What is Synchronous Programming?

## Definition

Synchronous programming is an execution model where **each operation
must finish before the next operation begins**. The current thread waits
(blocks) until the current operation completes.

## Real-Life Example -- Washing Clothes

``` text
Wash Clothes
    ↓
Dry Clothes
    ↓
Iron Clothes
    ↓
Fold Clothes
```

Every step depends on the previous step.

## Restaurant Example

``` text
Take Order
    ↓
Go to Kitchen
    ↓
Wait...
    ↓
Wait...
    ↓
Food Ready
    ↓
Serve Customer
```

The waiter is doing nothing useful while waiting.

## Programming Example

``` csharp
Console.WriteLine("Start");

ReadDatabase();

Console.WriteLine("End");
```

Execution:

``` text
Start
 ↓
ReadDatabase()
 ↓
Wait...
 ↓
End
```

## Thread View

``` text
Thread
  ↓
Execute Code
  ↓
Call Database
  ↓
WAIT
  ↓
WAIT
  ↓
Continue
```

The thread cannot execute any other work.

## Call Stack View

``` text
Main()
  ↓
GetEmployee()
  ↓
ReadDatabase()
```

Nothing is removed from the stack until `ReadDatabase()` returns.

## CPU Perspective

Suppose a database call takes **300 ms**.

The CPU is **not calculating** for 300 ms.

Instead:

``` text
CPU
 ↓
Send SQL Request
 ↓
Waiting
 ↓
Receive Result
 ↓
Continue
```

The thread is blocked while waiting for the database.

## ASP.NET Core Example

1000 requests:

``` text
1000 Requests
      ↓
1000 Threads
      ↓
Most Threads Waiting
```

This wastes memory and reduces scalability.

## Advantages

-   Easy to understand
-   Predictable
-   Easy to debug
-   Great for short CPU-bound work

## Disadvantages

-   Blocks threads
-   Wastes resources during I/O
-   Poor scalability

## Common Interview Mistakes

❌ Synchronous means single-threaded.

No. A process can have many threads. Each thread executes synchronously.

❌ Synchronous is always slow.

No. Small CPU-bound work is often faster synchronously.

------------------------------------------------------------------------

# 1.2 What is Asynchronous Programming?

## Definition

Asynchronous programming starts an operation **without blocking the
current thread**. The thread is free to do other work while the
operation is in progress. When it completes, execution resumes.

## Biggest Misconception

❌ async creates a new thread.

Correct understanding:

``` text
Start I/O
   ↓
Release Thread
   ↓
I/O Continues
   ↓
Continuation Executes
```

## Restaurant Example

``` text
Take Order
    ↓
Kitchen Starts Cooking
    ↓
Serve Other Customers
    ↓
Kitchen Calls
    ↓
Serve Food
```

The cooking time didn't change. Resource utilization improved.

## Example

``` csharp
await ReadDatabaseAsync();
```

Execution

``` text
Start Database
      ↓
Release Thread
      ↓
Database Working
      ↓
Continuation
      ↓
Resume
```

## Benefits

-   Better scalability
-   Better responsiveness
-   Better thread utilization

## Important Facts

-   Async is mainly for I/O-bound work.
-   Async does not make SQL Server faster.
-   Async does not automatically create threads.

------------------------------------------------------------------------

# 1.3 Blocking vs Non-Blocking

## Blocking

A thread waits until an operation completes.

Example:

``` csharp
Thread.Sleep(5000);
```

The thread is occupied for five seconds.

## Non-Blocking

The thread starts an operation and becomes available for other work.

Example:

``` csharp
await Task.Delay(5000);
```

The thread is released while waiting.

## Thread Timeline

Blocking

``` text
Working
██████
Waiting
████████████
Continue
████
```

Non-Blocking

``` text
Working
██████
FREE
............
Resume
████
```

## Key Points

-   Blocking blocks the thread.
-   Non-blocking frees the thread.
-   Non-blocking does not make operations faster.
-   It improves scalability.

## Blocking APIs

-   Thread.Sleep()
-   Task.Wait()
-   Task.Result
-   File.ReadAllText()

## Non-Blocking APIs

-   Task.Delay()
-   await
-   HttpClient.GetAsync()
-   File.ReadAllTextAsync()

------------------------------------------------------------------------

# 1.4 Sequential vs Concurrent Execution

## Sequential

Each operation starts after the previous one finishes.

``` csharp
await A();
await B();
await C();
```

Timeline

``` text
A
 ↓
B
 ↓
C
```

## Concurrent

Multiple operations make progress during the same period.

``` csharp
Task t1 = A();
Task t2 = B();
Task t3 = C();

await Task.WhenAll(t1, t2, t3);
```

Timeline

``` text
Task A
██████....████

Task B
   █████████

Task C
      ███████
```

## Important Facts

-   Sequential is about execution order.
-   Concurrent is about progress.
-   Concurrent is not Parallel.
-   Async is not automatically concurrent.

## Comparison

  Sequential               Concurrent
  ------------------------ ----------------------------------------
  One task after another   Multiple tasks progress together
  Easy                     More coordination
  Predictable              Better throughput for independent work

## Interview Traps

`await A(); await B();`

✔ Sequential asynchronous

`Task t1=A(); Task t2=B(); await Task.WhenAll(t1,t2);`

✔ Concurrent asynchronous

------------------------------------------------------------------------

# Module 1 Summary

After this module you should understand:

-   Why asynchronous programming exists
-   Synchronous programming
-   Asynchronous programming
-   Blocking vs Non-blocking
-   Sequential vs Concurrent
-   Why async improves scalability
-   Why async is mainly for I/O-bound work
-   Why threads are valuable resources

# Next Module

**1.5 CPU-bound Work**
