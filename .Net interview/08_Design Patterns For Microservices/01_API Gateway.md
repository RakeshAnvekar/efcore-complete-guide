# Module 8.1 – API Gateway Pattern

> **Course:** Microservices Architecture Master Roadmap

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. Introduction
3. Why API Gateway?
4. Problems Without API Gateway
5. What is API Gateway?
6. How API Gateway Works
7. Request Flow
8. Authentication
9. Routing
10. Load Balancing
11. Rate Limiting
12. Response Aggregation
13. Caching
14. ASP.NET Core API Gateway
15. Popular API Gateway Tools
16. Advantages
17. Disadvantages
18. Real Enterprise Architecture
19. Best Practices
20. Interview Questions
21. Interview Answers
22. Summary
23. Key Takeaways

---

# Learning Objectives

By the end of this module, you will understand:

- What an API Gateway is
- Why API Gateway is needed
- Problems solved by API Gateway
- Request routing
- Authentication
- Load Balancing
- Response Aggregation
- Rate Limiting
- Caching
- ASP.NET Core API Gateway
- Enterprise Architecture

---

# Introduction

In a Monolithic application,

the client talks to only one application.

```
Client

↓

Monolithic Application

↓

Database
```

Everything is simple.

However,

when we move to Microservices,

one application becomes many services.

Example

```
Order Service

Payment Service

Inventory Service

Customer Service

Shipping Service

Notification Service
```

Now,

the client has to communicate with multiple services.

This creates many challenges.

API Gateway solves these problems.

---

# Why API Gateway?

Suppose you are building Amazon.

Services

```
Order Service

Payment Service

Inventory Service

Shipping Service

Customer Service

Notification Service
```

The mobile application needs data from all these services.

Without API Gateway,

the mobile app must know every service.

---

# Without API Gateway

```
                    Mobile App
                  /    |      \
                 /     |       \
                ▼      ▼        ▼
          Order API  Payment API
                │          │
                ▼          ▼
         Inventory API  Shipping API
                │
                ▼
        Customer API
```

The frontend directly communicates with every service.

---

# Problems Without API Gateway

## Problem 1

Too many API calls.

Suppose Order Details page requires

```
Order

Customer

Inventory

Payment

Shipping
```

The frontend makes

```
5 HTTP Requests
```

One screen

↓

Five API calls.

---

## Problem 2

Frontend knows every URL.

```
orders.company.com

payment.company.com

inventory.company.com

shipping.company.com
```

If URLs change,

Frontend also changes.

---

## Problem 3

Authentication everywhere.

Every service validates JWT.

```
Order

↓

Validate JWT

Payment

↓

Validate JWT

Inventory

↓

Validate JWT
```

Same code is repeated.

---

## Problem 4

CORS configuration

Every service needs

- CORS
- HTTPS
- Headers
- Security
- Certificates

Repeated configuration.

---

## Problem 5

Different response formats.

Order API

```json
{
  "orderId":1
}
```

Customer API

```json
{
  "id":5
}
```

Product API

```json
{
  "product_name":"Laptop"
}
```

Frontend has to merge everything.

---

# Solution – API Gateway

Instead of

```
Client

↓

Every Service
```

Use

```
                 Client
                    │
                    ▼
              API Gateway
        ┌─────────┼─────────┐
        ▼         ▼         ▼
    Order     Payment   Inventory
        │         │         │
        ▼         ▼         ▼
 Customer   Shipping Notification
```

Now

Client knows only one endpoint.

---

# What is API Gateway?

An API Gateway is a **single entry point** for all client requests.

Instead of calling multiple services,

the client communicates only with the API Gateway.

The gateway decides

- Which service to call
- How to route requests
- Whether authentication is required
- Whether responses should be cached
- Whether multiple responses should be combined

---

# Request Flow

Suppose the client requests

```
GET /api/orders/1001
```

Flow

```
Client

↓

API Gateway

↓

Order Service

↓

API Gateway

↓

Client
```

The client never communicates directly with Order Service.

---

# Request Aggregation Example

Suppose the Order Details screen requires

- Order
- Customer
- Payment
- Inventory

Without Gateway

```
Client

↓

Order API

↓

Customer API

↓

Inventory API

↓

Payment API
```

Total

```
4 HTTP Requests
```

With Gateway

```
Client

↓

Gateway

↓

Order

Customer

Payment

Inventory

↓

Gateway combines response

↓

Client
```

Only one HTTP request.

---

# Authentication

Without Gateway

```
Client

↓

Order Service

↓

JWT Validation

↓

Payment Service

↓

JWT Validation

↓

Inventory Service

↓

JWT Validation
```

Every service validates JWT.

---

With Gateway

```
Client

↓

API Gateway

↓

JWT Validation

↓

Order

↓

Payment

↓

Inventory
```

Authentication happens once.

---

# Routing

Gateway Configuration

```
/orders

↓

Order Service
```

```
/payment

↓

Payment Service
```

```
/inventory

↓

Inventory Service
```

The client doesn't know where services are deployed.

---

# Load Balancing

Suppose

Order Service has three instances.

```
Order Service 1

Order Service 2

Order Service 3
```

Gateway distributes requests.

```
Client

↓

Gateway

↓

Instance 1

↓

Instance 2

↓

Instance 3
```

Load is shared.

---

# Rate Limiting

Suppose one client sends

```
1000 Requests / Minute
```

Gateway rule

```
Allow only

100 Requests / Minute
```

Extra requests receive

```
HTTP 429

Too Many Requests
```

This protects backend services.

---

# Caching

Frequently requested products.

Without Cache

```
Client

↓

Gateway

↓

Product Service

↓

Database
```

Every request reaches the database.

With Cache

```
Client

↓

Gateway Cache

↓

Return Cached Data
```

Faster responses.

Reduced database load.

---

# Request Transformation

Client sends

```
GET /products
```

Gateway converts it into

```
GET /api/v2/products
```

Backend services remain unchanged.

---

# Response Transformation

Suppose Product Service returns

```json
{
   "product_name":"Laptop"
}
```

Gateway transforms it to

```json
{
   "name":"Laptop"
}
```

Client receives a consistent response format.

---

# Logging

Instead of logging in every service,

Gateway logs

```
Incoming Request

Outgoing Request

Headers

IP Address

Response Time

Status Code
```

Centralized logging.

---

# Monitoring

Gateway can monitor

- Response Time
- Request Count
- Failed Requests
- Slow APIs
- Active Users

Very useful in production.

---

# ASP.NET Core API Gateway

Most popular gateways

## YARP

Microsoft's official reverse proxy.

```
Client

↓

YARP

↓

Microservices
```

Advantages

- High performance
- Microsoft supported
- Easy configuration
- ASP.NET Core integration

---

## Ocelot

Popular .NET API Gateway.

```
Client

↓

Ocelot

↓

Microservices
```

Supports

- Authentication
- Routing
- Rate Limiting
- Load Balancing

---

# Responsibilities of API Gateway

- Routing
- Authentication
- Authorization
- SSL Termination
- Rate Limiting
- Load Balancing
- Response Aggregation
- Request Transformation
- Response Transformation
- Caching
- Logging
- Monitoring

---

# Advantages

✔ Single Entry Point

✔ Simpler Frontend

✔ Centralized Authentication

✔ Better Security

✔ Load Balancing

✔ Response Aggregation

✔ Centralized Logging

✔ Centralized Monitoring

✔ Caching

✔ API Versioning

---

# Disadvantages

## Single Point of Failure

If Gateway fails,

all APIs become unavailable.

Solution

Deploy multiple Gateway instances.

---

## Additional Latency

Every request passes through one extra component.

Usually acceptable.

---

## More Infrastructure

One more service to maintain.

---

# Real Enterprise Architecture

```
                 Internet
                      │
                      ▼
             Azure Load Balancer
                      │
                      ▼
                API Gateway
        ┌──────────┼───────────┐
        ▼          ▼           ▼
   Order API   Payment API  Inventory API
        │          │           │
        ▼          ▼           ▼
   SQL Server  Payment DB  Inventory DB
```

This architecture is commonly used in enterprise applications.

---

# Popular API Gateway Tools

| Tool | Platform |
|------|----------|
| YARP | ASP.NET Core |
| Ocelot | ASP.NET Core |
| Kong | Multi-platform |
| NGINX | Reverse Proxy |
| Azure API Management | Azure |
| AWS API Gateway | AWS |
| Envoy | Service Mesh |
| Traefik | Kubernetes |

---

# Best Practices

✔ Keep Gateway lightweight.

✔ Do not place business logic inside the Gateway.

✔ Centralize authentication.

✔ Enable logging and monitoring.

✔ Configure rate limiting.

✔ Use caching where appropriate.

✔ Use HTTPS everywhere.

✔ Deploy multiple Gateway instances for high availability.

---

# Interview Questions

## Question 1

What is API Gateway?

---

## Question 2

Why do Microservices need API Gateway?

---

## Question 3

What responsibilities does API Gateway have?

---

## Question 4

Difference between API Gateway and Load Balancer?

---

## Question 5

Which API Gateway is commonly used in ASP.NET Core?

---

## Question 6

What is Response Aggregation?

---

## Question 7

Why is JWT validation usually performed in the Gateway?

---

# Interview Answers

## What is API Gateway?

API Gateway is a single entry point that receives client requests and routes them to appropriate microservices while handling cross-cutting concerns such as authentication, routing, caching, logging, and rate limiting.

---

## Why is API Gateway needed?

It simplifies client communication, centralizes security, hides internal services, reduces the number of client requests, and provides a unified API.

---

## API Gateway vs Load Balancer

API Gateway

- Routes requests
- Authentication
- Rate Limiting
- Response Aggregation
- Logging
- Caching

Load Balancer

- Distributes requests across service instances
- Improves availability
- Does not usually perform API-specific processing

---

## Which API Gateway is recommended for ASP.NET Core?

Microsoft YARP is increasingly used for modern ASP.NET Core applications.

Ocelot is also widely used.

---

## What is Response Aggregation?

The Gateway collects responses from multiple microservices, combines them, and returns a single response to the client.

---

## Why validate JWT at the Gateway?

Validating the JWT once at the gateway reduces duplicated authentication logic in each microservice and centralizes security.

---

# Summary

API Gateway is one of the most important patterns in Microservices Architecture.

It acts as a single entry point for clients and provides centralized routing, authentication, rate limiting, logging, caching, response aggregation, and monitoring.

Modern enterprise ASP.NET Core applications commonly use API Gateways such as **YARP** or **Ocelot** to simplify client communication and improve maintainability.

---

# Key Takeaways

✔ API Gateway is the single entry point for all client requests.

✔ Clients no longer communicate directly with microservices.

✔ API Gateway handles routing, authentication, authorization, logging, caching, and monitoring.

✔ Response Aggregation reduces the number of client API calls.

✔ YARP and Ocelot are popular API Gateway solutions in ASP.NET Core.

✔ API Gateway is one of the most frequently asked Microservices interview topics.