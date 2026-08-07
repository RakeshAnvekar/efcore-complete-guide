# System Design Interview Handbook

# README – Complete Request Flows (Senior .NET Developer)

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. Production Architecture
2. Component Overview
3. Product Details Flow (Cache Hit)
4. Product Details Flow (Cache Miss)
5. User Login (JWT)
6. Authenticated API Request
7. Place Order Flow (Success)
8. Place Order Flow (Payment Failure)
9. Product Update (Cache Invalidation)
10. Notification Flow
11. Kafka Event Flow
12. End-to-End Request Journey
13. Interview Explanation Order

---

# 1. Production Architecture

```text
                                User
                                  │
                                  ▼
                              Browser
                                  │
                                  ▼
                           DNS Resolution
                                  │
                                  ▼
                         CDN (Static Files)
                                  │
                                  ▼
                   WAF (Web Application Firewall)
                                  │
                                  ▼
                           Load Balancer
                                  │
             ┌────────────────────┼────────────────────┐
             ▼                    ▼                    ▼
         API Server 1        API Server 2        API Server 3
                  │               │                 │
                  └───────────────┼─────────────────┘
                                  │
                            API Gateway
                                  │
      ┌───────────────┬───────────────┬───────────────┬───────────────┐
      ▼               ▼               ▼               ▼
 Product Service   Order Service  Inventory Service Payment Service
      │               │               │               │
      ▼               ▼               ▼               ▼
 Redis Cache      Redis Cache     Redis Cache     Redis Cache
      │               │               │               │
      ▼               ▼               ▼               ▼
 Product DB       Order DB      Inventory DB     Payment DB
                          │
                          ▼
                    Kafka / Event Bus
      ┌───────────────────┼───────────────────┐
      ▼                   ▼                   ▼
 Notification        Search Service      Analytics
```

---

# 2. Component Overview

## DNS

Purpose

Converts a domain name into an IP address.

Example

```
amazon.com

↓

52.xx.xx.xx
```

Without DNS, browsers wouldn't know where to send requests.

---

## CDN

Stores static content close to users.

Examples

* Images
* CSS
* JavaScript
* Fonts

Benefits

* Faster page loading
* Reduced application server load

---

## WAF

Filters malicious traffic.

Protects against

* SQL Injection
* Cross-Site Scripting (XSS)
* DDoS attacks
* Bot traffic

---

## Load Balancer

Distributes traffic across multiple servers.

Benefits

* High availability
* Better scalability
* Automatic failover

---

## API Gateway

Single entry point for all APIs.

Responsibilities

* Authentication
* Authorization
* Routing
* Rate limiting
* Logging
* Request aggregation

---

## Microservices

Each service owns one business capability.

Examples

* Product
* Order
* Inventory
* Payment

Each service has

* Business logic
* Its own database
* Its own Redis cache

---

## Redis

Distributed in-memory cache.

Purpose

Reduce database calls.

Common Pattern

Cache Aside

---

## SQL Database

Permanent storage.

Source of truth.

Redis is only a cache.

---

## Kafka

Asynchronous messaging platform.

Used for

* Notifications
* Inventory updates
* Analytics
* Search indexing

---

# 3. Product Details Request (Cache Hit)

```
User

↓

Browser

↓

DNS

↓

CDN

↓

WAF

↓

Load Balancer

↓

API Gateway

↓

Product Service

↓

Redis

↓

Cache Hit

↓

Return Product

↓

Browser Displays Product
```

Explanation

The product already exists in Redis.

No Repository call.

No SQL query.

Fast response.

---

# 4. Product Details Request (Cache Miss)

```
User

↓

Browser

↓

DNS

↓

CDN

↓

WAF

↓

Load Balancer

↓

API Gateway

↓

Product Service

↓

Redis

↓

Cache Miss

↓

Repository

↓

SQL Server

↓

Product Found?

│

├── No

│

└── Return 404

│

└── Yes

↓

Serialize Product

↓

Save to Redis

↓

Return Product

↓

Browser
```

Explanation

Database is queried only once.

Future requests come from Redis.

---

# 5. Login Flow (JWT)

```
User

↓

Login API

↓

Validate Credentials

↓

SQL Server

↓

Credentials Valid?

│

├── No

│

└── 401 Unauthorized

│

└── Yes

↓

Generate JWT

↓

Return JWT

↓

Browser Stores Token
```

---

# 6. Authenticated API Request

```
Browser

Authorization: Bearer JWT

↓

API Gateway

↓

Validate JWT

↓

Valid?

│

├── No

│

└── 401

│

└── Yes

↓

Product Service

↓

Return Product
```

---

# 7. Place Order (Success)

```
Customer

↓

API Gateway

↓

Order Service

↓

Validate Request

↓

Inventory Service

↓

Stock Available?

│

├── No

│

└── Out Of Stock

│

└── Yes

↓

Reserve Stock

↓

Payment Service

↓

Payment Success?

│

├── No

│

└── Release Stock

│

└── Yes

↓

Save Order

↓

Publish Kafka Event

↓

Notification

↓

Inventory Update

↓

Analytics

↓

Return Success
```

---

# 8. Payment Failure

```
Customer

↓

Order Service

↓

Reserve Stock

↓

Payment Service

↓

Payment Failed

↓

Release Reserved Stock

↓

Publish InventoryReleased Event

↓

Return Payment Failed
```

This is a compensation step in the **Saga Pattern**.

---

# 9. Product Update (Cache Invalidation)

```
Admin

↓

Product Service

↓

Update SQL

↓

Delete Redis Cache

↓

Publish ProductUpdated Event

↓

Kafka

↓

Search Service

↓

Recommendation Service

↓

Refresh Their Data
```

Always update the database first, then invalidate or refresh the cache.

---

# 10. Notification Flow

```
Order Created

↓

Kafka

↓

Notification Worker

↓

Generate Email

↓

SMTP Provider

↓

Customer Receives Email
```

If email fails

```
Notification Worker

↓

Retry

↓

Still Failed?

│

├── No

│

└── Success

│

└── Yes

↓

Dead Letter Queue
```

---

# 11. Kafka Event Flow

```
Order Service

↓

Kafka Topic

↓

Inventory Service

↓

Reduce Stock

↓

Notification Service

↓

Send Email

↓

Analytics Service

↓

Update Dashboard
```

Benefits

* Loose coupling
* Independent scaling
* Reliable asynchronous processing

---

# 12. Complete End-to-End Product Request

```
User

↓

Browser

↓

DNS

↓

CDN

↓

WAF

↓

Load Balancer

↓

API Gateway

↓

Product Service

↓

Redis

↓

Cache Hit?

│

├── Yes

│

└── Return Product

│

└── No

↓

Repository

↓

SQL Server

↓

Save to Redis

↓

Return Product

↓

Browser
```

This is the complete lifecycle of a read request.

---

# 13. How to Explain This in an Interview

Always explain in this order:

### Step 1

Clarify requirements.

---

### Step 2

Draw the high-level architecture.

---

### Step 3

Explain every component.

* DNS
* CDN
* WAF
* Load Balancer
* API Gateway
* Microservices
* Redis
* Database
* Kafka

---

### Step 4

Walk through one complete request.

Example:

User → Browser → DNS → Load Balancer → API Gateway → Product Service → Redis → SQL (if needed) → Response.

---

### Step 5

Explain write operations.

Example:

Order → Payment → Kafka → Notification → Inventory.

---

### Step 6

Explain failure handling.

* Retry
* Circuit Breaker
* Dead Letter Queue
* Saga Compensation

---

### Step 7

Explain scalability.

* Stateless APIs
* Multiple API instances
* Redis for caching
* Kafka for asynchronous processing
* Database per service

---

# Interview Tips

When explaining diagrams:

* Don't rush to technologies like Kafka or Redis first.
* Start with the user's request and follow it through the system.
* Explain **why** each component exists, not just **what** it is.
* Mention trade-offs where appropriate (for example, cache freshness vs. performance or synchronous vs. asynchronous communication).
* Use one complete request flow from start to finish before discussing optimizations.

If you can confidently explain every diagram in this README, you'll cover the core architecture patterns that appear in most Senior .NET system design interviews.
