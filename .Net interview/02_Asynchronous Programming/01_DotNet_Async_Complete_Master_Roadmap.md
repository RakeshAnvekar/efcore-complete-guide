# .NET Asynchronous Programming -- Complete Interview Master Roadmap

> Author: ChatGPT Purpose: Master every async concept from beginner to
> Senior/Lead level.

------------------------------------------------------------------------

# Module 1 -- Why Asynchronous Programming Exists - completed

## 1.1 What is Synchronous Programming? - completed

## 1.2 What is Asynchronous Programming? - completed 

## 1.3 Blocking vs Non-Blocking - completed

## 1.4 Sequential vs Concurrent Execution - completed

## 1.5 CPU-bound Work - completed

## 1.6 I/O-bound Work - completed   

## 1.7 Why Thread.Sleep() is Bad - completed

## 1.8 Why Task.Delay() is Better - completed

## 1.9 Why async/await was Introduced - completed

## 1.10 How Operating Systems Perform Asynchronous I/O

### Interview Questions - completed

-   What problem does async solve?
-   Why not create a thread for every request?
-   Concurrency vs Parallelism?

------------------------------------------------------------------------

# Module 2 -- Thread Fundamentals

## 2.1 Process - completed

## 2.2 Thread  - completed

## 2.3 Managed Threads -completed

## 2.4 CLR Thread Pool   -completed

## 2.5 Worker Threads  -completed

## 2.6 IO Completion Port Threads (IOCP)  -completed


## 2.7 Thread Scheduling - Completed

## 2.8 Context Switching - Completed

## 2.9 Thread Pool Starvation - Completed

## 2.10 Thread vs Task  - Completed


### Interview Questions

-   Does async create a new thread?
-   How many threads does ASP.NET Core use?

------------------------------------------------------------------------

# Module 3 -- Task 

## 3.1 What is Task? - Completed

## 3.2 Task Lifecycle - Completed

## 3.3 Task`<TResult>`{=html} - Completed

## 3.4 Task.CompletedTask  - Completed

## 3.5 Task.FromResult() - Completed

## 3.6 Task.Run() - Completed

## 3.7 Task.Factory.StartNew() - Completed

## 3.8 Task.Wait() - Completed

## 3.9 Task.Result - Completed

## 3.10 Task.Status - Completed

## 3.11 Task.WhenAll() - Completed

## 3.12 Task.WhenAny() - Completed

## 3.13 Continuations - Completed
 
## 3.14 Fire-and-Forget - Completed

## 3.15 Task Best Practices - Completed

------------------------------------------------------------------------

# Module 4 -- async Keyword

## 4.1 What async Really Does

## 4.2 Compiler Transformation

## 4.3 async Methods

## 4.4 async without await

## 4.5 async Task

## 4.6 async Task`<T>`{=html}

## 4.7 async void

## 4.8 State Machine Overview

------------------------------------------------------------------------

# Module 5 -- await Keyword

## 5.1 Await Flow

## 5.2 Awaitable Pattern

## 5.3 Continuations

## 5.4 Suspension

## 5.5 Resumption

## 5.6 Await Execution Flow

## 5.7 Await vs Wait()

------------------------------------------------------------------------

# Module 6 -- Compiler Generated State Machine

## 6.1 IAsyncStateMachine

## 6.2 MoveNext()

## 6.3 AsyncMethodBuilder

## 6.4 AwaitUnsafeOnCompleted()

## 6.5 Generated IL (High Level)

## 6.6 Execution Flow

------------------------------------------------------------------------

# Module 7 -- SynchronizationContext

## 7.1 What is SynchronizationContext?

## 7.2 UI Context

## 7.3 WPF Dispatcher

## 7.4 WinForms Context

## 7.5 ASP.NET Framework Context

## 7.6 ASP.NET Core (No SynchronizationContext)

## 7.7 Continuation Context

## 7.8 Deadlocks

------------------------------------------------------------------------

# Module 8 -- ConfigureAwait(false)

## 8.1 Context Capture

## 8.2 ConfigureAwait(false)

## 8.3 Performance Benefits

## 8.4 Deadlock Prevention

## 8.5 Library Guidelines

## 8.6 ASP.NET Core Considerations

------------------------------------------------------------------------

# Module 9 -- Deadlocks

## 9.1 Why Deadlocks Happen

## 9.2 Result

## 9.3 Wait()

## 9.4 GetAwaiter().GetResult()

## 9.5 Context Deadlocks

## 9.6 Prevention Techniques

------------------------------------------------------------------------

# Module 10 -- ValueTask

## 10.1 Why ValueTask Exists

## 10.2 Allocation Reduction

## 10.3 Performance

## 10.4 Restrictions

## 10.5 Multiple Await Issue

## 10.6 Task vs ValueTask

## 10.7 When to Use

## 10.8 When Not to Use

------------------------------------------------------------------------

# Module 11 -- CancellationToken

## 11.1 Cooperative Cancellation

## 11.2 CancellationToken

## 11.3 CancellationTokenSource

## 11.4 Cancel()

## 11.5 CancelAfter()

## 11.6 LinkedTokenSource

## 11.7 ThrowIfCancellationRequested()

## 11.8 Timeouts

## 11.9 Passing Tokens

------------------------------------------------------------------------

# Module 12 -- IAsyncEnumerable

## 12.1 Async Streams

## 12.2 IAsyncEnumerable`<T>`{=html}

## 12.3 await foreach

## 12.4 yield return

## 12.5 yield break

## 12.6 Cancellation

## 12.7 Streaming Large Data

------------------------------------------------------------------------

# Module 13 -- Parallel Programming

## 13.1 Parallel.For

## 13.2 Parallel.ForEach

## 13.3 PLINQ

## 13.4 MaxDegreeOfParallelism

## 13.5 Task.Run

## 13.6 Async vs Parallel

------------------------------------------------------------------------

# Module 14 -- Exception Handling

## 14.1 Exceptions in async

## 14.2 try/catch

## 14.3 AggregateException

## 14.4 Task.WhenAll Exceptions

## 14.5 UnobservedTaskException

------------------------------------------------------------------------

# Module 15 -- Task Combinators

## 15.1 Task.WhenAll

## 15.2 Task.WhenAny

## 15.3 ContinueWith

## 15.4 Retry Pattern

## 15.5 Timeout Pattern

## 15.6 Race Conditions

------------------------------------------------------------------------

# Module 16 -- Async Best Practices

## 16.1 Avoid async void

## 16.2 Avoid Wait()

## 16.3 Avoid Result

## 16.4 Don't Block Async Code

## 16.5 Always Pass CancellationToken

## 16.6 ConfigureAwait in Libraries

## 16.7 Don't Ignore Tasks

## 16.8 Avoid Unnecessary Task.Run

------------------------------------------------------------------------

# Module 17 -- ASP.NET Core Async Pipeline

## 17.1 Kestrel

## 17.2 Middleware

## 17.3 Controllers

## 17.4 Services

## 17.5 Repository Layer

## 17.6 EF Core Async APIs

## 17.7 Database I/O

## 17.8 Response Pipeline

------------------------------------------------------------------------

# Module 18 -- Performance

## 18.1 Allocations

## 18.2 Thread Pool Starvation

## 18.3 Context Switching

## 18.4 ExecutionContext

## 18.5 Benchmarking

## 18.6 Memory Optimizations

------------------------------------------------------------------------

# Module 19 -- Advanced Topics

## 19.1 ExecutionContext

## 19.2 AsyncLocal

## 19.3 TaskScheduler

## 19.4 Custom Awaiters

## 19.5 Channels

## 19.6 Pipelines

## 19.7 SemaphoreSlim

## 19.8 ReaderWriterLockSlim

## 19.9 PeriodicTimer

## 19.10 IAsyncDisposable

## 19.11 await using

------------------------------------------------------------------------

# Module 20 -- CLR Internals

## 20.1 Compiler

## 20.2 State Machine

## 20.3 AsyncMethodBuilder

## 20.4 Thread Pool

## 20.5 IO Completion Ports

## 20.6 Continuations

## 20.7 End-to-End Request Flow

------------------------------------------------------------------------

# Interview Comparison Topics

-   async vs await
-   Task vs Thread
-   Task vs ValueTask
-   Thread.Sleep vs Task.Delay
-   Wait vs await
-   Result vs await
-   ConfigureAwait(true) vs ConfigureAwait(false)
-   SynchronizationContext vs ExecutionContext
-   CancellationToken vs CancellationTokenSource
-   IEnumerable`<T>`{=html} vs IAsyncEnumerable`<T>`{=html}
-   Task.WhenAll vs Task.WhenAny
-   async void vs async Task
-   Parallel.ForEach vs Task.WhenAll
-   IDisposable vs IAsyncDisposable

------------------------------------------------------------------------

# Recommended Learning Order

1.  Module 1
2.  Module 2
3.  Module 3
4.  Module 4
5.  Module 5
6.  Module 6
7.  Module 7
8.  Module 8
9.  Module 9
10. Module 10
11. Module 11
12. Module 12
13. Module 13
14. Module 14
15. Module 15
16. Module 16
17. Module 17
18. Module 18
19. Module 19
20. Module 20
