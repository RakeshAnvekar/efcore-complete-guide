# .NET Garbage Collector (GC) Deep Dive Study Notes

> Daily revision notes for .NET Garbage Collector fundamentals through the Large Object Heap.

---

# Part 1 – Garbage Collection Fundamentals

## Why was GC invented?

Before .NET (for example in C/C++) developers manually allocated and freed memory.

```cpp
Employee* emp = new Employee();
delete emp;
```

Problems:
- Memory leaks
- Double free
- Dangling pointers
- Crashes

The CLR introduced the Garbage Collector (GC) to automatically reclaim memory occupied by unreachable managed objects.

## Definition

Garbage Collection is the CLR's automatic memory management mechanism that identifies unreachable managed objects and reclaims their memory.

## Memory Management

Memory management answers:

1. Where to allocate memory?
2. How much memory?
3. When to release it?
4. How to reuse it?

## Stack vs Managed Heap

### Stack
- Local variables
- Parameters
- References
- Method frames

### Managed Heap
- Objects created with `new`

```csharp
Employee emp = new Employee();
```

```
Stack
emp ------>
Heap
Employee Object
```

## Managed vs Unmanaged Memory

Managed:
- Controlled by CLR
- GC cleans memory

Unmanaged:
- File handles
- Database connections
- Sockets
- OS resources

GC does not release unmanaged resources.

## What happens during `new`

1. Calculate object size.
2. Allocate memory.
3. Initialize fields.
4. Run constructor.
5. Return reference.

## Does obj = null free memory?

No. It only removes one reference. If no references remain, the object becomes eligible for garbage collection.

---

# Part 2 – Heap and Object Allocation

## Bump Pointer Allocation

```
Allocate
↓
Move Pointer
↓
Done
```

This makes allocation extremely fast.

## Object Header

```
Sync Block Index
Method Table Pointer
Object Fields
```

Sync Block:
- lock
- hash code
- synchronization

Method Table:
- virtual methods
- interfaces
- polymorphism

## Allocation Steps

1. Calculate size.
2. Check free space.
3. Move bump pointer.
4. Initialize memory.
5. Execute constructor.
6. Return reference.

---

# Part 3 – Garbage Collection Process

## Reachability Analysis

GC asks:

Can this object be reached from a GC Root?

If Yes → Survives

If No → Garbage

## GC Roots

- Local variables
- Static fields
- CPU registers
- Active threads
- Runtime handles

## Mark Phase

Reachable objects are marked.

## Sweep Phase

Unmarked objects become available for reuse.

## Compact Phase

Before:

A
FREE
B
FREE
C

After:

A
B
C
FREE SPACE

Compaction removes fragmentation and updates references.

---

# Part 4 – Generations

Weak Generational Hypothesis:

Most objects die young.

## Generation 0
- New objects
- Fastest collection

## Generation 1
- Survived Gen0

## Generation 2
- Long-lived objects
- Slowest collection

## Promotion

Gen0 → Gen1 → Gen2

Only reachable objects are promoted.

## Collection Types

Gen0: scans Gen0

Gen1: scans Gen0 + Gen1

Gen2: scans Gen0 + Gen1 + Gen2

## Ephemeral Segment

Gen0 + Gen1

---

# Part 5 – Large Object Heap (LOH)

Objects approximately 85 KB or larger are allocated on the LOH.

Example:

```csharp
byte[] buffer = new byte[100000];
```

## Why LOH?

Moving very large objects is expensive.

## Characteristics

- Separate from SOH
- Collected with Gen2
- Modern .NET supports LOH compaction but not every collection.

## SOH vs LOH

| SOH | LOH |
|-----|-----|
| < ~85 KB | >= ~85 KB |
| Gen0/1/2 | Collected with Gen2 |

## Best Practices

- Reuse buffers
- Use ArrayPool<T>
- Avoid repeated large allocations

---

# Quick Interview Revision

- GC automatically reclaims unreachable managed objects.
- Objects created using `new` go to the Managed Heap.
- `obj = null` does not immediately free memory.
- GC uses Reachability Analysis, not reference counting.
- GC phases: Mark, Sweep, Compact.
- New objects start in Gen0.
- Surviving objects are promoted to Gen1 and Gen2.
- Large objects (~85 KB+) go to the LOH.
