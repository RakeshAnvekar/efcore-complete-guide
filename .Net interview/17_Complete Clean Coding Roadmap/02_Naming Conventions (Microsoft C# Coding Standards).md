# Clean Code for .NET Developers

# README 2 – Naming Conventions (Microsoft C# Standards)

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. Why Naming Matters
2. General Naming Rules
3. Class Naming
4. Interface Naming
5. Method Naming
6. Variable Naming
7. Private Fields
8. Constants
9. Readonly Fields
10. Properties
11. Boolean Variables
12. Enum Naming
13. Async Method Naming
14. Generic Type Parameters
15. Namespace Naming
16. Folder & File Naming
17. Database Naming
18. API Naming
19. DTO Naming
20. Common Naming Mistakes
21. Enterprise Example
22. Code Review Checklist
23. Interview Questions

---

# 1. Why Naming Matters

Good names reduce the need for comments.

Bad code

```csharp
public void P(Order o)
{
    if(o.A > 5000)
    {
    }
}
```

Good code

```csharp
public void ProcessOrder(Order order)
{
    if(order.TotalAmount > PremiumOrderAmount)
    {
        ApplyPremiumDiscount(order);
    }
}
```

A developer should understand the code without asking questions.

---

# 2. General Naming Rules

Always use names that describe:

* Purpose
* Business meaning
* Responsibility

Avoid

```text
a
b
x
temp
obj
data
manager
helper
```

Prefer

```text
customer

order

invoice

payment

inventory

discountCalculator
```

---

# 3. Class Naming

Use **PascalCase**

Good

```csharp
OrderService

PaymentService

CustomerRepository

InvoiceGenerator

DiscountCalculator
```

Bad

```csharp
orderservice

order_service

Order_Service

service1

Manager
```

Classes should be nouns.

---

# 4. Interface Naming

Always prefix with **I**

```csharp
IOrderService

IPaymentService

IRepository<T>

ILogger

IEmailSender
```

Bad

```csharp
OrderServiceInterface

OrderServiceImpl

Service
```

---

# 5. Method Naming

Methods represent actions.

Use verbs.

Good

```csharp
CreateOrder()

UpdateOrder()

DeleteOrder()

CalculateTax()

ReserveInventory()

GenerateInvoice()

SendEmail()
```

Bad

```csharp
Order()

Data()

Run()

Do()

Execute1()
```

---

# 6. Variable Naming

Use **camelCase**

Good

```csharp
customerName

totalAmount

discount

shippingAddress

orderDate
```

Bad

```csharp
a

x

obj

temp

data
```

---

# 7. Private Fields

Microsoft recommends prefixing private fields with `_`.

Good

```csharp
private readonly IOrderRepository _repository;

private readonly ILogger<OrderService> _logger;

private readonly AppDbContext _context;
```

Bad

```csharp
repository

Repository

m_repository

repo
```

---

# 8. Constants

Use **PascalCase**

```csharp
public const decimal PremiumOrderAmount = 5000;

public const int MaxRetryCount = 3;

public const string AdminRole = "Admin";
```

Bad

```csharp
premium

MAX

maxRetry

value
```

---

# 9. Readonly Fields

```csharp
private readonly ILogger<OrderService> _logger;

private readonly IConfiguration _configuration;

private readonly IMediator _mediator;
```

---

# 10. Properties

Use **PascalCase**

```csharp
public string CustomerName { get; set; }

public decimal TotalAmount { get; set; }

public DateTime OrderDate { get; set; }
```

Bad

```csharp
customername

customer_name

total
```

---

# 11. Boolean Variables

Always start with words that imply a true/false state.

Good

```csharp
isActive

isDeleted

hasPermission

canProcessOrder

shouldRetry
```

Bad

```csharp
status

flag

value

check
```

---

# 12. Enum Naming

Use singular names.

```csharp
public enum OrderStatus
{
    Pending,
    Confirmed,
    Cancelled,
    Delivered
}
```

Bad

```csharp
OrderStatuses

Statuses
```

---

# 13. Async Method Naming

Always end async methods with **Async**

Good

```csharp
GetOrderAsync()

SaveOrderAsync()

CreateCustomerAsync()

DeleteOrderAsync()
```

Bad

```csharp
GetOrder()

Save()

Run()
```

This immediately tells other developers that the method is asynchronous.

---

# 14. Generic Type Parameters

Standard conventions

```csharp
T

TEntity

TKey

TResult

TRequest

TResponse
```

Example

```csharp
public interface IRepository<TEntity>
{
}
```

---

# 15. Namespace Naming

Use the company and project hierarchy.

```text
Company.Project.API

Company.Project.Application

Company.Project.Domain

Company.Project.Infrastructure

Company.Project.Shared
```

Example

```text
Amazon.OrderService.Application.Commands
```

---

# 16. Folder & File Naming

Folders

```text
Controllers

Services

Repositories

Entities

Commands

Queries

DTOs

Configurations

Middlewares
```

Files

```text
OrderController.cs

OrderService.cs

OrderRepository.cs

CreateOrderCommand.cs

GetOrderQuery.cs
```

One public class per file.

---

# 17. Database Naming

Tables

```text
Orders

Customers

Products

Invoices
```

Primary Keys

```text
OrderId

CustomerId

ProductId
```

Foreign Keys

```text
CustomerId

ProductId
```

Avoid

```text
tblOrder

tblCustomer

data1

value2
```

---

# 18. API Naming

REST endpoints use nouns.

Good

```http
GET /orders

GET /orders/10

POST /orders

PUT /orders/10

DELETE /orders/10
```

Bad

```http
GET /GetOrders

POST /CreateOrder

PUT /UpdateOrder
```

HTTP verbs already describe the action.

---

# 19. DTO Naming

Use suffixes.

```text
CreateOrderRequest

UpdateOrderRequest

OrderResponse

OrderDto

CustomerDto
```

Avoid

```text
Data

Model1

OrderData

Result
```

---

# 20. Common Naming Mistakes

Bad

```csharp
int a;

string data;

bool status;

decimal x;

List<Order> list;
```

Good

```csharp
int customerAge;

string customerEmail;

bool isPremiumCustomer;

decimal totalAmount;

List<Order> orders;
```

---

# 21. Enterprise Example

Bad

```csharp
public class Manager
{
    public void Do(Order o)
    {
    }
}
```

Good

```csharp
public class OrderProcessingService
{
    public void ProcessOrder(Order order)
    {
    }
}
```

The second version immediately communicates the class's responsibility.

---

# 22. Code Review Checklist

Before submitting code, ask:

* Are class names meaningful?
* Are methods verbs?
* Are variables descriptive?
* Do boolean names start with `is`, `has`, `can`, or `should`?
* Do async methods end with `Async`?
* Are interfaces prefixed with `I`?
* Are private fields prefixed with `_`?
* Is each public class in its own file?
* Do folder names describe responsibilities?
* Can another developer understand the names without comments?

---

# 23. Common Interview Questions

### Why is naming important?

Good naming improves readability, reduces the need for comments, and makes code easier to maintain.

---

### Why should async methods end with `Async`?

It clearly indicates that the method is asynchronous and should typically be awaited.

---

### Why prefix interfaces with `I`?

It distinguishes abstractions from implementations and follows Microsoft .NET conventions.

---

### Why use descriptive variable names?

Because code is read far more often than it is written. Descriptive names reduce misunderstandings and bugs.

---

### What naming convention does C# use?

* Classes → PascalCase
* Interfaces → PascalCase with `I`
* Methods → PascalCase
* Properties → PascalCase
* Local variables → camelCase
* Parameters → camelCase
* Private fields → `_camelCase`
* Constants → PascalCase
* Enums → PascalCase (singular)
* Async methods → PascalCase ending with `Async`

---

# Key Takeaways

* Choose names that describe intent, not implementation.
* Follow Microsoft C# naming conventions consistently.
* Prefer business terminology over generic names.
* Good names make code self-documenting.
* Consistent naming improves code reviews, collaboration, and long-term maintenance.

---

# Next Module

**README 3 – DRY (Don't Repeat Yourself) Principle**

Topics include:

* What DRY really means
* Common violations
* Refactoring duplicate code
* Helper methods vs shared services
* Generic methods
* Repository examples
* Real enterprise examples
* Code review scenarios
* Interview questions
