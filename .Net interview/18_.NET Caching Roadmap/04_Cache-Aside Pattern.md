# .NET Interview Guide – Cache-Aside Pattern (Single Server & Multi-Server)

## What is Cache-Aside Pattern?

Cache-Aside is the most commonly used caching pattern in .NET applications.

The application:

1. Checks Redis Cache.
2. If data exists → Return cached data.
3. If data does not exist → Read from Database.
4. Store data in Redis.
5. Return response.

---

## Architecture

```text
Request
   ↓
Redis Cache
   ↓
Cache Hit?
   ↓
 Yes -----------------> Return Response
   ↓
 No
   ↓
Database
   ↓
Store in Redis
   ↓
Return Response
```

---

# Basic Cache-Aside Implementation

```csharp
public async Task<Product?> GetProductAsync(int productId)
{
    string cacheKey = $"product:{productId}";

    var cachedData =
        await _cache.GetStringAsync(cacheKey);

    if (!string.IsNullOrEmpty(cachedData))
    {
        return JsonSerializer
            .Deserialize<Product>(cachedData);
    }

    var product =
        await _repository.GetByIdAsync(productId);

    if(product == null)
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

# Problem – Cache Stampede

Imagine:

```text
Cache Entry Expired
```

Suddenly:

```text
1000 Users Request Product 101
```

Without protection:

```text
1000 Requests
      ↓
1000 Cache Misses
      ↓
1000 Database Calls
```

Database becomes overloaded.

This is called:

```text
Cache Stampede
```

---

# Single Server Approach

## Scenario

```text
Users
   ↓
ASP.NET Core API
   ↓
Redis
   ↓
SQL Server
```

Only one API server exists.

---

## Solution

Use:

```csharp
SemaphoreSlim
```

---

## Why SemaphoreSlim?

SemaphoreSlim allows only one request to rebuild the cache.

```csharp
private static readonly SemaphoreSlim _lock =
    new SemaphoreSlim(1,1);
```

---

## Implementation

```csharp
public async Task<Product?> GetProductAsync(int productId)
{
    string cacheKey =
        $"product:{productId}";

    var cached =
        await _cache.GetStringAsync(cacheKey);

    if (!string.IsNullOrEmpty(cached))
    {
        return JsonSerializer
            .Deserialize<Product>(cached);
    }

    await _lock.WaitAsync();

    try
    {
        cached =
            await _cache.GetStringAsync(cacheKey);

        if (!string.IsNullOrEmpty(cached))
        {
            return JsonSerializer
                .Deserialize<Product>(cached);
        }

        var product =
            await _repository.GetByIdAsync(productId);

        if(product == null)
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
    finally
    {
        _lock.Release();
    }
}
```

---

## Flow

```text
Request 1
    ↓
Cache Miss
    ↓
Gets Semaphore Lock
    ↓
Database Call
    ↓
Update Cache
    ↓
Release Lock

Request 2
    ↓
Waits
    ↓
Reads Cache

Request 3
    ↓
Waits
    ↓
Reads Cache
```

---

## Result

Without SemaphoreSlim:

```text
100 Requests
      ↓
100 Database Calls
```

With SemaphoreSlim:

```text
100 Requests
      ↓
1 Database Call
      ↓
99 Cache Reads
```

---

# Limitation of SemaphoreSlim

SemaphoreSlim only works within a single application instance.

Example:

```text
Server 1
 └─ SemaphoreSlim
```

If you add more servers:

```text
Server 1
Server 2
Server 3
```

Each server gets its own SemaphoreSlim.

---

# Multi-Server Approach

## Scenario

```text
                 Load Balancer
                        |
        ---------------------------------
        |               |              |
     API-1          API-2          API-3
                        |
                      Redis
                        |
                     Database
```

---

## Problem

Suppose cache expires.

Requests arrive:

```text
User A → API-1
User B → API-2
User C → API-3
```

All servers get:

```text
Cache Miss
```

Each server has its own SemaphoreSlim.

Result:

```text
API-1 → Database
API-2 → Database
API-3 → Database
```

Multiple database calls occur.

---

# Solution – Distributed Lock

Instead of storing lock in server memory:

```text
Server Memory
```

Store lock in Redis.

```text
Redis
 └─ lock:product:101
```

Now all servers share the same lock.

---

## Distributed Lock Flow

### API-1

```text
Acquire Lock
      ↓
Success
      ↓
Database Call
```

---

### API-2

```text
Acquire Lock
      ↓
Failed
      ↓
Wait
```

---

### API-3

```text
Acquire Lock
      ↓
Failed
      ↓
Wait
```

---

### API-1 Finishes

```text
Update Cache
      ↓
Release Lock
```

---

### API-2

```text
Reads Cache
```

No database call.

---

## Redis Lock Example

Acquire Lock:

```redis
SET lock:product:101 unique-id NX EX 30
```

Meaning:

| Option | Meaning |
|----------|----------|
| NX | Create only if not exists |
| EX 30 | Expire after 30 seconds |
| unique-id | Lock owner |

---

## Result

Without Distributed Lock:

```text
3 Servers
     ↓
3 Database Calls
```

With Distributed Lock:

```text
3 Servers
     ↓
1 Database Call
     ↓
2 Cache Reads
```

---

# RedLock

RedLock is a Redis-based Distributed Lock implementation.

Popular NuGet:

```text
RedLock.net
```

---

## Example

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

# Comparison

| Feature | SemaphoreSlim | Distributed Lock | RedLock |
|----------|--------------|------------------|----------|
| Single Server | ✅ | ✅ | ✅ |
| Multi Server | ❌ | ✅ | ✅ |
| Async Support | ✅ | ✅ | ✅ |
| Uses Redis | ❌ | Optional | ✅ |
| Prevent Cache Stampede | ✅ | ✅ | ✅ |
| Production Microservices | ❌ | ✅ | ✅ |

---

# Cache Invalidation

After updating the database:

```csharp
public async Task UpdateProductAsync(Product product)
{
    await _repository.UpdateAsync(product);

    await _cache.RemoveAsync(
        $"product:{product.Id}");
}
```

This ensures stale cache is removed.

---

# Interview Questions

## What is Cache-Aside Pattern?

A caching strategy where the application first checks the cache. If data is unavailable, it loads data from the database, stores it in the cache, and returns the result.

---

## What is Cache Stampede?

A situation where many requests simultaneously miss the cache and hit the database.

---

## How do you prevent Cache Stampede on a single server?

Using:

```csharp
SemaphoreSlim
```

---

## Why can't SemaphoreSlim solve multi-server problems?

Because SemaphoreSlim exists only in application memory. Each server has its own instance and cannot coordinate with other servers.

---

## How do you prevent Cache Stampede in multi-server environments?

Using:

```text
Distributed Lock
```

or

```text
RedLock
```

stored in Redis.

---

# Final Interview Answer

For a single ASP.NET Core server, I use the Cache-Aside Pattern with Redis and protect cache rebuilds using `SemaphoreSlim` to prevent cache stampede. For a scaled-out environment with multiple API servers, `SemaphoreSlim` is insufficient because each server has its own memory. In that case, I use a Distributed Lock or Redis-based RedLock so that only one server rebuilds the cache while the others wait and then read the newly cached data. This reduces database load, improves scalability, and prevents cache stampede in distributed systems.