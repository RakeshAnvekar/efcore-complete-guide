# Module 2.1 -- Process
------------------------------------------------------------------------

# Learning Objectives

After completing this chapter, you should be able to answer:

-   What is a Process?
-   Program vs Process
-   Why do Processes exist?
-   Who creates a Process?
-   What happens when you launch a .NET application?
-   What is Process Memory Layout?
-   Process vs Thread
-   Interview Questions

------------------------------------------------------------------------

# What is a Program?

A **Program** is a file stored on disk.

Examples:

-   `MyApp.exe`
-   `chrome.exe`
-   `notepad.exe`

A program is **passive**. It is **not executing**.

------------------------------------------------------------------------

# What is a Process?

## Definition

> **A Process is a running instance of a program.**

When Windows loads a program into memory and starts executing it, the
program becomes a **Process**.

### Process Flow

``` text
Program (.exe)
      │
      ▼
Windows Starts It
      │
      ▼
Running Process
```

------------------------------------------------------------------------

# Program vs Process

  Program          Process
  ---------------- -------------------------------
  Stored on disk   Loaded into memory
  Passive          Active
  Not executing    Executing
  `.exe` file      Running instance of a program

------------------------------------------------------------------------

# Why Do We Need Processes?

Processes provide:

-   Memory Isolation
-   Resource Isolation
-   Security
-   Stability

Without processes, one application could overwrite another application's
memory.

------------------------------------------------------------------------

# Who Creates a Process?

The **Operating System (Windows)** creates every process.

> The CLR does **NOT** create the process.

------------------------------------------------------------------------

# Process Creation Flow

``` text
Double Click MyApp.exe
        │
        ▼
Windows Loader
        │
        ▼
Create Process
        │
        ▼
Allocate Virtual Address Space
        │
        ▼
Load EXE
        │
        ▼
Load Required DLLs
        │
        ▼
Load CLR
        │
        ▼
Create Main Thread
        │
        ▼
Execute Main()
```

------------------------------------------------------------------------

# What is Inside a Process?

-   Executable Code
-   Loaded DLLs
-   Virtual Address Space
-   Managed Heap
-   Native Heap
-   Threads
-   Thread Stacks
-   Handles
-   Process ID (PID)

------------------------------------------------------------------------

# Process ID (PID)

Every running process receives a unique Process ID assigned by Windows.

------------------------------------------------------------------------

# Heap

Reference-type objects are typically allocated on the Managed Heap.

``` csharp
Employee emp = new Employee();
```

------------------------------------------------------------------------

# Stack

Each thread owns its own stack.

``` csharp
void Test()
{
    int x = 10;
}
```

The local variable `x` is normally stored on that thread's stack.

------------------------------------------------------------------------

# Simplified Process Memory Layout

``` text
+--------------------------------------+
| Executable Code (.exe)               |
+--------------------------------------+
| Loaded DLLs                          |
+--------------------------------------+
| Managed Heap                         |
+--------------------------------------+
| Native Heap                          |
+--------------------------------------+
| Main Thread Stack                    |
+--------------------------------------+
| Worker Thread Stack                  |
+--------------------------------------+
```

------------------------------------------------------------------------

# Process vs Thread

  Process                         Thread
  ------------------------------- ----------------------------
  Running instance of a program   Smallest unit of execution
  Own virtual memory              Shares process memory
  Heavyweight                     Lightweight
  Contains one or more threads    Belongs to one process

------------------------------------------------------------------------

# ASP.NET Core Example

``` text
dotnet MyApi.dll
      │
      ▼
Windows Creates Process
      │
      ▼
Loads CLR
      │
      ▼
Creates Main Thread
      │
      ▼
Starts Kestrel
      │
      ▼
Accepts HTTP Requests
```

------------------------------------------------------------------------

# Interview Questions

## What is a Process?

A Process is a running instance of a program with its own virtual
memory, resources, and at least one thread.

## Who creates a Process?

The Operating System.

## Can a Process exist without a Thread?

No.

## Can one Process have multiple Threads?

Yes.

## Can two Processes share memory?

Not by default.

------------------------------------------------------------------------

# Common Mistakes

-   ❌ Program and Process are the same.

-   ✅ A Program is a file; a Process is its running instance.

-   ❌ The CLR creates the Process.

-   ✅ Windows creates the Process.

-   ❌ Every Process always has only one Thread.

-   ✅ A Process starts with one thread but can create many more.

------------------------------------------------------------------------

# Summary

A Process is the execution container for an application. It provides
memory, isolation, resources, and one or more threads.

------------------------------------------------------------------------

# Key Takeaways

-   Program = File on disk
-   Process = Running instance
-   Windows creates the Process
-   Every Process has its own virtual memory
-   Every Process starts with at least one thread
-   A Process can contain many threads

------------------------------------------------------------------------

# Module 2.3 – Managed Threads

> **Author:** ChatGPT
>
> **Purpose:** Senior/Lead .NET Interview Preparation

---

# Learning Objectives

After completing this chapter, you should be able to answer:

- What is a Managed Thread?
- Why do we need Managed Threads?
- Native Thread vs Managed Thread
- Who creates Managed Threads?
- What is `System.Threading.Thread`?
- How does the CLR manage Threads?
- What happens internally when `new Thread()` is called?
- Managed Thread Architecture
- Interview Questions

---

# Table of Contents

- Introduction
- Native Threads
- Why Managed Threads?
- What is a Managed Thread?
- Managed Thread Architecture
- Native Thread vs Managed Thread
- How CLR Creates Managed Threads
- `System.Threading.Thread`
- Internal Working of `new Thread()`
- Code Example
- Summary
- Key Takeaways

---

# Introduction

Before learning **Managed Threads**, let's recall what we already know.

```
Program

↓

Process

↓

Thread

↓

CPU Executes Instructions
```

Question:

**Who created the Thread?**

Answer:

**Windows (Operating System)**

Now another question arises.

How does **.NET** know about this Windows thread?

How can C# execute code using a Windows thread?

The answer is:

**Managed Threads**

---

# Native Threads

The Windows Operating System understands only one type of thread.

```
Native Thread
```

Windows has no knowledge of:

- C#
- .NET
- CLR
- Task
- async
- await

It only understands **Native Threads**.

Execution

```
Windows

↓

Native Thread

↓

CPU
```

---

# The Problem

Imagine you write

```csharp
Console.WriteLine("Hello");
```

Question

Who executes this code?

Windows?

No.

CLR?

Yes.

But the CLR cannot execute instructions directly on the CPU.

The CLR must use a Windows thread.

---

# Microsoft's Solution

Microsoft introduced the concept of a **Managed Thread**.

Think of the architecture like this.

```
Your C# Code

↓

CLR

↓

Managed Thread

↓

Native Thread

↓

CPU
```

The Managed Thread acts as the CLR's representation of an operating system thread.

---

# What is a Managed Thread?

## Definition

> A **Managed Thread** is a thread represented and managed by the Common Language Runtime (CLR).

The actual execution still happens on the underlying operating system thread.

The CLR adds runtime services on top of that thread.

---

# Real-Life Analogy

Imagine a car.

The engine moves the car.

Now imagine adding

- Dashboard
- GPS
- Airbags
- Cruise Control

The engine still performs the actual work.

The extra features make the experience easier and safer.

Similarly

```
Native Thread

↓

Managed Thread
```

The Native Thread executes instructions.

The Managed Thread provides additional runtime capabilities.

---

# Managed Thread Architecture

```
+-----------------------------------+
|          Your C# Code             |
+-----------------------------------+
                │
                ▼
+-----------------------------------+
|               CLR                 |
+-----------------------------------+
                │
                ▼
+-----------------------------------+
|         Managed Thread            |
+-----------------------------------+
                │
                ▼
+-----------------------------------+
|         Native Thread             |
+-----------------------------------+
                │
                ▼
+-----------------------------------+
|              CPU                  |
+-----------------------------------+
```

---

# Native Thread vs Managed Thread

| Native Thread | Managed Thread |
|---------------|----------------|
| Created by Windows | Managed by CLR |
| Executes machine instructions | Represents the OS thread in .NET |
| Knows nothing about C# | Provides .NET runtime services |
| Scheduled by Windows | Uses Windows scheduling |

---

# Why Do We Need Managed Threads?

Without Managed Threads, the CLR would have to interact directly with low-level Windows APIs every time.

Managed Threads provide:

- Runtime metadata
- Exception handling
- Thread Local Storage (TLS)
- Debugging support
- Culture information
- Runtime security
- Integration with Garbage Collection

This allows .NET developers to work with threads using simple C# APIs.

---

# Who Creates the Managed Thread?

Suppose you start a .NET application.

Execution Flow

```
Double Click

↓

Windows Creates Process

↓

Windows Creates Native Thread

↓

Load CLR

↓

CLR Creates Managed Thread Object

↓

Execute Main()
```

Notice

- Windows creates the actual thread.
- CLR creates the **Managed Thread object** that represents it.

---

# What Does the CLR Manage?

The CLR adds many services to a Managed Thread.

```
Managed Thread

├── Exception Handling
├── Garbage Collection Coordination
├── Security Context
├── Thread Local Storage
├── Culture Information
├── Debugging Support
├── Runtime Metadata
└── Managed Thread ID
```

Windows does not provide these .NET-specific services.

---

# System.Threading.Thread

The `System.Threading.Thread` class is the .NET API used to create and manage threads.

Example

```csharp
using System.Threading;

Thread thread = new Thread(DoWork);

thread.Start();
```

The `Thread` object you create is a **Managed Thread**.

The CLR then communicates with Windows to create the underlying Native Thread.

---

# Internal Working of new Thread()

Suppose you write

```csharp
Thread thread = new Thread(DoWork);

thread.Start();
```

Internal Flow

```
Your Application

↓

System.Threading.Thread

↓

CLR

↓

Windows CreateThread()

↓

Native Thread Created

↓

CPU Executes DoWork()
```

Important:

`new Thread()` **does not start** the thread.

The thread begins executing only after

```csharp
thread.Start();
```

is called.

---

# Code Example

```csharp
using System;
using System.Threading;

class Program
{
    static void Main()
    {
        Thread worker = new Thread(DoWork);

        worker.Start();

        Console.WriteLine("Main Thread");

        worker.Join();
    }

    static void DoWork()
    {
        Console.WriteLine("Worker Thread");
    }
}
```

Output (order may vary)

```
Main Thread
Worker Thread
```

or

```
Worker Thread
Main Thread
```

because both threads execute concurrently.

---

# Summary (Part 1)

Until now, we've learned:

- Windows understands only Native Threads.
- The CLR represents them as Managed Threads.
- `System.Threading.Thread` is the .NET class for creating Managed Threads.
- The CLR adds many runtime services on top of the underlying OS thread.


