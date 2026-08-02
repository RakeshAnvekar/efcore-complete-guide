# 🚀 C# Extension Methods

> **Difficulty:** ⭐⭐⭐⭐☆
>
> **Interview Level:** Mid → Senior → Lead .NET Developer
>
> **Key Concept:** An **Extension Method** allows you to add new functionality to an existing class **without modifying its source code, creating a derived class, or recompiling it**.

---

# 📚 Table of Contents

* 🎯 Overview
* 🔹 Why Do We Need Extension Methods?
* 🔹 What is an Extension Method?
* ⚡ How Extension Methods Work
* 🧠 Internal Working
* 💻 Code Examples
* 📊 Extension Method vs Static Method
* 🌍 Real-World Examples
* ✅ Advantages
* ❌ Disadvantages
* 💡 Best Practices
* 🎤 Common Interview Questions
* 📌 Summary
* 🎯 Interview One-Liner

---

# 🎯 Overview

Suppose you want to add a new method to an existing class.

For example, the **String** class already provides methods like:

```csharp
string name = "Rakesh";

name.ToUpper();

name.ToLower();

name.Substring(0,3);
```

Now suppose you want to call

```csharp
name.IsPalindrome();
```

But **String** is a .NET built-in class.

You cannot modify its source code.

Extension Methods solve this problem.

---

# 🎯 Why Do We Need Extension Methods?

Sometimes we need to add new functionality to a class but:

* We don't own the source code.
* The class belongs to the .NET Framework.
* The class comes from a NuGet package.
* We don't want to use inheritance.

Instead of modifying the class, we extend it.

```text
            Existing Class

                  │

        Cannot Modify Source Code

                  │

                  ▼

        Extension Method

                  │

                  ▼

        New Functionality
```

---

# 🔹 What is an Extension Method?

An **Extension Method** is a **static method** inside a **static class**.

The first parameter uses the **`this`** keyword.

That tells the compiler which type is being extended.

---

# 📝 Rules for Creating an Extension Method

### Rule 1

The class must be **static**.

```csharp
public static class StringExtensions
{

}
```

---

### Rule 2

The method must be **static**.

```csharp
public static bool IsPalindrome(...)
{

}
```

---

### Rule 3

The first parameter must use **`this`**.

```csharp
public static bool IsPalindrome(this string value)
```

The `this` keyword specifies the type being extended.

---

# 💻 Complete Example

```csharp
using System.Linq;

public static class StringExtensions
{
    public static bool IsPalindrome(this string value)
    {
        string reversed =
            new string(value.Reverse().ToArray());

        return value == reversed;
    }
}
```

Usage

```csharp
string word = "madam";

bool result = word.IsPalindrome();

Console.WriteLine(result);
```

Output

```text
True
```

Notice that **String** never originally had an `IsPalindrome()` method.

---

# ⚡ How Extension Methods Work

When you write

```csharp
word.IsPalindrome();
```

The compiler converts it into

```csharp
StringExtensions.IsPalindrome(word);
```

### Compiler Flow

```text
word.IsPalindrome()

        │

        ▼

Compiler

        │

        ▼

StringExtensions.IsPalindrome(word)
```

Extension methods are simply **static methods with special compiler support**.

---

# 🧠 Internal Working

Suppose

```csharp
employee.GetFullName();
```

Internally

```text
Employee

↓

Extension Method Call

↓

Compiler Converts

↓

EmployeeExtensions.GetFullName(employee)
```

The object itself is passed as the first parameter.

---

# 💻 Example with Employee

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }
}
```

Extension Method

```csharp
public static class EmployeeExtensions
{
    public static string FullInfo(this Employee emp)
    {
        return $"{emp.Id} - {emp.Name}";
    }
}
```

Usage

```csharp
Employee emp = new Employee
{
    Id = 101,
    Name = "Rakesh"
};

Console.WriteLine(emp.FullInfo());
```

Output

```text
101 - Rakesh
```

---

# 💻 LINQ Example

Most LINQ methods are Extension Methods.

```csharp
List<int> numbers = new List<int>
{
    1,2,3,4,5
};

var even =
numbers.Where(x => x % 2 == 0);
```

Although it appears that `Where()` belongs to `List<T>`, it is actually an extension method.

Compiler converts it to

```csharp
Enumerable.Where(numbers,
                 x => x % 2 == 0);
```

---

# 🧠 Method Resolution

Suppose

```csharp
public class Employee
{
    public void Print()
    {
        Console.WriteLine("Class Method");
    }
}
```

Extension Method

```csharp
public static class EmployeeExtensions
{
    public static void Print(this Employee emp)
    {
        Console.WriteLine("Extension Method");
    }
}
```

Calling

```csharp
employee.Print();
```

Output

```text
Class Method
```

The compiler always prefers the **instance method** over the extension method.

---

# 📊 Extension Method vs Static Method

| Feature                     | Extension Method | Static Method |
| --------------------------- | ---------------- | ------------- |
| Belongs To                  | Static Class     | Static Class  |
| Called Like Instance Method | ✅ Yes            | ❌ No          |
| Uses `this` Keyword         | ✅ Yes            | ❌ No          |
| Can Extend Existing Class   | ✅ Yes            | ❌ No          |
| Compiler Converts Call      | ✅ Yes            | ❌ No          |
| Supports IntelliSense       | ✅ Yes            | ❌ Limited     |

---

# 🌍 Real-World Examples

## String

```csharp
name.IsPalindrome();
```

---

## Employee

```csharp
employee.FullInfo();
```

---

## DateTime

```csharp
DateTime.Now.IsWeekend();
```

---

## LINQ

```csharp
employees.Where(e => e.IsActive);

employees.OrderBy(e => e.Name);

employees.Select(e => e.Name);

employees.Count();
```

All of these are Extension Methods.

---

# ✅ Advantages

* Adds functionality without modifying the original class
* Works with .NET Framework classes
* Cleaner and more readable syntax
* Encourages reusable helper methods
* Used extensively by LINQ

---

# ❌ Disadvantages

* Cannot access private members
* Cannot override existing instance methods
* Too many extension methods can make APIs harder to discover
* They are resolved at compile time (no runtime polymorphism)

---

# 💡 Best Practices

✔ Use Extension Methods for reusable helper functionality.

✔ Keep Extension Methods small and focused.

✔ Organize them into meaningful static classes.

✔ Use clear and descriptive method names.

❌ Don't use Extension Methods when inheritance or normal instance methods are more appropriate.

---

# 🎤 Common Interview Questions

## Q1. What is an Extension Method?

A static method that extends an existing type using the `this` keyword on its first parameter.

---

## Q2. Can Extension Methods access private members?

No.

They can access only members that are accessible to the caller.

---

## Q3. Can Extension Methods override existing methods?

No.

If both an instance method and an extension method have the same signature, the instance method is always chosen.

---

## Q4. Why must Extension Methods be static?

Because they are implemented as static methods.

The compiler simply provides instance-method-like syntax.

---

## Q5. Why is the `this` keyword required?

It tells the compiler which type the method extends.

---

## Q6. Are LINQ methods Extension Methods?

Yes.

Methods like

* `Where()`
* `Select()`
* `OrderBy()`
* `GroupBy()`
* `Count()`
* `First()`

are Extension Methods defined in the `System.Linq.Enumerable` class.

---

# 📌 Summary

| Scenario                             | Recommended       |
| ------------------------------------ | ----------------- |
| Add functionality to a .NET class    | Extension Method  |
| Add helper methods to your own class | Extension Method  |
| Extend `string`                      | Extension Method  |
| Extend `DateTime`                    | Extension Method  |
| Extend `List<T>`                     | Extension Method  |
| Implement reusable utility methods   | Extension Method  |
| Modify private fields                | ❌ Not Possible    |
| Override existing instance methods   | ❌ Not Possible    |
| LINQ (`Where`, `Select`, `OrderBy`)  | Extension Methods |

---

# 🎯 Interview One-Liner

> **An Extension Method is a static method defined inside a static class that uses the `this` keyword on its first parameter to add new functionality to an existing type without modifying its source code or using inheritance.**

---

# ⭐ Lead Interview Tip

One of the most common senior interview questions is:

**"How does LINQ add methods like `Where()`, `Select()`, and `OrderBy()` to `List<T>`?"**

**Answer:**

They are **Extension Methods** defined in the `System.Linq.Enumerable` class.

When you write:

```csharp
employees.Where(e => e.IsActive);
```

the compiler converts it to:

```csharp
Enumerable.Where(employees, e => e.IsActive);
```

This is why LINQ methods appear to be instance methods even though they are implemented as static methods.
