# Module 10.1 – Database per Service Pattern

> **Course:** ASP.NET Core Microservices with .NET

**Module:** Database per Service Pattern

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. What is Database per Service?
3. Why Do We Need Database per Service?
4. Monolithic vs Microservices Database
5. Problems with Shared Database
6. Database per Service Architecture
7. How Services Communicate
8. Why Services Cannot Access Other Databases
9. Data Duplication
10. Technology Flexibility
11. Database Ownership
12. Database per Service with Saga
13. Database per Service with Outbox
14. Advantages
15. Disadvantages
16. Enterprise Best Practices
17. Interview Questions
18. Summary
19. Key Takeaways

---

# Learning Objectives

By the end of this module, you will understand

- What is Database per Service
- Why every Microservice should own its own database
- Problems with Shared Database
- How services communicate
- Why data duplication is acceptable
- Enterprise implementation
- Interview concepts

---

# What is Database per Service?

Database per Service is one of the fundamental design patterns in Microservices Architecture.

It states

> Every Microservice should own and manage its own database.

No other Microservice is allowed to directly access that database.

Each service owns

- Database
- Tables
- Views
- Stored Procedures
- Indexes
- Migrations

---

# Why Do We Need Database per Service?

Suppose we have an E-Commerce application.

Modules

- Order
- Payment
- Inventory
- Shipping

In a Monolithic application,

all modules usually share one database.

```
                 E-Commerce Application

      Order Module

      Payment Module

      Inventory Module

      Shipping Module

                  │

                  ▼

             Shared Database
```

Database

```
Orders

Payments

Inventory

Shipping
```

Everything shares the same database.

---

# Problems with Shared Database

Although a shared database looks simple,

it creates many problems.

---

## Problem 1 – Tight Coupling

Suppose Order Team changes

```
Orders

CustomerName

↓

CustomerFullName
```

Payment Service still uses

```
CustomerName
```

Application breaks.

One team's change affects another team.

---

## Problem 2 – Independent Deployment

Suppose Order Team wants to deploy a new feature.

Because everyone shares the same database,

they must coordinate with

- Payment Team
- Inventory Team
- Shipping Team

Deployment becomes slow.

---

## Problem 3 – Security

Suppose Payment Service accidentally executes

```sql
DELETE FROM Orders
```

Order data is deleted.

One service should never be able to modify another service's data.

---

## Problem 4 – Technology Lock

Suppose

Order Team prefers

```
SQL Server
```

Payment Team prefers

```
PostgreSQL
```

Inventory Team prefers

```
MongoDB
```

A shared database prevents teams from choosing the best database for their service.

---

# Microservices Solution

Instead of one shared database,

every service owns its own database.

```
                Order Service

                     │

                 OrderDB
```

```
               Payment Service

                     │

                PaymentDB
```

```
              Inventory Service

                     │

               InventoryDB
```

```
              Shipping Service

                     │

               ShippingDB
```

Every service owns only its own database.

---

# Real Enterprise Example

Suppose a customer places an order.

Order Service stores

```
OrderDB

Orders
```

Payment Service stores

```
PaymentDB

Payments
```

Inventory Service stores

```
InventoryDB

Products
```

Shipping Service stores

```
ShippingDB

Shipments
```

Each database belongs to exactly one service.

---

# Database Ownership

Each service is responsible for

```
Tables

Indexes

Stored Procedures

Views

Migrations

Constraints
```

No other service should modify these objects.

---

# Can One Service Access Another Database?

The answer is

**No.**

Example

Wrong Design

```
Payment Service

↓

SELECT * FROM OrderDB
```

This creates tight coupling.

Instead,

Payment Service should communicate using APIs or Events.

---

# Correct Communication

Option 1

REST API

```
Payment Service

↓

Order Service API
```

---

Option 2

Kafka Event

```
Order Service

↓

Kafka

↓

Payment Service
```

This is the preferred approach in Event-Driven Architecture.

---

# Example Using Kafka

Customer places an order.

Order Service

```
Save Order

↓

OrderDB

↓

Publish Event

↓

Kafka

↓

OrderCreated
```

Payment Service

```
Receive Event

↓

PaymentDB

↓

Create Payment Record
```

Notice

Payment Service never queries OrderDB.

---

# Why Is This Better?

Services become independent.

Order Service only knows

```
OrderDB
```

Payment Service only knows

```
PaymentDB
```

Communication happens through business events.

---

# Data Duplication

Suppose Inventory Service also needs Customer Name.

Should it query OrderDB?

No.

Instead,

Order Service publishes

```json
{
    "OrderId":1001,
    "CustomerName":"Rakesh",
    "Product":"Laptop"
}
```

Inventory Service stores only the information it needs.

Example

OrderDB

```
OrderId

CustomerName

Product
```

InventoryDB

```
OrderId

CustomerName

Product
```

CustomerName exists in both databases.

This is called

```
Controlled Data Duplication
```

It is completely acceptable in Microservices.

---

# Why Is Data Duplication Accepted?

Storage is inexpensive.

Tight coupling is expensive.

Duplicating a few fields is much better than tightly coupling services.

---

# Technology Flexibility

One of the biggest advantages of Database per Service is technology freedom.

Example

```
Order Service

↓

SQL Server
```

```
Payment Service

↓

PostgreSQL
```

```
Inventory Service

↓

MongoDB
```

```
Shipping Service

↓

Cosmos DB
```

Every team chooses the database best suited to its requirements.

---

# Enterprise Architecture

```
                   Customer

                       │

                       ▼

                 API Gateway

                       │

      ┌──────────┬──────────┬──────────┬──────────┐

      ▼          ▼          ▼          ▼

   Order      Payment    Inventory   Shipping

   Service     Service      Service     Service

      │          │            │            │

  OrderDB   PaymentDB   InventoryDB  ShippingDB
```

Every service owns

- Business Logic
- Database
- APIs
- Events

---

# Database per Service with Saga

Customer places an order.

```
Order Service

↓

OrderDB

↓

Kafka

↓

Payment Service

↓

PaymentDB

↓

Kafka

↓

Inventory Service

↓

InventoryDB

↓

Kafka

↓

Shipping Service

↓

ShippingDB
```

Every service updates only its own database.

Saga coordinates the overall business transaction.

---

# Database per Service with Outbox

Each service maintains its own Outbox table.

Order Service

```
OrderDB

Orders

OutboxMessages
```

Payment Service

```
PaymentDB

Payments

OutboxMessages
```

Inventory Service

```
InventoryDB

Inventory

OutboxMessages
```

Each Outbox table belongs to its respective service.

---

# Advantages

✔ Loose Coupling

✔ Independent Deployment

✔ Independent Scaling

✔ Better Security

✔ Technology Flexibility

✔ Fault Isolation

✔ Team Independence

✔ Easier Maintenance

---

# Disadvantages

✖ Data Duplication

✖ Eventual Consistency

✖ More Databases to Manage

✖ Distributed Transactions

✖ More Infrastructure

---

# Enterprise Best Practices

✔ Every service owns exactly one database.

✔ Never allow direct database access between services.

✔ Communicate using REST APIs or Events.

✔ Use Saga Pattern for distributed transactions.

✔ Use Outbox Pattern for reliable event publishing.

✔ Accept controlled data duplication.

✔ Each team manages its own schema and migrations.

---

# Interview Questions

## What is Database per Service?

Database per Service is a Microservices design pattern where every service owns and manages its own private database.

---

## Why shouldn't Microservices share a database?

Because it creates tight coupling, prevents independent deployment, increases security risks, and reduces scalability.

---

## Can Payment Service query OrderDB directly?

No.

Payment Service should communicate with Order Service using REST APIs or asynchronous events.

---

## Is data duplication acceptable?

Yes.

Controlled data duplication is acceptable because it keeps services independent and loosely coupled.

---

## Can different services use different databases?

Yes.

One service can use SQL Server, another PostgreSQL, another MongoDB, depending on business requirements.

---

## How does Database per Service work with Saga?

Each service updates only its own database.

Saga coordinates the complete business workflow using events.

---

## How does Database per Service work with Outbox?

Each service has its own Outbox table in its own database.

The Outbox table is used to reliably publish events after the local database transaction succeeds.

---

# Summary

Database per Service is one of the core principles of Microservices Architecture.

Every service owns its own database and no other service is allowed to access it directly.

Services communicate through APIs or asynchronous events instead of sharing data.

This provides loose coupling, independent deployment, better scalability, and improved fault isolation.

Database per Service is commonly used together with Saga and Outbox patterns in enterprise applications.

---

# Key Takeaways

✔ Every Microservice owns its own database.

✔ No direct database access between services.

✔ Communication happens through APIs or Events.

✔ Shared databases create tight coupling.

✔ Controlled data duplication is acceptable.

✔ Database per Service enables independent deployment and scaling.

✔ Database per Service works naturally with Saga and Outbox patterns.

---

# Next Module

## Module 10.2 – API Composition Pattern

In the next module, you will learn

- What is API Composition?
- Why do we need it?
- Aggregator Service
- API Gateway Aggregation
- Real-world examples
- ASP.NET Core implementation
- Difference between API Composition and CQRS
- Enterprise best practices