# Entity Framework Core
# When to Use Lazy Loading vs Eager Loading vs Explicit Loading

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Topic:** Choosing the Right Loading Strategy

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Introduction
2. Lazy Loading
3. Eager Loading
4. Explicit Loading
5. Real-Time Examples
6. Decision Tree
7. Comparison Table
8. Best Practices
9. Interview Questions
10. Summary

---

# Introduction

Entity Framework Core provides three ways to load related data.

```
1. Lazy Loading

2. Eager Loading

3. Explicit Loading
```

Each has different use cases.

Choosing the correct loading strategy is important for application performance.

---

# 1. Lazy Loading

## What is it?

Entity Framework loads related entities **only when they are accessed**.

Example

```csharp
var department = context.Departments.First();

Console.WriteLine(department.Name);

// Employees are loaded here
Console.WriteLine(department.Employees.Count);
```

Initially

```
Department

↓

Employees

NOT Loaded
```

Later

```
department.Employees
```

Entity Framework automatically executes another SQL query.

---

## When Should We Use Lazy Loading?

Use Lazy Loading when

✔ Related data is rarely needed.

✔ Desktop Applications (WinForms/WPF)

✔ Small Internal Tools

✔ Small CRUD Applications

✔ Prototypes

Example

Employee Screen

Initially

```
Employee Name

Department
```

Later

User clicks

```
View Projects
```

Projects load automatically.

---

## Avoid Lazy Loading When

❌ ASP.NET Core APIs

❌ Microservices

❌ Reports

❌ Dashboard

❌ Large Datasets

❌ Performance-Critical Applications

Reason

```
N+1 Query Problem
```

---

# 2. Eager Loading

## What is it?

Entity Framework loads the main entity and all required related entities immediately.

Example

```csharp
var departments =
context.Departments
       .Include(d => d.Employees)
       .ToList();
```

Everything is loaded together.

```
Department

↓

Employees

Already Loaded
```

---

## When Should We Use Eager Loading?

Use Eager Loading when

✔ Related data is definitely required.

✔ ASP.NET Core APIs

✔ REST APIs

✔ Reports

✔ Dashboard

✔ Microservices

✔ Export to Excel

✔ Export to PDF

✔ Mobile APIs

Example

```
Order Details API
```

Need

```
Order

↓

Customer

↓

OrderItems

↓

Products
```

Load everything together.

---

## Why?

Only one database operation.

No N+1 Problem.

Better performance.

---

# 3. Explicit Loading

## What is it?

The developer manually decides when related entities should be loaded.

Example

```csharp
var department =
context.Departments.First();

context.Entry(department)
       .Collection(d => d.Employees)
       .Load();
```

Entity Framework loads Employees only because the developer explicitly requested them.

---

## When Should We Use Explicit Loading?

Use Explicit Loading when

✔ Related data depends on user action.

✔ Expand/Collapse UI

✔ View Details Button

✔ Admin Screens

✔ Optional Data

✔ Conditional Loading

Example

Employee API

Initially

```
Employee Details
```

Later

User clicks

```
Show Department
```

Now

```csharp
context.Entry(employee)
       .Reference(e => e.Department)
       .Load();
```

Only then is Department loaded.

---

# Real-Time Example 1

## Banking Application

```
Customer

↓

Accounts

↓

Transactions
```

Need everything immediately?

```
Use

Eager Loading
```

Need transactions only after clicking

```
View Transactions
```

Use

```
Explicit Loading
```

Need transactions automatically whenever accessed?

```
Lazy Loading
```

---

# Real-Time Example 2

## E-Commerce

```
Order

↓

OrderItems

↓

Product
```

Order Details API

```
Use

Eager Loading
```

Admin clicks

```
Show Shipment Details
```

```
Use

Explicit Loading
```

Desktop Application

```
Use

Lazy Loading
```

---

# Real-Time Example 3

## HR Application

Employee List Page

Need only

```
Employee Name

Department
```

Use

```
Eager Loading
```

User clicks

```
Show Salary History
```

Use

```
Explicit Loading
```

Small Desktop App

```
Lazy Loading
```

works fine.

---

# Decision Tree

```
Do you know related data is required?

           │

      Yes ─────────► Eager Loading

           │

           No

           │

Will user request it later?

           │

      Yes ─────────► Explicit Loading

           │

           No

           │

Small Desktop App?

           │

      Yes ─────────► Lazy Loading

           │

           No

           │

Prefer Explicit Loading
```

---

# Comparison Table

| Feature | Lazy | Eager | Explicit |
|----------|------|--------|----------|
| Loads Automatically | ✔ | ✔ | ❌ |
| Developer Controls Timing | ❌ | ❌ | ✔ |
| Related Data Loaded Immediately | ❌ | ✔ | ❌ |
| SQL Queries | Multiple | Usually One | Manual |
| Can Cause N+1 | ✔ | ❌ | ✔ (if used in loops) |
| Uses Include() | ❌ | ✔ | ❌ |
| Uses Load() | ❌ | ❌ | ✔ |
| Uses Proxy | ✔ | ❌ | ❌ |
| Best for APIs | ❌ | ✔ | Sometimes |
| Best for Desktop Apps | ✔ | ✔ | ✔ |

---

# Which One Should I Use?

| Scenario | Recommended |
|-----------|-------------|
| REST API | Eager Loading |
| Microservice | Eager Loading |
| Dashboard | Eager Loading |
| Reports | Eager Loading |
| Export to Excel | Eager Loading |
| Export to PDF | Eager Loading |
| Desktop Application | Lazy Loading |
| Optional Details Screen | Explicit Loading |
| Expand/Collapse | Explicit Loading |
| View Details Button | Explicit Loading |
| Admin Screen | Explicit Loading |

---

# Best Practices

## Lazy Loading

✔ Use only for small applications.

✔ Avoid inside loops.

✔ Be aware of the N+1 Query Problem.

---

## Eager Loading

✔ Best for Web APIs.

✔ Use Include() only for required relationships.

✔ Avoid loading huge object graphs unnecessarily.

---

## Explicit Loading

✔ Use when data is optional.

✔ Gives complete control over SQL execution.

✔ Avoid calling Load() inside loops.

---

# Interview Questions

## Which loading strategy is best for ASP.NET Core APIs?

```
Eager Loading
```

Because related data is usually known in advance, and it avoids the N+1 Query Problem.

---

## Which loading strategy gives the developer complete control?

```
Explicit Loading
```

---

## Which loading strategy can automatically execute SQL?

```
Lazy Loading
```

---

## Which loading strategy avoids the N+1 Query Problem?

```
Eager Loading
```

---

## Which loading strategy uses Include()?

```
Eager Loading
```

---

## Which loading strategy uses Load()?

```
Explicit Loading
```

---

# Summary

Choose the loading strategy based on your application's requirements.

- **Lazy Loading** is suitable for small applications where related data is rarely needed.
- **Eager Loading** is the preferred choice for ASP.NET Core APIs, Microservices, and reporting because it retrieves all required data up front and avoids unnecessary database round trips.
- **Explicit Loading** is ideal when related data should be loaded only after a specific user action or business condition.

---

# Easy Interview Trick

Imagine visiting a restaurant.

## Lazy Loading

```
You order food.

↓

Later you ask for dessert.

↓

Waiter automatically brings it.
```

---

## Eager Loading

```
You order

Food

+

Dessert

↓

Everything arrives together.
```

---

## Explicit Loading

```
You order food.

↓

Finish eating.

↓

Call the waiter.

↓

"Please bring dessert."

↓

Dessert arrives.
```

---

# Final Interview Recommendation

| Application Type | Best Choice |
|------------------|-------------|
| ASP.NET Core Web API | ✅ Eager Loading |
| Microservices | ✅ Eager Loading |
| Reporting | ✅ Eager Loading |
| Dashboard | ✅ Eager Loading |
| Desktop Application | ✅ Lazy Loading |
| Optional User Actions | ✅ Explicit Loading |
| Expand/Collapse UI | ✅ Explicit Loading |
| View Details Screen | ✅ Explicit Loading |