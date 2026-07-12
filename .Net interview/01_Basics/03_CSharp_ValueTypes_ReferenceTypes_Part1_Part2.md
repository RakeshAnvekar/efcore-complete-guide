# C# Value Types & Reference Types - Part 1 and Part 2

## Part 1 - Value Types vs Reference Types

### Why Do We Need Two Types?

**Value Types:** Assignment creates a copy of the data.

**Reference Types:** Assignment copies the reference to the same object.

### Value Type Example

``` csharp
int x = 10;
int y = x;
y = 20;

Console.WriteLine(x); //10
Console.WriteLine(y); //20
```

Memory:

    Stack
    x = 10
    y = 20

### Reference Type Example

``` csharp
class Employee
{
    public string Name;
}

Employee e1 = new Employee();
e1.Name = "Rakesh";

Employee e2 = e1;
e2.Name = "John";

Console.WriteLine(e1.Name); // John
```

Memory:

    Stack:
    e1 ----+
    e2 ----+-------> Heap: Employee(Name="John")

### Value Types

-   int
-   long
-   short
-   byte
-   bool
-   char
-   decimal
-   float
-   double
-   DateTime
-   enum
-   struct

### Reference Types

-   class
-   string
-   array
-   interface
-   delegate
-   object

### Important Concept

Don't memorize:

-   Value Type = Stack
-   Reference Type = Heap

Instead remember:

-   Value Types copy values.
-   Reference Types copy references.

------------------------------------------------------------------------

## Part 2 - Stack vs Heap

### Stack

Stores:

-   Method calls
-   Local variables
-   Parameters
-   Return addresses

Example:

``` csharp
void Add()
{
    int x = 10;
    int y = 20;
}
```

Stack Frame:

    y = 20
    x = 10

When the method exits, the stack frame is automatically removed.

### Heap

Stores objects.

``` csharp
Employee emp = new Employee();
```

Memory:

    Stack:
    emp ---->

    Heap:
    Employee Object

### Fields Live Inside the Object

``` csharp
class Employee
{
    public int Id;
    public string Name;
}
```

`Id` and `Name` are inside the Employee object on the heap.

### Value Types Are Not Always on the Stack

``` csharp
class Student
{
    public int Age;
}
```

`Age` is stored inside the Student object on the heap because it is a
field of a heap object.

### Reference Variable Example

``` csharp
Employee emp = null;
```

The reference variable exists, but no heap object is allocated.

------------------------------------------------------------------------

## Stack vs Heap

  Stack               Heap
  ------------------- --------------------
  Fast                Slower
  Small               Large
  Automatic cleanup   Garbage Collector
  Method execution    Object storage
  LIFO                Dynamic allocation

------------------------------------------------------------------------

## Common Interview Questions

### Where is `int age = 25` stored?

As a local variable, in the current stack frame.

### Where is `Employee emp = new Employee()` stored?

-   `emp` (reference variable): current stack frame
-   Employee object: managed heap

### Where is `Employee.Id` stored?

Inside the Employee object on the managed heap.

### Who cleans the Stack?

The runtime automatically removes stack frames.

### Who cleans the Heap?

The Garbage Collector (GC).

------------------------------------------------------------------------

# Key Takeaways

-   Value types store actual values.
-   Reference types store references.
-   Assignment of value types copies data.
-   Assignment of reference types copies references.
-   Objects are allocated on the managed heap.
-   Local variables exist in the current stack frame.
-   Do not answer interviews with "Value types are always on the stack."
    The correct explanation is that storage depends on context.

This is the senior-level explanation expected in .NET interviews.
