# Caching in ASP.NET Core

# README 4 – Output Cache (Complete Guide)

> **Interview Level:** Senior / Lead .NET Developer

> **Supported:** .NET 7, .NET 8, .NET 9

---

# Table of Contents

1. What is Output Cache?
2. Why Output Cache?
3. How Output Cache Works
4. Output Cache Request Flow
5. Output Cache vs IMemoryCache
6. Output Cache vs Redis
7. Output Cache vs Response Cache
8. Registering Output Cache
9. Enabling Output Cache
10. Cache Duration
11. Cache Policies
12. Vary By Route
13. Vary By Query String
14. Vary By Header
15. Vary By Value
16. Cache Tags
17. Cache Invalidation
18. Enterprise Architecture
19. Performance Benefits
20. Best Practices
21. Common Mistakes
22. Code Review Checklist
23. Interview Questions
24. Key Takeaways
25. Next Module

---

# 1. What is Output Cache?

Output Cache is a server-side caching feature introduced in ASP.NET Core.

Instead of caching objects, Output Cache stores the **entire HTTP response**.

Example

```http
GET /api/products
```

Response

```json
[
    {
        "id":1,
        "name":"Laptop",
        "price":65000
    }
]
```

This complete JSON response is cached.

---

# 2. Why Output Cache?

Without Output Cache

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

JSON Serialization

↓

Return Response
```

Every request executes the full pipeline.

---

With Output Cache

```text
First Request

↓

Controller

↓

Service

↓

Repository

↓

SQL

↓

Generate JSON

↓

Store Response

↓

Client
```

Second Request

```text
Client

↓

Output Cache

↓

Return Cached Response
```

Notice

The controller is **not executed**.

The service is **not executed**.

The repository is **not executed**.

The database is **not queried**.

---

# 3. How Output Cache Works

Suppose

```http
GET /products
```

Execution

```text
Request

↓

Output Cache

↓

Response Exists?

│

├── Yes

│

│ Return Cached HTTP Response

│

└── No

↓

Controller

↓

Business Logic

↓

Repository

↓

Database

↓

Generate JSON

↓

Store HTTP Response

↓

Return Response
```

---

# 4. Output Cache Request Flow

First Request

```text
Client

↓

Output Cache

↓

Cache Miss

↓

Controller

↓

Service

↓

Repository

↓

SQL Server

↓

Generate Response

↓

Cache Response

↓

Return
```

Second Request

```text
Client

↓

Output Cache

↓

Cache Hit

↓

Return Cached Response
```

---

# 5. Output Cache vs IMemoryCache

| Output Cache         | IMemoryCache                           |
| -------------------- | -------------------------------------- |
| Caches HTTP response | Caches objects                         |
| Controller skipped   | Controller executes                    |
| Service skipped      | Service executes                       |
| Repository skipped   | Repository executes                    |
| SQL skipped          | SQL skipped only if service uses cache |

IMemoryCache caches application data.

Output Cache caches the final HTTP response.

---

# 6. Output Cache vs Redis

Redis

```text
Controller

↓

Redis

↓

Deserialize

↓

Return Object
```

Output Cache

```text
Request

↓

Cached HTTP Response

↓

Return
```

Output Cache is typically faster because it bypasses the application pipeline on a cache hit.

---

# 7. Output Cache vs Response Cache

## Response Cache

Uses HTTP cache headers.

```http
Cache-Control

ETag

Expires
```

The browser or proxy decides whether to reuse the response.

---

## Output Cache

Stores responses on the server.

The server decides when to serve the cached response.

Works regardless of browser caching.

---

# 8. Registering Output Cache

Program.cs

```csharp
builder.Services.AddOutputCache();
```

Register middleware

```csharp
app.UseOutputCache();
```

Middleware order is important.

Place it before endpoint mapping.

---

# 9. Enabling Output Cache

Controller

```csharp
[ApiController]
[Route("api/products")]
public class ProductController : ControllerBase
{
    [HttpGet]

    [OutputCache(Duration = 60)]

    public async Task<IActionResult> Get()
    {
        return Ok(await _service.GetProducts());
    }
}
```

The response is cached for 60 seconds.

---

# 10. Cache Duration

```csharp
[OutputCache(Duration = 30)]
```

Meaning

```text
Cache

↓

30 Seconds

↓

Automatically Expire
```

After expiration

↓

Controller executes again.

---

# 11. Cache Policies

Instead of repeating settings on every endpoint, define policies.

Program.cs

```csharp
builder.Services.AddOutputCache(options =>
{
    options.AddPolicy(
        "ProductsCache",
        builder =>
        {
            builder.Expire(TimeSpan.FromMinutes(10));
        });
});
```

Controller

```csharp
[OutputCache(PolicyName = "ProductsCache")]
```

Benefits

* Centralized configuration
* Easier maintenance
* Reusable policies

---

# 12. Vary By Route

Suppose

```http
GET /products/1

GET /products/2
```

Different route values automatically produce different cache entries.

```text
/products/1

↓

Product 1 Cache

--------------

/products/2

↓

Product 2 Cache
```

---

# 13. Vary By Query String

Suppose

```http
GET /products?page=1

GET /products?page=2
```

Configure

```csharp
builder.Services.AddOutputCache(options =>
{
    options.AddPolicy("PagedProducts",
        builder =>
        {
            builder.SetVaryByQuery("page");
        });
});
```

Apply

```csharp
[OutputCache(PolicyName = "PagedProducts")]
```

Each page receives its own cached response.

---

# 14. Vary By Header

Suppose the response changes based on

```http
Accept-Language
```

Configure

```csharp
builder.Services.AddOutputCache(options =>
{
    options.AddPolicy("LanguagePolicy",
        builder =>
        {
            builder.SetVaryByHeader("Accept-Language");
        });
});
```

English

↓

One cache

Spanish

↓

Different cache

---

# 15. Vary By Value

Suppose Premium users see different products.

Program.cs

```csharp
builder.Services.AddOutputCache(options =>
{
    options.AddPolicy("UserType",
        builder =>
        {
            builder.VaryByValue(context =>
            {
                return new KeyValuePair<string, string>(
                    "UserType",
                    context.User.IsInRole("Premium")
                        ? "Premium"
                        : "Regular");
            });
        });
});
```

Now Premium and Regular users receive separate cached responses.

---

# 16. Cache Tags

Tags allow invalidating multiple cached responses together.

Example

```csharp
builder.Services.AddOutputCache(options =>
{
    options.AddPolicy("Products",
        builder =>
        {
            builder.Tag("Products");
        });
});
```

Every cached product response belongs to the **Products** tag.

---

# 17. Cache Invalidation

Suppose

```http
POST /products
```

Product added.

Old cache

↓

Still returned.

Incorrect.

Invalidate cache

```csharp
await cacheStore.EvictByTagAsync(
    "Products",
    CancellationToken.None);
```

Next request

↓

Fresh SQL

↓

Fresh Cache

Cache invalidation is one of the most important aspects of Output Cache.

---

# 18. Enterprise Architecture

```text
React

↓

API Gateway

↓

Output Cache

↓

Cache Hit?

│

├── Yes

│

│ Return HTTP Response

│

└── No

↓

Controller

↓

Service

↓

Repository

↓

SQL Server

↓

Generate JSON

↓

Store Response

↓

Return
```

---

# 19. Performance Benefits

Without Output Cache

```text
Controller

20 ms

↓

Service

40 ms

↓

Repository

80 ms

↓

SQL

250 ms

↓

Serialization

40 ms

↓

Total

430 ms
```

With Output Cache

```text
Request

↓

Output Cache

↓

Return

↓

5 ms
```

Benefits

* Lower CPU usage
* Reduced database load
* Higher throughput
* Faster response time

---

# 20. Best Practices

* Use only for GET or HEAD endpoints.
* Cache public, read-heavy data.
* Configure appropriate expiration.
* Use policies instead of repeating attributes.
* Use tags for invalidation.
* Vary cache when responses differ by route, query, header, or user context.
* Test cache invalidation after updates.

---

# 21. Common Mistakes

❌ Caching POST requests.

❌ Caching user-specific data without varying the cache.

❌ Forgetting cache invalidation.

❌ Very long expiration for frequently changing data.

❌ Caching highly dynamic endpoints.

❌ Confusing Output Cache with IMemoryCache.

---

# 22. Code Review Checklist

Before approving code ask:

* Is Output Cache appropriate for this endpoint?
* Is the endpoint read-only?
* Is cache duration reasonable?
* Does the response vary by query, header, or route?
* Is invalidation implemented?
* Is sensitive or personalized data protected from being shared incorrectly?
* Are reusable policies used?

---

# 23. Common Interview Questions

### What is Output Cache?

A server-side feature that caches the complete HTTP response so future requests can bypass the controller and application logic.

---

### Difference between Output Cache and IMemoryCache?

Output Cache stores HTTP responses.

IMemoryCache stores application objects.

---

### Difference between Output Cache and Response Cache?

Response Cache relies on browser/proxy caching.

Output Cache stores responses on the server.

---

### Can Output Cache cache POST requests?

No.

Output Cache is intended for cacheable responses, typically GET and HEAD.

---

### When should you use Output Cache?

Public read-only endpoints such as

* Products
* Categories
* Countries
* Tax Rates
* Blog Posts

---

### When should you avoid Output Cache?

* Shopping Cart
* Bank Balance
* Payment Status
* Notifications
* User Profile
* OTP APIs

These responses are personalized or change frequently.

---

# 24. Key Takeaways

* Output Cache caches the complete HTTP response.
* The controller, service, and repository are skipped on cache hits.
* It provides better performance than object caching for suitable endpoints.
* Use policies for reusable configuration.
* Configure variation rules when responses depend on route, query, headers, or user context.
* Always invalidate cached responses when underlying data changes.

---

# 25. Next Module

## README 5 – Hybrid Cache (.NET 9)

Topics include:

* What is Hybrid Cache?
* Hybrid Cache Architecture
* Local Memory + Redis
* Automatic Cache Synchronization
* Cache Stampede Prevention
* Serialization
* Cache Tags
* Production Architecture
* Best Practices
* Complete .NET 9 Examples
* Interview Questions
