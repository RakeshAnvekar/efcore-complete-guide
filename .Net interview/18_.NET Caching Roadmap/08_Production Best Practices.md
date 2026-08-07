# Caching in ASP.NET Core

# README 2 – IMemoryCache (Complete Guide)

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is IMemoryCache?
2. Why Do We Need IMemoryCache?
3. How IMemoryCache Works
4. Cache Hit vs Cache Miss
5. Project Architecture
6. Registering IMemoryCache
7. Dependency Injection
8. Reading from Cache
9. Storing Data in Cache
10. Complete Product Service Example
11. Cache Keys
12. Absolute Expiration
13. Sliding Expiration
14. Combining Expirations
15. Removing Cache
16. MemoryCacheEntryOptions
17. Cache Priority
18. Cache-Aside Pattern
19. When to Use IMemoryCache
20. When NOT to Use IMemoryCache
21. IMemoryCache Limitations
22. Multi-Server Problem
23. Enterprise Architecture
24. Best Practices
25. Common Mistakes
26. Code Review Checklist
27. Interview Questions
28. Key Takeaways
29. Next Module

---

# 1. What is IMemoryCache?

`IMemoryCache` is the built-in in-memory caching mechanism provided by ASP.NET Core.

It stores frequently accessed data inside the application's RAM.

Instead of querying the database for every request, the application first checks memory.

---

# 2. Why Do We Need IMemoryCache?

Without cache

```text
Client

↓

Controller

↓

Service

↓

Repository

↓

SQL Server

↓

Return Data
```

Every request hits SQL Server.

---

With cache

```text
Client

↓

Controller

↓

Memory Cache

↓

Cache Hit?

│

├── Yes

│

│ Return Data

│

└── No

↓

Repository

↓

SQL Server

↓

Store Cache

↓

Return Data
```

Database load is greatly reduced.

---

# 3. How IMemoryCache Works

Suppose the API is

```http
GET /api/products/100
```

Request Flow

```text
Client

↓

Product Controller

↓

Product Service

↓

Memory Cache

↓

Found?

↓

Return Product

OR

↓

Repository

↓

SQL Server

↓

Store Product

↓

Return Product
```

---

# 4. Cache Hit vs Cache Miss

## Cache Hit

```text
Request

↓

Memory Cache

↓

Product Found

↓

Return Product

↓

2 ms
```

---

## Cache Miss

```text
Request

↓

Memory Cache

↓

Not Found

↓

Database

↓

Store in Cache

↓

Return Product

↓

300 ms
```

---

# 5. Project Architecture

```text
Controllers

↓

Services

↓

Repositories

↓

SQL Server

↑

↓

IMemoryCache
```

The Service layer is usually responsible for cache management.

---

# 6. Registering IMemoryCache

Program.cs

```csharp
builder.Services.AddMemoryCache();
```

ASP.NET Core registers the service with the Dependency Injection container.

---

# 7. Dependency Injection

```csharp
public class ProductService
{
    private readonly IMemoryCache _cache;
    private readonly IProductRepository _repository;

    public ProductService(
        IMemoryCache cache,
        IProductRepository repository)
    {
        _cache = cache;
        _repository = repository;
    }
}
```

Never instantiate `MemoryCache` manually.

Always use DI.

---

# 8. Reading from Cache

```csharp
string cacheKey = $"Product_{id}";

if (_cache.TryGetValue(cacheKey, out Product product))
{
    return product;
}
```

### What does `TryGetValue()` do?

It checks whether the key exists.

If it exists

* Returns `true`
* Assigns the value to the `out` variable

If it does not exist

* Returns `false`
* No exception is thrown

---

# 9. Storing Data in Cache

```csharp
_cache.Set(cacheKey, product);
```

This stores

```text
Key

↓

Product_100

↓

Value

↓

Product Object
```

---

# 10. Complete Product Service Example

```csharp
public class ProductService
{
    private readonly IMemoryCache _cache;
    private readonly IProductRepository _repository;

    public ProductService(
        IMemoryCache cache,
        IProductRepository repository)
    {
        _cache = cache;
        _repository = repository;
    }

    public async Task<Product> GetProductAsync(int id)
    {
        string cacheKey = $"Product_{id}";

        if (_cache.TryGetValue(cacheKey, out Product product))
        {
            Console.WriteLine("Cache Hit");

            return product;
        }

        Console.WriteLine("Cache Miss");

        product = await _repository.GetByIdAsync(id);

        _cache.Set(cacheKey, product);

        return product;
    }
}
```

Execution

First request

```text
Request

↓

Cache Miss

↓

SQL Server

↓

Cache

↓

Return
```

Second request

```text
Request

↓

Cache Hit

↓

Return
```

No SQL query is executed.

---

# 11. Cache Keys

Always use descriptive keys.

Good

```text
Product_100

Customer_10

Order_500

CountryList

Settings
```

Bad

```text
1

abc

temp

cache
```

Meaningful keys make debugging much easier.

---

# 12. Absolute Expiration

```csharp
_cache.Set(
    cacheKey,
    product,
    TimeSpan.FromMinutes(10));
```

Behavior

```text
12:00

↓

Stored

↓

12:10

↓

Removed
```

Even if users continue reading the value, it expires after 10 minutes.

---

# 13. Sliding Expiration

```csharp
_cache.Set(
    cacheKey,
    product,
    new MemoryCacheEntryOptions
    {
        SlidingExpiration = TimeSpan.FromMinutes(5)
    });
```

Behavior

```text
12:00

Stored

↓

12:03

Accessed

↓

Expiry becomes

12:08

↓

12:06

Accessed Again

↓

Expiry becomes

12:11
```

Frequently used items remain cached.

---

# 14. Combining Expirations

Best practice

```csharp
_cache.Set(
    cacheKey,
    product,
    new MemoryCacheEntryOptions
    {
        SlidingExpiration =
            TimeSpan.FromMinutes(5),

        AbsoluteExpirationRelativeToNow =
            TimeSpan.FromHours(1)
    });
```

Meaning

* Keep the cache alive while it is actively used.
* Never keep it longer than one hour.

---

# 15. Removing Cache

When data changes

```csharp
await _repository.UpdateAsync(product);

_cache.Remove($"Product_{product.Id}");
```

Next request

↓

Reads fresh data from SQL Server.

Never leave stale cache after updates.

---

# 16. MemoryCacheEntryOptions

```csharp
var options =
    new MemoryCacheEntryOptions()
    {
        AbsoluteExpirationRelativeToNow =
            TimeSpan.FromMinutes(30),

        SlidingExpiration =
            TimeSpan.FromMinutes(5),

        Priority =
            CacheItemPriority.High
    };

_cache.Set(cacheKey, product, options);
```

---

# 17. Cache Priority

Available priorities

```text
Low

Normal

High

NeverRemove
```

If memory becomes low

```text
Low

↓

Removed First

↓

Normal

↓

High

↓

NeverRemove
```

Use `NeverRemove` sparingly.

---

# 18. Cache-Aside Pattern

The most common caching strategy.

```text
Client

↓

Application

↓

Cache

↓

Found?

│

├── Yes

│

│ Return

│

└── No

↓

Database

↓

Store Cache

↓

Return
```

This is the pattern most applications implement with `IMemoryCache`.

---

# 19. When to Use IMemoryCache

Good candidates

* Product Categories
* Country List
* Currency List
* Application Settings
* Tax Rates
* Feature Flags
* Configuration Data
* Lookup Tables

These are read frequently and change rarely.

---

# 20. When NOT to Use IMemoryCache

Avoid caching

* Bank Balance
* Shopping Cart
* Live Stock Prices
* OTP
* Payment Status
* Live Sports Scores
* Real-time Inventory

These values change frequently.

---

# 21. IMemoryCache Limitations

`IMemoryCache` exists only inside one application instance.

```text
Application

↓

RAM

↓

Cache
```

Restart the application

↓

Cache is lost.

---

# 22. Multi-Server Problem

Imagine

```text
Load Balancer

↓

Server A

Server B

Server C
```

Server A caches Product 100.

The next request goes to Server B.

```text
Server B

↓

Cache Miss
```

Each server maintains its own independent cache.

For multi-server deployments, prefer Redis or another distributed cache.

---

# 23. Enterprise Architecture

```text
React

↓

ASP.NET Core API

↓

Product Service

↓

IMemoryCache

↓

Hit?

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

Store in Cache

↓

Return Product
```

---

# 24. Best Practices

* Cache only frequently accessed data.
* Use meaningful cache keys.
* Always set an expiration.
* Remove cache after updates or deletes.
* Keep caching logic in the Service layer.
* Use `TryGetValue()` before querying the database.
* Combine Sliding and Absolute expiration when appropriate.
* Monitor cache size in production.

---

# 25. Common Mistakes

❌ Never setting expiration.

❌ Caching highly dynamic data.

❌ Using random cache keys.

❌ Forgetting to invalidate cache after updates.

❌ Putting cache logic inside controllers.

❌ Using `NeverRemove` for everything.

❌ Assuming cache is shared across servers.

---

# 26. Code Review Checklist

Before approving code ask:

* Is the cached data suitable for caching?
* Is the cache key meaningful?
* Is expiration configured?
* Is cache invalidated after updates?
* Is the Service layer managing cache?
* Is duplicate caching logic avoided?
* Will stale data cause business problems?

---

# 27. Common Interview Questions

### What is IMemoryCache?

A built-in ASP.NET Core service that stores data in application memory to reduce expensive operations such as database queries.

---

### Is IMemoryCache shared between multiple servers?

No.

Each application instance has its own memory cache.

---

### Difference between Absolute and Sliding Expiration?

| Absolute Expiration | Sliding Expiration                 |
| ------------------- | ---------------------------------- |
| Fixed lifetime      | Lifetime extends after each access |
| Never resets        | Resets every time the item is read |

---

### What is Cache-Aside?

The application checks the cache first. If the data is missing, it loads it from the database, stores it in the cache, and returns it.

---

### Why not cache everything?

Because frequently changing data becomes stale and can produce incorrect business results.

---

# 28. Key Takeaways

* `IMemoryCache` stores data in application RAM.
* It is extremely fast compared to database access.
* Always use Dependency Injection.
* Use `TryGetValue()` to check for cache hits.
* Set appropriate expiration policies.
* Remove cached data after updates.
* Use `IMemoryCache` only for single-server or non-shared caching scenarios.
* For distributed applications, prefer Redis.

---

# 29. Next Module

## README 3 – Redis Distributed Cache

Topics covered:

* What is Redis?
* Why Redis?
* Redis Architecture
* Installing Redis
* `IDistributedCache`
* StackExchange.Redis
* Serialization
* Cache-Aside with Redis
* Cache Invalidation
* High Availability
* Redis in Microservices
* Production Best Practices
* Complete .NET Examples
