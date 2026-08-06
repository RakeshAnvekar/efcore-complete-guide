# CQRS + MediatR in ASP.NET Core

# README 2 – Complete CQRS + MediatR Implementation

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. Project Overview
2. Solution Architecture
3. Project Structure
4. Entity
5. Repository
6. Registering MediatR
7. Creating a Command
8. Command Handler
9. Creating a Query
10. Query Handler
11. DTO
12. Controller
13. Dependency Injection
14. Complete Request Flow
15. Internal MediatR Flow
16. CQRS vs Traditional Architecture
17. Advantages
18. Best Practices
19. Common Interview Questions
20. Key Takeaways

---

# 1. Project Overview

We are building an Order Management API.

Supported APIs

```http
POST /api/orders

GET /api/orders/{id}
```

We will use:

* ASP.NET Core Web API
* CQRS
* MediatR
* Repository Pattern
* Entity Framework Core
* Dependency Injection

---

# 2. Solution Architecture

```text
                Client

                   │

                   ▼

           OrderController

                   │

                   ▼

               IMediator

        ┌──────────┴──────────┐

        ▼                     ▼

CreateOrderCommand      GetOrderQuery

        │                     │

        ▼                     ▼

CreateOrderHandler     GetOrderHandler

        │                     │

        ▼                     ▼

     Repository          Repository

        │                     │

        └──────────┬──────────┘

                   ▼

              AppDbContext

                   ▼

               SQL Server
```

---

# 3. Project Structure

```text
Application

│

├── Commands
│
│   └── Orders
│       └── CreateOrder
│           ├── CreateOrderCommand.cs
│           └── CreateOrderHandler.cs
│
├── Queries
│
│   └── Orders
│       └── GetOrder
│           ├── GetOrderQuery.cs
│           └── GetOrderHandler.cs
│
├── DTOs
│
├── Interfaces
│
└── Behaviors

Infrastructure

│

├── Repository
├── DbContext
└── Persistence

API

│

├── Controllers
├── Program.cs
└── appsettings.json
```

---

# 4. Entity

```csharp
public class Order
{
    public int Id { get; set; }

    public string CustomerName { get; set; }

    public decimal Amount { get; set; }
}
```

Represents the database table.

---

# 5. Repository

## Interface

```csharp
public interface IOrderRepository
{
    Task AddAsync(Order order);

    Task<Order?> GetByIdAsync(int id);
}
```

---

## Implementation

```csharp
public class OrderRepository : IOrderRepository
{
    private readonly AppDbContext _context;

    public OrderRepository(AppDbContext context)
    {
        _context = context;
    }

    public async Task AddAsync(Order order)
    {
        await _context.Orders.AddAsync(order);

        await _context.SaveChangesAsync();
    }

    public async Task<Order?> GetByIdAsync(int id)
    {
        return await _context.Orders.FindAsync(id);
    }
}
```

> **Note:** If your application uses a Unit of Work, remove `SaveChangesAsync()` from the repository and commit once through the Unit of Work.

---

# 6. Register MediatR

Install packages

```bash
dotnet add package MediatR

dotnet add package MediatR.Extensions.Microsoft.DependencyInjection
```

Program.cs

```csharp
builder.Services.AddMediatR(cfg =>
{
    cfg.RegisterServicesFromAssembly(typeof(Program).Assembly);
});
```

This scans the assembly and automatically registers all handlers.

---

# 7. Create Command

```csharp
using MediatR;

public class CreateOrderCommand : IRequest<int>
{
    public string CustomerName { get; set; }

    public decimal Amount { get; set; }
}
```

Meaning

```text
Request

↓

Returns

↓

OrderId
```

---

# 8. Create Command Handler

```csharp
using MediatR;

public class CreateOrderHandler
    : IRequestHandler<CreateOrderCommand, int>
{
    private readonly IOrderRepository _repository;

    public CreateOrderHandler(
        IOrderRepository repository)
    {
        _repository = repository;
    }

    public async Task<int> Handle(
        CreateOrderCommand request,
        CancellationToken cancellationToken)
    {
        var order = new Order
        {
            CustomerName = request.CustomerName,
            Amount = request.Amount
        };

        await _repository.AddAsync(order);

        return order.Id;
    }
}
```

Flow

```text
Command

↓

Handler

↓

Repository

↓

Database
```

---

# 9. Create Query

```csharp
using MediatR;

public class GetOrderQuery
    : IRequest<OrderDto>
{
    public int Id { get; set; }
}
```

Queries never modify data.

---

# 10. Create Query Handler

```csharp
using MediatR;

public class GetOrderHandler
    : IRequestHandler<GetOrderQuery, OrderDto>
{
    private readonly IOrderRepository _repository;

    public GetOrderHandler(
        IOrderRepository repository)
    {
        _repository = repository;
    }

    public async Task<OrderDto> Handle(
        GetOrderQuery request,
        CancellationToken cancellationToken)
    {
        var order =
            await _repository.GetByIdAsync(request.Id);

        if(order == null)
            return null;

        return new OrderDto
        {
            Id = order.Id,

            CustomerName = order.CustomerName,

            Amount = order.Amount
        };
    }
}
```

Each handler has exactly one responsibility.

---

# 11. DTO

```csharp
public class OrderDto
{
    public int Id { get; set; }

    public string CustomerName { get; set; }

    public decimal Amount { get; set; }
}
```

DTOs are returned to clients instead of EF entities.

---

# 12. Controller

```csharp
[ApiController]
[Route("api/orders")]
public class OrderController : ControllerBase
{
    private readonly IMediator _mediator;

    public OrderController(IMediator mediator)
    {
        _mediator = mediator;
    }

    [HttpPost]
    public async Task<IActionResult> Create(
        CreateOrderCommand command)
    {
        var id = await _mediator.Send(command);

        return Ok(id);
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> Get(int id)
    {
        var order =
            await _mediator.Send(
                new GetOrderQuery
                {
                    Id = id
                });

        return Ok(order);
    }
}
```

The controller only communicates with `IMediator`.

---

# 13. Dependency Injection

```csharp
builder.Services.AddScoped<IOrderRepository, OrderRepository>();

builder.Services.AddMediatR(cfg =>
{
    cfg.RegisterServicesFromAssembly(typeof(Program).Assembly);
});
```

MediatR automatically discovers:

* CreateOrderHandler
* GetOrderHandler

No manual registration is required.

---

# 14. Complete Request Flow

## Create Order

```text
HTTP POST

↓

OrderController

↓

CreateOrderCommand

↓

IMediator.Send()

↓

CreateOrderHandler

↓

OrderRepository

↓

AppDbContext

↓

SQL Server

↓

Order Id

↓

Client
```

---

## Get Order

```text
HTTP GET

↓

OrderController

↓

GetOrderQuery

↓

IMediator.Send()

↓

GetOrderHandler

↓

OrderRepository

↓

AppDbContext

↓

SQL Server

↓

OrderDto

↓

Client
```

---

# 15. Internal MediatR Flow

Suppose the controller executes

```csharp
await _mediator.Send(command);
```

Internally MediatR performs:

```text
Receive Request

↓

Identify Request Type

↓

CreateOrderCommand

↓

Find IRequestHandler<CreateOrderCommand,int>

↓

Resolve CreateOrderHandler

↓

Execute Handle()

↓

Return Response
```

The controller never knows which handler executed.

---

# 16. CQRS vs Traditional Architecture

| Traditional           | CQRS                     |
| --------------------- | ------------------------ |
| One Service           | One Handler per Use Case |
| Mixed Read & Write    | Read and Write separated |
| Large Service Classes | Small Focused Classes    |
| Harder to Test        | Easier to Test           |
| Tightly Coupled       | Loosely Coupled          |

---

# 17. Advantages

* Separation of concerns
* Single Responsibility Principle
* Thin controllers
* Easier unit testing
* Better maintainability
* Better scalability
* Independent optimization of reads and writes
* Cleaner architecture

---

# 18. Best Practices

* Keep one handler for one use case.
* Return DTOs from queries.
* Do not expose EF entities directly.
* Use validation before executing handlers.
* Keep controllers thin.
* Keep handlers focused on business logic.
* Combine CQRS with Repository and Unit of Work when appropriate.
* Add Pipeline Behaviors for logging, validation, and performance.

---

# 19. Common Interview Questions

### What is CQRS?

CQRS separates commands (writes) from queries (reads) into different handlers.

---

### What is MediatR?

MediatR is a library implementing the Mediator Pattern that routes requests to their corresponding handlers.

---

### Why use MediatR?

* Decouples controllers from business logic.
* Simplifies dependency management.
* Makes handlers independently testable.
* Supports pipeline behaviors.

---

### Does CQRS require MediatR?

No.

CQRS is an architectural pattern.

MediatR is a library commonly used to implement it.

---

### Why inject IMediator instead of a Service?

Because controllers should only send requests.

The Mediator resolves and executes the appropriate handler.

---

### Can a Command return data?

Yes.

A command commonly returns:

* Created Id
* Success/Failure
* Result object

However, commands should not be used to retrieve complex read models.

---

# 20. Key Takeaways

* CQRS separates reads from writes.
* Commands modify data.
* Queries retrieve data.
* Every command has one handler.
* Every query has one handler.
* Controllers communicate only with `IMediator`.
* MediatR resolves the appropriate handler automatically.
* Handlers contain business logic.
* Repositories contain persistence logic.
* CQRS improves maintainability, testability, and scalability.
* Pipeline Behaviors can add logging, validation, authorization, caching, and performance monitoring without changing handlers.

---

# What's Next?

## README 3 – Enterprise CQRS + MediatR

Topics covered:

* Pipeline Behaviors
* FluentValidation Integration
* Global Exception Handling
* Logging Behavior
* Performance Behavior
* Authorization Behavior
* Unit of Work Integration
* Domain Events
* Notifications
* Transaction Management
* Complete Clean Architecture implementation

This is the architecture commonly used in enterprise banking, healthcare, insurance, and large e-commerce systems.
