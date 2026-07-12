# .NET CLR & JIT Interview Notes

> A concise interview revision guide for CLR and JIT.

---

# What is CLR?

The **Common Language Runtime (CLR)** is the execution engine of .NET. It provides the runtime environment for managed code.

## Responsibilities

- Execute managed code
- Load assemblies
- JIT compilation
- Garbage Collection (GC)
- Memory management
- Exception handling
- Thread management
- Security
- Type safety
- Debugging support
- Interoperability (P/Invoke, COM)

### Interview Answer

> CLR is the execution engine of .NET. It loads assemblies, verifies IL, compiles IL to native machine code using JIT, manages memory through the Garbage Collector, handles exceptions, manages threads, enforces type safety, and provides runtime services.

---

# Managed vs Unmanaged Code

## Managed Code

- Runs under CLR
- Memory managed by GC
- Type safe
- Automatic exception handling

Example

```csharp
Console.WriteLine("Hello");
```

## Unmanaged Code

- Runs directly on the operating system
- Manual memory management
- Examples: C, C++, Win32 API

---

# Complete Compilation Flow

```text
Write C#
      │
      ▼
Roslyn Compiler
      │
      ▼
IL + Metadata + Manifest
      │
      ▼
Assembly (.dll/.exe)
      │
      ▼
CLR Loads Assembly
      │
      ▼
JIT Compiler
      │
      ▼
Native Machine Code
      │
      ▼
CPU Executes
```

---

# Roslyn Compiler

Roslyn is the C# compiler.

Responsibilities:

- Parse source code
- Syntax analysis
- Semantic analysis
- Generate IL
- Generate Metadata
- Produce Assembly

---

# Intermediate Language (IL)

Also known as:

- IL
- MSIL
- CIL

Characteristics:

- CPU independent
- Platform independent
- Stack-based instruction set
- Converted to native code by JIT

Example

```csharp
int x = 10;
int y = 20;
Console.WriteLine(x + y);
```

Approximate IL

```text
ldc.i4.s 10
ldc.i4.s 20
add
call Console.WriteLine
```

---

# Why IL?

```text
C#
 ↓
IL
 ↓
JIT
 ↓
Intel / AMD / ARM
```

Benefits

- Platform independence
- Runtime optimization
- Security verification
- Language interoperability

---

# Assembly

A deployment unit in .NET.

Extensions

- .dll
- .exe

Contains

- IL
- Metadata
- Manifest
- Resources

---

# Manifest

Stores

- Assembly Name
- Version
- Culture
- Public Key
- Referenced Assemblies
- Entry Point

CLR reads the manifest first.

---

# Metadata

Metadata describes everything inside an assembly.

Contains

- Classes
- Methods
- Properties
- Fields
- Interfaces
- Parameters
- Attributes
- Generic types

Reflection works because of metadata.

---

# Reflection

Reflection inspects assemblies at runtime.

```csharp
typeof(Employee).GetMethods();
```

Uses

- Dependency Injection
- ORMs
- Serialization
- Plugin systems
- Testing frameworks

---

# Assembly Loading

```text
Read Manifest
      ↓
Read Metadata
      ↓
Resolve Dependencies
      ↓
Load Types
      ↓
Verify IL
```

---

# Class Loader

Loads types only when required (lazy loading).

---

# IL Verification

CLR verifies:

- Type safety
- Stack correctness
- Method signatures
- Valid casts
- Inheritance rules

---

# JIT Compiler

JIT = Just-In-Time Compiler

```text
IL
 ↓
Native Machine Code
```

Only methods that are called are compiled.

---

# JIT Execution Flow

```text
Method Called
      ↓
JIT Compilation
      ↓
Native Code
      ↓
CPU Execution
```

---

# Does JIT Compile the Entire Application?

**No.**

Only methods executed are compiled.

Unused methods are never JIT compiled.

---

# Second Method Call

First call

```text
IL
 ↓
JIT
 ↓
Native Code
```

Second call

```text
Native Code
 ↓
Execute
```

No recompilation.

---

# Where is Native Code Stored?

- Stored in process memory (RAM)
- Not written into DLL/EXE
- Removed when the process exits

---

# Types of JIT

## Normal JIT

Compile on first execution.

## Tiered Compilation

- First: Quick compilation
- Later: Optimized recompilation

## ReadyToRun (R2R)

Assembly contains precompiled native code for faster startup.

## Native AOT

Entire application compiled to native executable.

No JIT at runtime.

---

# Garbage Collector (GC)

Automatically frees unreachable objects.

Benefits

- Prevents memory leaks
- Reclaims unused memory
- Improves reliability

---

# Managed Heap

Objects created using `new` are stored on the managed heap.

```csharp
Employee emp = new Employee();
```

Managed by the Garbage Collector.

---

# Stack vs Heap

| Stack | Heap |
|--------|------|
| Method frames | Objects |
| Fast | Slower |
| Auto cleanup | GC cleanup |
| Mostly value types | Reference types |

---

# CLR Components

```text
CLR
├── Class Loader
├── JIT Compiler
├── Garbage Collector
├── Thread Manager
├── Exception Manager
├── Security
├── Metadata Engine
├── Type Checker
└── Debugger
```

---

# Complete Runtime Flow

```text
Write C#
      ↓
Roslyn
      ↓
IL
      ↓
Assembly
      ↓
CLR Starts
      ↓
Reads Manifest
      ↓
Reads Metadata
      ↓
Loads Types
      ↓
Verifies IL
      ↓
Method Invoked
      ↓
JIT Compiles
      ↓
Native Machine Code
      ↓
CPU Executes
      ↓
GC Cleans Memory
      ↓
Application Ends
```

---

# CLR Responsibilities

- Memory Management
- JIT Compilation
- Garbage Collection
- Assembly Loading
- Exception Handling
- Thread Management
- Security
- Type Safety
- Debugging
- Interoperability

---

# Top Interview Questions

1. What is CLR?
2. What is IL/MSIL/CIL?
3. Why does .NET use IL?
4. What is Metadata?
5. What is Manifest?
6. What is an Assembly?
7. What is JIT?
8. Does JIT compile the whole application?
9. Where is native code stored?
10. Managed vs Unmanaged code?
11. What is Reflection?
12. What is Tiered Compilation?
13. What is ReadyToRun?
14. What is Native AOT?
15. How does CLR ensure type safety?

---

# 2-Minute Interview Answer

> The C# compiler (Roslyn) converts source code into IL along with metadata and a manifest inside an assembly. When the application starts, the CLR loads the assembly, reads the manifest and metadata, verifies the IL, and loads the required types. When a method is called for the first time, the JIT compiler converts that method's IL into native machine code for the current CPU architecture. The CPU executes the native code, which is cached for future calls. During execution, the CLR also manages memory using the Garbage Collector, handles exceptions, manages threads, and enforces type safety until the application exits.
