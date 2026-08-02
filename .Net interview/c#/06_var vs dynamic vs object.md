# 🚀 C# `var` vs `object` vs `dynamic`

> **Difficulty:** ⭐⭐⭐⭐☆
>
> **Interview Level:** Mid → Senior → Lead .NET Developer
>
> **Key Concept:** Understanding when type checking happens—**Compile Time vs Runtime**.

---

# 📚 Table of Contents

- Why Do We Need Them?
- What is `var`?
- What is `object`?
- What is `dynamic`?
- Compiler vs Runtime
- Memory Diagrams
- Performance Comparison
- Real-World Examples
- Best Practices
- Interview Questions
- Summary

---

# 🎯 Why Do We Need `var`, `object`, and `dynamic`?

Normally we declare variables like this:

```csharp
string name = "Rakesh";
int age = 30;
```

Sometimes we don't want to explicitly write the type.

Sometimes we don't know the type until runtime.

That's why C# provides three different options.

```
                    C#
                     │
      ┌──────────────┼──────────────┐
      │              │              │
     var          object        dynamic
      │              │              │
Compile Time    Base CLR Type    Runtime
```

---

# 🔹 What is `var`?

> **Definition**

`var` is **not a data type**.

It is a **compile-time type inference keyword**.

The compiler automatically determines the variable type.

---

## Example

```csharp
var name = "Rakesh";
```

Compiler converts it into

```csharp
string name = "Rakesh";
```

### Compiler View

```
Source Code

var age = 25;

        │

        ▼

Compiler

        │

        ▼

int age = 25;
```

No runtime overhead.

---

# ⚡ Compile-Time Type Checking

```csharp
var number = 10;

number = 20;
```

✔ Works

```csharp
var number = 10;

number = "Hello";
```

❌ Compile-time error

```
Cannot convert string to int
```

Because `var` has already become an `int`.

---

# 🧠 Memory

```
Stack

name
 │
 ▼

Heap

"Rakesh"
```

Exactly the same as

```csharp
string name = "Rakesh";
```

---

# ✅ Advantages

- Strongly typed
- Faster compilation
- Full IntelliSense
- Cleaner code
- Required for anonymous types

---

# ❌ Disadvantages

- Must be initialized
- Type cannot change
- Can reduce readability if overused

---

# 🔹 What is `object`?

Everything in .NET ultimately derives from `System.Object`.

```
           object
              │
    ┌─────────┴─────────┐
    │                   │
Value Types      Reference Types
```

---

## Example

```csharp
object value = "Hello";
```

The compiler only knows it's an **object**.

Therefore,

```csharp
value.Length;
```

❌ Error

Need casting.

```csharp
((string)value).Length;
```

✔ Works

---

# 📦 Boxing

```csharp
int number = 10;

object obj = number;
```

```
Stack

10

↓

Heap

10

↓

object
```

---

# 📦 Unboxing

```csharp
int number = (int)obj;
```

```
Heap

↓

Copy Value

↓

Stack
```

---

# 🔹 What is `dynamic`?

Unlike `var`, **the compiler performs no type checking**.

Everything happens at runtime.

```csharp
dynamic value = "Hello";

Console.WriteLine(value.Length);
```

✔ Compiles

✔ Runs

---

## Runtime Type Changes

```csharp
dynamic data = 10;

data = "Hello";

data = true;

data = DateTime.Now;
```

Everything works.

---

# ⚠ Runtime Errors

```csharp
dynamic value = 10;

Console.WriteLine(value.Name);
```

Compiles

↓

Runs

↓

💥 RuntimeBinderException

---

# 🧠 Compiler vs Runtime

```
var

Compile Time

↓

Known Type

↓

Safe

----------------------

object

Compile Time

↓

Known as object

↓

Casting Required

----------------------

dynamic

Compile Time

↓

Unknown

↓

Runtime Decides
```

---

# 📊 Performance Comparison

| Feature | var | object | dynamic |
|----------|-----|--------|----------|
| Type Known at Compile Time | ✅ | ✅ | ❌ |
| Runtime Binding | ❌ | ❌ | ✅ |
| IntelliSense | ✅ | Limited | Limited |
| Requires Casting | ❌ | ✅ | ❌ |
| Performance | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐☆ | ⭐⭐☆☆☆ |
| Type Safety | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐☆ | ⭐☆☆☆☆ |

---

# 💡 When Should You Use Each?

### ✅ Use `var`

- LINQ
- Local variables
- Anonymous types

---

### ✅ Use `object`

- Generic APIs
- Mixed-type collections
- Base class references

---

### ✅ Use `dynamic`

- Reflection
- COM Interop
- Office Automation
- Dynamic JSON

---

# 🎤 Interview Questions

### Is `var` dynamic?

❌ No.

`var` is **strongly typed**.

---

### Why does `object` need casting?

Because the compiler only knows it's an object.

---

### Why is `dynamic` slower?

Because member resolution happens **at runtime**.

---

### Which one is safest?

```
var ✅

↓

object

↓

dynamic ❌
```

---

# 📝 Quick Revision

| Scenario | Best Choice |
|----------|-------------|
| Local Variable | `var` |
| LINQ | `var` |
| Anonymous Type | `var` |
| Mixed Types | `object` |
| Reflection | `dynamic` |
| COM Interop | `dynamic` |

---

# 🎯 Interview One-Liner

- **`var`** → Compile-time type inference. Strongly typed and optimized.
- **`object`** → Base type of all .NET types. Requires casting for specific members.
- **`dynamic`** → Runtime-resolved type. Flexible but sacrifices compile-time safety.