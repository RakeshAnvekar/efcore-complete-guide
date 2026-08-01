# Module 2 – Single Responsibility Principle (SRP)

> **Course:** SOLID Principles – Complete Master Roadmap

**Difficulty:** ⭐⭐☆☆☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. What is SRP?
3. Understanding "One Reason to Change"
4. Real-World Analogy
5. Bad Design Example
6. Identifying Multiple Responsibilities
7. Refactoring Using SRP
8. Benefits of SRP
9. SRP in ASP.NET Core
10. Common Mistakes
11. Best Practices
12. Real-World Banking Example
13. Interview Questions
14. Interview Answers
15. Summary
16. Key Takeaways
17. Next Lesson

---

# Learning Objectives

By the end of this lesson, you will understand:

- What the Single Responsibility Principle (SRP) is
- What "One Reason to Change" means
- How to identify multiple responsibilities in a class
- How to refactor code using SRP
- Benefits of SRP
- How SRP is implemented in ASP.NET Core applications

---

# Introduction

The **Single Responsibility Principle (SRP)** is the first and arguably the most important principle in SOLID.

It teaches us how to organize our classes so that each class has a clear and focused purpose.

Understanding SRP makes the remaining SOLID principles much easier to learn.

---

# What is the Single Responsibility Principle?

Robert C. Martin (Uncle Bob) defines SRP as:

> **"A class should have only one reason to change."**

This is the official definition.

Many beginners misunderstand this statement.

They think:

- One class should contain only one method.
- One class should perform only one operation.

Both interpretations are incorrect.

A class can contain many methods, as long as those methods belong to the **same responsibility**.

---

# What Does "One Reason to Change" Mean?

A **reason to change** means a change caused by one specific business responsibility.

Consider the following class.

```csharp
public class EmployeeService
{
    public void SaveEmployee()
    {
    }

    public void UpdateEmployee()
    {
    }

    public void DeleteEmployee()
    {
    }
}
```

All three methods belong to employee management.

Possible changes include:

- Employee validation changes
- Employee storage changes
- Employee business rules change

These are all related to the same responsibility.

Therefore, this class follows SRP.

---

Now consider another example.

```csharp
public class EmployeeService
{
    public void SaveEmployee()
    {
    }

    public void SendEmail()
    {
    }

    public void GenerateReport()
    {
    }

    public void CalculateSalary()
    {
    }
}
```

This class contains multiple responsibilities.

Each feature belongs to a different business domain.

```
EmployeeService

├── Employee Management
├── Email Notification
├── Report Generation
└── Salary Calculation
```

Each responsibility may change independently.

Therefore, this class violates SRP.

---

# Real-World Analogy

Imagine a restaurant.

Different employees perform different jobs.

```
Restaurant

├── Chef
│      └── Cook Food
│
├── Cashier
│      └── Billing
│
├── Waiter
│      └── Serve Food
│
└── Cleaner
       └── Clean Tables
```

Every person has one primary responsibility.

Now imagine the chef has to:

- Cook food
- Wash dishes
- Serve customers
- Clean tables
- Collect payments

The restaurant becomes inefficient.

The same concept applies to software design.

Each class should have one responsibility.

---

# Bad Design Example

```csharp
public class Employee
{
    public void SaveEmployee()
    {
        Console.WriteLine("Saving Employee");
    }

    public void SendEmail()
    {
        Console.WriteLine("Sending Email");
    }

    public void GenerateReport()
    {
        Console.WriteLine("Generating Report");
    }

    public void CalculateSalary()
    {
        Console.WriteLine("Calculating Salary");
    }
}
```

At first glance, the class appears acceptable.

However, let's analyze its responsibilities.

```
Employee

├── Save Employee
├── Email
├── Reports
└── Salary
```

This class has four completely different responsibilities.

---

# Why Is This a Problem?

Suppose the email template changes.

Should employee management code change?

No.

Suppose salary rules change.

Should reporting code change?

Again, no.

However, because all responsibilities exist in one class, modifying one feature may accidentally affect another.

This increases maintenance cost and introduces bugs.

---

# Refactoring Using SRP

Instead of keeping everything inside one class,

```
Employee

├── Save
├── Email
├── Salary
└── Reports
```

Split the responsibilities.

```
EmployeeService

EmailService

SalaryService

ReportService
```

Now every class has a clear purpose.

---

## EmployeeService

```csharp
public class EmployeeService
{
    public void SaveEmployee()
    {
        Console.WriteLine("Saving Employee");
    }
}
```

---

## EmailService

```csharp
public class EmailService
{
    public void SendEmail()
    {
        Console.WriteLine("Sending Email");
    }
}
```

---

## SalaryService

```csharp
public class SalaryService
{
    public void CalculateSalary()
    {
        Console.WriteLine("Calculating Salary");
    }
}
```

---

## ReportService

```csharp
public class ReportService
{
    public void GenerateReport()
    {
        Console.WriteLine("Generating Report");
    }
}
```

Now each class has only one reason to change.

---

# Benefits of SRP

## 1. Easier Maintenance

Suppose salary calculation changes.

Only this class needs modification.

```
SalaryService
```

Other classes remain untouched.

---

## 2. Easier Testing

Testing becomes much simpler.

Instead of testing one huge class, we test each service independently.

Example:

```
EmailService

↓

Unit Test
```

No need to involve salary or reporting logic.

---

## 3. Better Team Collaboration

Imagine three developers.

```
Developer A

↓

SalaryService
```

```
Developer B

↓

EmailService
```

```
Developer C

↓

ReportService
```

Each developer works independently.

Merge conflicts become less frequent.

---

## 4. Better Reusability

Need email functionality in another project?

Simply reuse:

```
EmailService
```

No need to copy employee or salary code.

---

## 5. Better Readability

Smaller classes are easier to understand.

Developers quickly identify where a particular business rule belongs.

---

# SRP in ASP.NET Core

ASP.NET Core naturally follows SRP through layered architecture.

```
HTTP Request

↓

Controller

↓

Service

↓

Repository

↓

Database
```

Each layer has a single responsibility.

---

## Controller

```csharp
public class EmployeeController : ControllerBase
{
    private readonly EmployeeService employeeService;

    public EmployeeController(EmployeeService employeeService)
    {
        this.employeeService = employeeService;
    }

    [HttpPost]
    public IActionResult Save(Employee employee)
    {
        employeeService.SaveEmployee(employee);

        return Ok();
    }
}
```

Responsibility:

Receive HTTP requests and return HTTP responses.

---

## Service

```csharp
public class EmployeeService
{
    public void SaveEmployee(Employee employee)
    {
        // Business Logic
    }
}
```

Responsibility:

Implement business rules.

---

## Repository

```csharp
public class EmployeeRepository
{
    public void Save(Employee employee)
    {
        // Database Logic
    }
}
```

Responsibility:

Interact with the database.

---

# Common Mistakes

## Mistake 1

Thinking one class should contain only one method.

Wrong.

A class may have many methods if they all belong to the same responsibility.

---

## Mistake 2

Creating dozens of tiny classes without meaningful responsibilities.

This leads to unnecessary complexity.

---

## Mistake 3

Mixing unrelated business logic.

Example:

```
EmployeeService

├── Database
├── Email
├── SMS
├── Reports
├── Salary
├── Logging
```

This clearly violates SRP.

---

# Best Practices

- Give every class one business responsibility.
- Use meaningful class names.
- Separate business logic from infrastructure logic.
- Keep controllers thin.
- Move database logic into repositories.
- Move notification logic into dedicated services.
- Keep classes small and focused.

---

# Real-World Banking Example

Imagine you are developing an online banking system.

### Without SRP

```
AccountService

├── Create Account
├── Send SMS
├── Generate Statement
├── Email Customer
├── Loan Calculation
├── Fraud Detection
```

Every business team modifies the same class.

Result:

- Merge conflicts
- Difficult testing
- Difficult maintenance

---

### With SRP

```
AccountService

NotificationService

StatementService

LoanService

FraudDetectionService
```

Each team works independently.

Maintenance becomes much easier.

---

# Interview Questions

## Question 1

What is the Single Responsibility Principle?

---

## Question 2

What does "One Reason to Change" mean?

---

## Question 3

Can an SRP-compliant class have multiple methods?

---

## Question 4

How does SRP improve unit testing?

---

## Question 5

How is SRP used in ASP.NET Core?

---

# Interview Answers

## What is SRP?

The Single Responsibility Principle states that a class should have only one reason to change. Every class should focus on one business responsibility.

---

## What is a reason to change?

A reason to change is a modification caused by changes in one specific business responsibility. If unrelated business requirements require changes to the same class, the class violates SRP.

---

## Can a class have multiple methods?

Yes.

A class may contain many methods, provided they all support the same responsibility.

---

## How does SRP improve testing?

Focused classes have fewer dependencies, making them easier to isolate and test using unit tests.

---

## How is SRP used in ASP.NET Core?

ASP.NET Core separates responsibilities into Controllers, Services, Repositories, Middleware, and other layers. Each layer has a distinct responsibility, making applications easier to maintain and extend.

---

# Summary

The Single Responsibility Principle teaches us to design software so that each class has one clear responsibility and one reason to change.

Instead of creating large "God Classes" that handle multiple concerns, we split responsibilities into focused classes.

This approach results in:

- Better maintainability
- Better readability
- Easier testing
- Better team collaboration
- Higher code quality

SRP is the foundation of all other SOLID principles.

---

# Key Takeaways

✔ A class should have one responsibility.

✔ A class should have one reason to change.

✔ One responsibility does **not** mean one method.

✔ Separate unrelated business logic into different classes.

✔ Smaller, focused classes are easier to maintain and test.

✔ ASP.NET Core naturally encourages SRP through layered architecture.

---

# Next Lesson

## Module 2.2 – Identifying Responsibilities

In the next lesson, you'll learn how to:

- Identify responsibilities in existing classes.
- Detect SRP violations.
- Recognize God Classes.
- Refactor legacy code into well-designed, maintainable components.
- Analyze real-world enterprise examples before applying the next SOLID principle.