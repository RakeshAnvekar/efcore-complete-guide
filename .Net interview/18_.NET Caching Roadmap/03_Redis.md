# Caching in ASP.NET Core

# README 3 – Redis Distributed Cache (Complete Guide)

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is Redis?
2. Why Redis?
3. Why IMemoryCache is Not Enough
4. Redis Architecture
5. Redis vs IMemoryCache
6. How Redis Works
7. Cache Hit vs Cache Miss
8. Installing Redis
9. Adding Redis to ASP.NET Core
10. Registering Redis
11. IDistributedCache
12. Why Serialization is Required
13. Writing Data to Redis
14. Reading Data from Redis
15. Complete ProductService Example
16. Cache Expiration
17. Cache Invalidation
18. Distributed Cache Architecture
19. Enterprise Architecture
20. Performance Benefits
21. Best Practices
22. Common Mistakes
23. Code Review Checklist
24. Interview Questions
25. Key Takeaways
26. Next Module

---

# 1. What is Redis?

Redis stands for

```text
REmote DIctionary Server
```

Redis is an **in-memory NoSQL data store** commonly used as a:

* Distributed Cache
* Session Store
* Message Broker
* Pub/Sub Engine
* Leaderboard Store

In ASP.NET Core, Redis is most commonly used as a **Distributed Cache**.

---

# 2. Why Redis?

Imagine an e-commerce website receiving

```text
100,000 Product Requests

Every Minute
```

If every request goes to SQL Server

```text
Client

↓

API

↓

SQL Server

↓

Return Product
```

The database becomes overloaded.

Instead

```text
Client

↓

API

↓

Redis

↓

Product

↓

Return
```

Only the first request needs SQL.

---

# 3. Why IMemoryCache is Not Enough?

Suppose we have

```text
Load Balancer

↓

API-1

API-2

API-3
```

Each server has its own memory.

```text
API-1

↓

IMemoryCache

-------------

API-2

↓

IMemoryCache

-------------

API-3

↓

IMemoryCache
```

Problem

Customer Request 1

↓

API-1

↓

Product Cached

Customer Request 2

↓

API-2

↓

Cache Miss

Every server has a different cache.

---

# 4. Redis Architecture

Redis is shared.

```text
                Load Balancer

                      │

        ┌─────────────┼─────────────┐

        ▼             ▼             ▼

     API-1         API-2         API-3

        │             │             │

        └─────────────┼─────────────┘

                      ▼

                 Redis Server

                      ▼

                 SQL Server
```

Every server reads the same cache.

---

# 5. Redis vs IMemoryCache

| Feature                    | IMemoryCache       | Redis              |
| -------------------------- | ------------------ | ------------------ |
| Location                   | Application Memory | Separate Server    |
| Shared Across Servers      | ❌ No               | ✅ Yes              |
| Suitable for Load Balancer | ❌ No               | ✅ Yes              |
| Distributed                | ❌ No               | ✅ Yes              |
| Restart Safe               | ❌ Cache Lost       | ✅ Can Persist Data |
| Speed                      | Very Fast          | Very Fast          |

---

# 6. How Redis Works

Suppose

```http
GET /api/products/100
```

Execution

```text
Client

↓

ASP.NET Core API

↓

Redis

↓

Cache Hit?

│

├── Yes

│

│ Return Product

│

└── No

↓

Repository

↓

SQL Server

↓

Serialize

↓

Redis

↓

Return Product
```

---

# 7. Cache Hit vs Cache Miss

## Cache Hit

```text
Request

↓

Redis

↓

Product Found

↓

Return

↓

2 ms
```

---

## Cache Miss

```text
Request

↓

Redis

↓

Product Missing

↓

SQL Server

↓

Store Product

↓

Return

↓

300 ms
```

---

# 8. Installing Redis

## Docker

```bash
docker run -d \
--name redis \
-p 6379:6379 \
redis
```

Redis starts on

```text
localhost:6379
```

---

# 9. Adding Redis to ASP.NET Core

Install package

```bash
dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis
```

---

# 10. Registering Redis

Program.cs

```csharp
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379";

    options.InstanceName = "ECommerce";
});
```

Now ASP.NET Core automatically registers

```text
IDistributedCache
```

---

# 11. IDistributedCache

Inject using Dependency Injection

```csharp
public class ProductService
{
    private readonly IDistributedCache _cache;

    public ProductService(IDistributedCache cache)
    {
        _cache = cache;
    }
}
```

Notice

IMemoryCache

↓

```csharp
IMemoryCache
```

Redis

↓

```csharp
IDistributedCache
```

---

# 12. Why Serialization is Required

Redis stores

* String
* Byte[]
* Integer
* Hash
* List
* Set

Redis cannot understand

```csharp
Product product
```

Therefore

```text
C# Object

↓

JSON

↓

Redis

↓

JSON

↓

C# Object
```

---

# 13. Writing Data to Redis

```csharp
Product product = new Product
{
    Id = 100,
    Name = "Laptop",
    Price = 65000
};

string json =
    JsonSerializer.Serialize(product);

await _cache.SetStringAsync(
    "Product_100",
    json);
```

Stored in Redis

```json
{
    "Id":100,
    "Name":"Laptop",
    "Price":65000
}
```

---

# 14. Reading Data from Redis

```csharp
string json =
    await _cache.GetStringAsync("Product_100");

if(json != null)
{
    Product product =
        JsonSerializer.Deserialize<Product>(json);
}
```

Flow

```text
Redis

↓

JSON

↓

Deserialize

↓

Product Object
```

---

# 15. Complete ProductService Example

```csharp
public class ProductService
{
    private readonly IDistributedCache _cache;
    private readonly IProductRepository _repository;

    public ProductService(
        IDistributedCache cache,
        IProductRepository repository)
    {
        _cache = cache;
        _repository = repository;
    }

    public async Task<Product> GetProductAsync(int id)
    {
        string cacheKey = $"Product_{id}";

        string json =
            await _cache.GetStringAsync(cacheKey);

        if(json != null)
        {
            Console.WriteLine("Cache Hit");

            return JsonSerializer.Deserialize<Product>(json);
        }

        Console.WriteLine("Cache Miss");

        Product product =
            await _repository.GetByIdAsync(id);

        json =
            JsonSerializer.Serialize(product);

        await _cache.SetStringAsync(cacheKey, json);

        return product;
    }
}
```

Execution

First Request

```text
Redis

↓

Miss

↓

SQL

↓

Redis

↓

Return
```

Second Request

```text
Redis

↓

Hit

↓

Return
```

---

# 16. Cache Expiration

```csharp
var options =
    new DistributedCacheEntryOptions
    {
        AbsoluteExpirationRelativeToNow =
            TimeSpan.FromMinutes(30)
    };

await _cache.SetStringAsync(
    cacheKey,
    json,
    options);
```

Redis removes the cache after 30 minutes.

---

# 17. Cache Invalidation

Suppose

Product Price Updated

Database

↓

65000 → 70000

Redis

↓

Still 65000

Wrong.

After update

```csharp
await _repository.UpdateAsync(product);

await _cache.RemoveAsync(
    $"Product_{product.Id}");
```

Next request

↓

Fresh SQL data

↓

Updated Redis

---

# 18. Distributed Cache Architecture

```text
Client

↓

API

↓

Redis

↓

Found?

│

├── Yes

│

│ Return

│

└── No

↓

Repository

↓

SQL

↓

Redis

↓

Return
```

This is the **Cache-Aside Pattern**, the most common caching pattern.

---

# 19. Enterprise Architecture

```text
                 React

                    │

                    ▼

              API Gateway

                    │

          Product Service

                    │

                    ▼

                Redis Cache

             Hit?       Miss?

              │            │

              ▼            ▼

          Return Data   SQL Server

                             │

                             ▼

                      Store in Redis

                             │

                             ▼

                        Return Product
```

---

# 20. Performance Benefits

Without Redis

```text
Every Request

↓

SQL Server

↓

250-500 ms
```

With Redis

```text
First Request

↓

SQL

↓

Redis

↓

Next Requests

↓

Redis

↓

1-5 ms
```

Benefits

* Faster responses
* Reduced SQL load
* Better scalability
* Improved user experience

---

# 21. Best Practices

* Cache frequently read data.
* Use meaningful cache keys.
* Always configure expiration.
* Remove cache after updates and deletes.
* Serialize complex objects as JSON.
* Keep caching logic in the Service layer.
* Use Redis for multi-server deployments.

---

# 22. Common Mistakes

❌ Caching everything.

❌ Forgetting cache invalidation.

❌ No expiration.

❌ Storing passwords or secrets.

❌ Using random cache keys.

❌ Assuming Redis replaces SQL Server.

❌ Calling SQL before checking Redis.

---

# 23. Code Review Checklist

Before approving code ask:

* Is Redis appropriate for this data?
* Is the cache key descriptive?
* Is expiration configured?
* Is JSON serialization handled correctly?
* Is cache invalidated after updates?
* Is SQL accessed only after a cache miss?
* Is caching implemented in the Service layer?

---

# 24. Common Interview Questions

### What is Redis?

Redis is an in-memory NoSQL data store commonly used as a distributed cache.

---

### Why Redis over IMemoryCache?

Redis is shared across all application servers, making it suitable for load-balanced and distributed environments.

---

### Why serialize objects?

Redis does not understand C# objects directly. Objects are converted to JSON (or bytes) before storing.

---

### Can Redis replace SQL Server?

No.

SQL Server remains the source of truth.

Redis is a cache to improve performance.

---

### What happens if Redis is restarted?

If persistence is disabled, cached data is lost. The application should treat Redis as a cache and reload data from SQL Server when needed.

---

### Which caching pattern does Redis commonly use?

**Cache-Aside Pattern.**

---

# 25. Key Takeaways

* Redis is a distributed in-memory cache.
* It is shared across all application servers.
* Redis improves performance by reducing database access.
* Store complex objects as JSON.
* Always configure expiration.
* Remove cache after updates.
* Keep SQL Server as the source of truth.
* Redis is the preferred caching solution for scalable ASP.NET Core applications.

---

# 26. Next Module

## README 4 – Cache-Aside Pattern (Production Implementation)

Topics include:

* Cache-Aside Architecture
* Read Flow
* Update Flow
* Delete Flow
* Cache Invalidation
* Repository Integration
* Redis + SQL Server
* E-Commerce Example
* Microservices Example
* Complete Production Code
* Common Pitfalls
* Interview Questions
