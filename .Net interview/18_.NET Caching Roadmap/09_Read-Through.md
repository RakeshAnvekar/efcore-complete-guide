# Read-Through Caching Pattern - Complete .NET Interview Guide

## Table of Contents

1. What is Read-Through Cache?
2. How Read-Through Works
3. Read-Through vs Cache-Aside
4. Request Flow
5. Real World Example
6. Why Redis Doesn't Support Read-Through Natively
7. Systems That Support Read-Through
8. Advantages
9. Disadvantages
10. Interview Questions
11. Interview Answers

---

# What is Read-Through Cache?

Read-Through Cache is a caching pattern where:

> The application only talks to the cache.

When data is not found in the cache:

1. Cache automatically loads data from database.
2. Cache stores the data.
3. Cache returns the data to the application.

The application never directly accesses the database.

---

# Architecture

```text
Application
      |
      v
    Cache
      |
      v
   Database
```

The application knows only about the cache.

---

# How Read-Through Works

Suppose Product 101 is requested.

## Step 1

Application requests:

```text
Product 101
```

```text
Application
      ↓
Cache
```

---

## Step 2

Cache checks memory.

```text
Cache Miss
```

---

## Step 3

Cache automatically loads data.

```text
Cache
   ↓
Database
```

Example:

```sql
SELECT * FROM Products
WHERE Id = 101
```

---

## Step 4

Database returns data.

```text
Database
    ↓
Cache
```

---

## Step 5

Cache stores result.

```text
product:101
```

---

## Step 6

Cache returns data.

```text
Cache
   ↓
Application
```

---

# First Request Flow

```text
Application
      ↓
Cache
      ↓
Miss
      ↓
Database
      ↓
Store In Cache
      ↓
Return Response
```

---

# Second Request Flow

```text
Application
      ↓
Cache
      ↓
Hit
      ↓
Return Response
```

No database call occurs.

---

# Cache-Aside vs Read-Through

## Cache-Aside

Application handles cache logic.

```text
Application
      ↓
Redis
      ↓
Database
```

Application performs:

- Cache lookup
- Database lookup
- Cache update

---

### Example

```csharp
var product =
    await cache.GetAsync(key);

if(product == null)
{
    product =
        await repository.GetByIdAsync(id);

    await cache.SetAsync(key, product);
}

return product;
```

Application manages everything.

---

# Read-Through

Application only talks to cache.

```text
Application
      ↓
Cache
      ↓
Database
```

The cache manages:

- Cache lookup
- Database lookup
- Cache update

---

### Example (Conceptual)

```csharp
var product =
    cache.Get("product:101");
```

Internally:

```text
Cache Miss
     ↓
Load From Database
     ↓
Store In Cache
     ↓
Return Data
```

Application is unaware of database operations.

---

# Real World Example

Think of a library.

---

## Cache-Aside

You ask the librarian:

```text
Do you have this book?
```

Librarian:

```text
No
```

You go to warehouse.

```text
Warehouse
    ↓
Get Book
    ↓
Bring Back
```

You did all the work.

---

## Read-Through

You ask librarian:

```text
Do you have this book?
```

Librarian:

```text
No problem.
```

Librarian goes to warehouse.

```text
Warehouse
     ↓
Gets Book
     ↓
Stores On Shelf
     ↓
Gives Book To You
```

You only talk to librarian.

The librarian is the cache.

---

# Why Redis Doesn't Support Read-Through Natively

Redis is a key-value store.

Redis understands:

```text
SET
GET
DEL
EXPIRE
```

Redis does not understand:

```sql
SELECT * FROM Products
```

Redis has no knowledge of:

- SQL Server
- PostgreSQL
- Oracle
- MongoDB

Therefore Redis cannot automatically load data from a database.

---

# Redis Architecture

```text
Application
      ↓
Redis
      ↓
Database
```

Redis and database are completely independent.

Because of this, ASP.NET Core applications usually implement:

```text
Cache-Aside Pattern
```

instead of Read-Through.

---

# Technologies Supporting Read-Through

Some caching platforms support automatic database loading.

Examples:

```text
Hazelcast
Apache Ignite
NCache
Ehcache
Infinispan
```

These systems can be configured with:

```text
Cache Loader
Read-Through Provider
Data Source Adapter
```

---

# Advantages

## Simpler Application Code

Application only talks to cache.

```text
Application
      ↓
Cache
```

No cache management code.

---

## Centralized Data Loading

Cache becomes responsible for:

- Loading data
- Storing data
- Returning data

---

## Consistent Behavior

Every application accesses data through the same cache layer.

---

# Disadvantages

## More Complex Cache Layer

Cache must know:

- Database
- Tables
- Queries

---

## Vendor Dependency

Often depends on cache-specific features.

Example:

```text
Hazelcast
Apache Ignite
NCache
```

Switching providers may require changes.

---

## Not Supported by Redis

Redis is the most popular distributed cache in .NET.

Redis does not natively support Read-Through.

---

# When To Use Read-Through

Good for:

```text
Reference Data
Product Catalogs
Country Lists
Configuration Data
Frequently Read Data
```

Data that:

- Changes infrequently
- Is read frequently

---

# When Not To Use Read-Through

Avoid when:

```text
Complex Queries
Heavy Write Systems
Real-Time Transactions
Frequently Changing Data
```

---

# Comparison Table

| Feature | Cache-Aside | Read-Through |
|----------|------------|-------------|
| App Checks Cache | Yes | No |
| App Loads DB Data | Yes | No |
| Cache Loads Data | No | Yes |
| Cache Updates Itself | No | Yes |
| Redis Support | Yes | No |
| Most Common In .NET | Yes | No |
| Application Complexity | Higher | Lower |
| Cache Complexity | Lower | Higher |

---

# Interview Questions

## What is Read-Through Cache?

A caching pattern where the application interacts only with the cache. On a cache miss, the cache automatically retrieves data from the database, stores it, and returns it.

---

## How is Read-Through different from Cache-Aside?

In Cache-Aside, the application is responsible for loading data from the database and updating the cache. In Read-Through, the cache performs these operations automatically.

---

## Does Redis support Read-Through?

No. Redis is a key-value store and does not know how to query relational databases. Therefore Redis does not provide native Read-Through functionality.

---

## Which caching pattern is most common in ASP.NET Core?

Cache-Aside is the most common pattern because it works naturally with Redis.

---

# Interview Answer

Read-Through caching is a pattern where the application interacts only with the cache. When data is missing, the cache automatically loads the data from the database, stores it, and returns it. This simplifies application code because the cache manages data retrieval. However, Redis does not natively support Read-Through since it is only a key-value store and cannot execute database queries. As a result, most ASP.NET Core applications use the Cache-Aside pattern with Redis, while technologies such as Hazelcast, Apache Ignite, and NCache provide Read-Through capabilities.