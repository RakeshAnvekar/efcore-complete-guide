# CQRS (Command Query Responsibility Segregation) with MediatR

# README 1 – CQRS Fundamentals

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is CQRS?
2. Why Do We Need CQRS?
3. Problems Without CQRS
4. Commands vs Queries
5. CQRS Architecture
6. Traditional Architecture vs CQRS
7. What is MediatR?
8. How MediatR Works
9. CQRS Request Flow
10. Benefits of CQRS
11. Common Use Cases
12. Common Interview Questions
13. Key Takeaways

---

# 1. What is CQRS?

**CQRS** stands for:

```text
Command Query Responsibility Segregation
```

CQRS is an **architectural pattern** that separates:

* **Commands** → Operations that modify data.
* **Queries** → Operations that read data.

Instead of one service handling both reads and writes, CQRS separates them into different handlers.

---

# 2. Why Do We Need CQRS?

Consider an E-Commerce application.

A single `OrderService` handles everything.

```csharp
public class OrderService
{
    public Task<Order> GetOrder(int id);

    public Task<List<Order>> GetOrders();

    public Task CreateOrder(Order order);

    public Task UpdateOrder(Order order);

    public Task DeleteOrder(int id);
}
```

As the application grows:

* Hundreds of methods
* Business rules
* Validation
* Transactions
* Mapping
* Logging
* Caching

The service becomes difficult to maintain.

---

# 3. Problems Without CQRS

## Mixed Responsibilities

One service performs:

* Reading
* Writing

Violates the **Single Responsibility Principle (SRP)**.

---

## Large Service Classes

Example

```text
OrderService

↓

200+ Methods
```

Understanding and maintaining such services becomes difficult.

---

## Difficult Testing

Testing a large service requires mocking many dependencies.

Small focused handlers are easier to test.

---

## Difficult Scaling

Read operations often require different optimization than write operations.

Traditional architecture does not separate them.

---

# 4. Commands vs Queries

## Command

A Command changes application state.

Examples

```text
CreateOrderCommand

UpdateOrderCommand

DeleteOrderCommand

CreateCustomerCommand
```

Commands perform:

```text
INSERT

UPDATE

DELETE
```

---

## Query

A Query only retrieves data.

Examples

```text
GetOrderQuery

GetOrdersQuery

GetProductsQuery

GetCustomerQuery
```

Queries perform:

```text
SELECT
```

---

## Important Rule

Commands

```text
Modify Data
```

Queries

```text
Read Data
```

Queries should never modify the database.

---

# 5. CQRS Architecture

```text
                    Client

                       │

          ┌────────────┴────────────┐

          ▼                         ▼

     Command Side             Query Side

          │                         │

    Command Handler          Query Handler

          │                         │

      Repository            Read Repository

          │                         │

              SQL Server / Database
```

The read side and write side are separated.

---

# 6. Traditional Architecture vs CQRS

## Traditional

```text
Client

↓

Controller

↓

Service

↓

Repository

↓

Database
```

One service handles all operations.

---

## CQRS

```text
Client

↓

Controller

↓

Mediator

↓

Command Handler

↓

Repository

↓

Database

-----------------------------

Client

↓

Controller

↓

Mediator

↓

Query Handler

↓

Read Repository

↓

Database
```

Each request has its own handler.

---

# 7. What is MediatR?

**MediatR** is a .NET library that implements the **Mediator Design Pattern**.

Instead of the controller directly calling services, it sends a request to the Mediator.

Example

Without MediatR

```text
Controller

↓

OrderService
```

With MediatR

```text
Controller

↓

Mediator

↓

Handler

↓

Repository
```

The controller no longer knows which handler processes the request.

---

# 8. How MediatR Works

Suppose the client sends

```http
POST /orders
```

Flow

```text
Controller

↓

CreateOrderCommand

↓

Mediator

↓

CreateOrderHandler

↓

Repository

↓

Database
```

Now suppose the client requests

```http
GET /orders/10
```

Flow

```text
Controller

↓

GetOrderQuery

↓

Mediator

↓

GetOrderHandler

↓

Repository

↓

Database
```

Different handlers process different requests.

---

# 9. CQRS Request Flow

## Create Order

```text
HTTP POST

↓

Controller

↓

CreateOrderCommand

↓

Mediator

↓

CreateOrderHandler

↓

Repository

↓

SaveChanges()

↓

Response
```

---

## Get Order

```text
HTTP GET

↓

Controller

↓

GetOrderQuery

↓

Mediator

↓

GetOrderHandler

↓

Repository

↓

Database

↓

Response
```

---

# 10. Benefits of CQRS

## Separation of Concerns

Read and write operations are independent.

---

## Small Focused Classes

Instead of one large service:

```text
OrderService
```

You get

```text
CreateOrderHandler

UpdateOrderHandler

DeleteOrderHandler

GetOrderHandler

GetOrdersHandler
```

Each handler has one responsibility.

---

## Better Testability

Handlers are small and easy to unit test.

---

## Better Maintainability

Business logic is isolated.

Adding new features requires creating new handlers rather than modifying existing services.

---

## Independent Optimization

The read side can use:

* Read Replicas
* Redis
* ElasticSearch

The write side can use:

* SQL Server
* Transactions
* Domain validation

---

# 11. Common Use Cases

CQRS is commonly used in:

* Banking systems
* E-Commerce applications
* ERP systems
* Healthcare applications
* Microservices
* Event-driven systems

Example

### Commands

* Place Order
* Cancel Order
* Create Invoice
* Update Customer

### Queries

* Order History
* Product Catalog
* Customer Profile
* Sales Dashboard

---

# 12. Common Interview Questions

### What is CQRS?

CQRS is an architectural pattern that separates read operations from write operations into different models and handlers.

---

### What is a Command?

A request that changes application state.

Examples:

* Create
* Update
* Delete

---

### What is a Query?

A request that retrieves data without modifying application state.

---

### What is MediatR?

MediatR is a .NET library implementing the Mediator Pattern. It routes commands and queries to their appropriate handlers while keeping controllers decoupled from business logic.

---

### Does CQRS require MediatR?

No.

CQRS is an architectural pattern.

MediatR is one popular implementation of the Mediator Pattern that fits well with CQRS.

---

### Should every application use CQRS?

No.

Simple CRUD applications often do not benefit from CQRS because it introduces additional classes and complexity.

CQRS is most valuable for applications with complex business logic or distinct read and write requirements.

---

# 13. Key Takeaways

* CQRS separates **Commands** (writes) from **Queries** (reads).
* Commands modify data.
* Queries only read data.
* CQRS improves separation of concerns.
* MediatR implements the Mediator Pattern and routes requests to handlers.
* Controllers communicate with `IMediator` instead of directly invoking services.
* Each command and query has its own dedicated handler.
* CQRS is best suited for medium to large applications with complex business logic.
* CQRS is an architectural pattern; MediatR is a supporting library, not a requirement.

---

# What's Next?

**README 2 – Complete CQRS + MediatR Implementation**

Topics include:

* Installing MediatR
* Project folder structure
* Registering MediatR in Dependency Injection
* `CreateOrderCommand`
* `CreateOrderCommandHandler`
* `GetOrderQuery`
* `GetOrderQueryHandler`
* `Controller using IMediator`
* Request/Response flow
* Pipeline Behaviors (Logging, Validation, Performance)
* Complete enterprise architecture using CQRS, MediatR, Repository, and Unit of Work
