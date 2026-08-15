# .NET Microservices Caching - Complete Interview Notes

## Table of Contents

1. Introduction to Caching
2. Why Caching in Microservices?
3. Cache Types
4. Cache-Aside Pattern
5. Distributed Cache vs In-Memory Cache
6. Cache Invalidation
7. Event-Driven Cache Invalidation
8. Cache Stampede
9. Distributed Locks
10. Output Caching
11. CQRS with Caching
12. Redis Best Practices
13. Common Interview Questions

---

# 1. Introduction to Caching

Caching is a technique used to store frequently accessed data in a fast storage layer so that future requests can be served quickly without repeatedly accessing the database.

Benefits:

- Faster response times
- Reduced database load
- Better scalability
- Improved user experience

---

# 2. Why Caching in Microservices?

Consider a Product Service:

```text
Request
   ↓
Product Service
   ↓
SQL Server
```

For every request:

```text
10000 Requests
      ↓
10000 Database Calls
```

Database becomes the bottleneck.

With Redis:

```text
Request
   ↓
Redis
   ↓
Cache Hit?
   ↓
Yes → Return Data
   ↓
No
   ↓
Database
   ↓
Store In Redis
```

Benefits:

- Reduced DB traffic
- Faster responses
- Better scalability

---

# 3. Cache Types

## Local Cache (IMemoryCache)

Stored inside application memory.

```text
Server
  └─ IMemoryCache
```

Example:

```csharp
services.AddMemoryCache();
```

### Advantages

- Extremely fast
- No network calls

### Disadvantages

- Not shared across servers
- Lost when application restarts

---

## Distributed Cache (Redis)

Stored outside application memory.

```text
API
 ↓
Redis
 ↓
Database
```

Example:

```csharp
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379";
    options.InstanceName = "ECommerce_";
});
```

### Advantages

- Shared by all servers
- Survives application restarts
- Scalable

---

# 4. Cache-Aside Pattern

Most common caching pattern.

## Flow

```text
Request
   ↓
Redis
   ↓
Cache Hit?
   ↓
Yes → Return Response
   ↓
No
   ↓
Database
   ↓
Store In Redis
   ↓
Return Response
```

---

## Example

```csharp
public async Task<Product?> GetProductAsync(int id)
{
    string cacheKey = $"product:{id}";

    var cachedData =
        await _cache.GetStringAsync(cacheKey);

    if (!string.IsNullOrEmpty(cachedData))
    {
        return JsonSerializer.Deserialize<Product>(
            cachedData);
    }

    var product =
        await _repository.GetByIdAsync(id);

    if (product == null)
        return null;

    await _cache.SetStringAsync(
        cacheKey,
        JsonSerializer.Serialize(product),
        new DistributedCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow =
                TimeSpan.FromMinutes(15)
        });

    return product;
}
```

---

# 5. Distributed Cache vs IMemoryCache

## IMemoryCache

```text
API-1
 └─ Cache

API-2
 └─ Cache

API-3
 └─ Cache
```

Problem:

```text
Product Updated
```

API-1:

```text
New Value
```

API-2:

```text
Old Value
```

Inconsistent data.

---

## Redis

```text
API-1
API-2
API-3
   ↓
 Redis
```

Single source of truth.

---

# 6. Cache Invalidation

One of the hardest problems in software engineering.

Suppose:

```text
Database Price = ₹120
```

Redis:

```text
Price = ₹100
```

Stale data.

---

## Solution

After update:

```csharp
public async Task UpdateProductAsync(Product product)
{
    await _repository.UpdateAsync(product);

    await _cache.RemoveAsync(
        $"product:{product.Id}");
}
```

---

## Flow

```text
Update Product
      ↓
Update Database
      ↓
Remove Redis Cache
```

Next request rebuilds cache.

---

# 7. Event-Driven Cache Invalidation

Used in Microservices.

## Example

```text
Product Service
Order Service
Inventory Service
```

Product updated.

Publish:

```text
ProductUpdatedEvent
```

Using:

- Kafka
- RabbitMQ
- Azure Service Bus

---

## Flow

```text
Product Updated
      ↓
Publish Event
      ↓
Message Broker
      ↓
Consumer Services
      ↓
Invalidate Cache
```

---

## Example

```text
Product Service
      ↓
Kafka
      ↓
Order Service
Inventory Service
```

Each service removes stale cache.

---

# 8. Cache Stampede

## Problem

Cache expires.

Suddenly:

```text
1000 Requests
      ↓
Cache Miss
      ↓
Database
```

Without protection:

```text
1000 Database Calls
```

---

# Single Server Solution

Use:

```csharp
SemaphoreSlim
```

Example:

```csharp
private static readonly SemaphoreSlim _lock =
    new SemaphoreSlim(1,1);
```

---

## Flow

```text
Request 1
    ↓
Database
    ↓
Update Cache

Request 2
    ↓
Wait

Request 3
    ↓
Wait
```

Result:

```text
1 Database Call
```

---

# 9. Distributed Locks

SemaphoreSlim works only on one server.

Microservice deployment:

```text
Load Balancer
      |
-------------------
|        |        |
API1    API2    API3
```

Each server has its own SemaphoreSlim.

Problem:

```text
API1 → DB
API2 → DB
API3 → DB
```

---

## Distributed Lock

Store lock in Redis.

```text
Redis
 └─ lock:product:101
```

---

## Flow

```text
API1
 ↓
Acquire Lock
 ↓
Success
 ↓
Database

API2
 ↓
Lock Exists
 ↓
Wait

API3
 ↓
Lock Exists
 ↓
Wait
```

Only one server rebuilds cache.

---

# RedLock

Popular Redis-based distributed lock implementation.

Package:

```text
RedLock.net
```

Example:

```csharp
await using var redLock =
    await redLockFactory.CreateLockAsync(
        $"product:{id}",
        TimeSpan.FromSeconds(30));

if(redLock.IsAcquired)
{
    // Rebuild cache
}
```

---

# 10. Output Caching

Caches entire HTTP responses.

Example:

```csharp
[OutputCache(Duration = 60)]
[HttpGet("{id}")]
public async Task<IActionResult> Get(int id)
{
    return Ok(await _service.GetAsync(id));
}
```

---

## Flow

```text
Request
   ↓
Output Cache
   ↓
Cache Hit
   ↓
Return Response
```

Controller not executed.

---

## Vary By Query

```csharp
[OutputCache(
    Duration = 60,
    VaryByQueryKeys = new[] { "productId" })]
```

Different cache entries:

```text
/products?productId=101
/products?productId=102
```

---

## Vary By Header

```csharp
[OutputCache(
    Duration = 60,
    VaryByHeaderNames =
        new[] { "Accept-Language" })]
```

Different cache entries:

```text
English
French
```

---

# 11. CQRS with Cache

Common architecture.

```text
Commands
    ↓
Database

Queries
    ↓
Redis
```

---

## Flow

```text
Read Request
     ↓
Redis

Write Request
     ↓
Database
     ↓
Invalidate Cache
```

---

# 12. Redis Best Practices

## Use Meaningful Keys

Good:

```text
product:101
user:5001
order:9001
```

Bad:

```text
101
5001
9001
```

---

## Set Expiration

```csharp
AbsoluteExpirationRelativeToNow =
    TimeSpan.FromMinutes(15)
```

Never keep data forever unless required.

---

## Serialize Objects

```csharp
var json =
    JsonSerializer.Serialize(product);
```

Store JSON in Redis.

---

## Use Compression

Large objects:

```text
Reports
PDF Metadata
Large Responses
```

Can be compressed before caching.

---

# 13. Common Interview Questions

## What is Cache-Aside Pattern?

Application first checks cache. On cache miss, it loads data from database, stores it in cache, and returns the result.

---

## Why Redis Instead of IMemoryCache?

Redis is shared across all application instances and survives application restarts.

---

## What is Cache Stampede?

Multiple requests simultaneously miss cache and overload the database.

---

## How Do You Prevent Cache Stampede?

Single Server:

```text
SemaphoreSlim
```

Multi Server:

```text
Distributed Lock
RedLock
```

---

## What is Cache Invalidation?

Removing or updating cached data when underlying data changes.

---

## What is Event-Driven Cache Invalidation?

Publishing events after data changes and allowing other microservices to remove stale cache.

---

## What is Output Cache?

Caching complete HTTP responses so that the controller is not executed for repeated requests.

---

# Interview Summary

```text
Most Common Pattern
    ↓
Cache-Aside

Single Server Stampede Prevention
    ↓
SemaphoreSlim

Multi Server Stampede Prevention
    ↓
Distributed Lock / RedLock

Shared Cache
    ↓
Redis

Cache Invalidation
    ↓
Remove Cache After Update

Microservices
    ↓
Event-Driven Invalidation
    ↓
Kafka / RabbitMQ / Azure Service Bus

Response Caching
    ↓
Output Cache
```

A strong .NET interview answer should cover:

- Cache-Aside Pattern
- Redis Distributed Cache
- Cache Invalidation
- Cache Stampede
- SemaphoreSlim
- Distributed Locks / RedLock
- Event-Driven Cache Invalidation
- Output Cache
- CQRS + Redis