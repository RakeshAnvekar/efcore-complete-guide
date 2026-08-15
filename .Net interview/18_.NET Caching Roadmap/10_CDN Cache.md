# CDN Cache - Complete .NET Interview Notes

# What is CDN Cache?

CDN stands for:

```text
Content Delivery Network
```

A CDN stores copies of static files in multiple locations around the world and serves them from the location nearest to the user.

---

# Why Do We Need CDN?

Imagine your ASP.NET Core application is hosted in Bangalore.

Your website contains:

```text
logo.png
product.jpg
main.js
site.css
video.mp4
```

Without CDN:

```text
USA User
      ↓
Bangalore Server
      ↓
Downloads Image
```

Every user must download files from Bangalore.

Result:

```text
Slow Loading
High Latency
More Server Load
```

---

# With CDN

CDN creates copies of files in multiple countries.

```text
                Original Server
                    Bangalore
                         |
        -----------------------------------
        |                |               |
     CDN USA         CDN UK        CDN Japan
```

Now:

```text
USA User
    ↓
CDN USA
```

```text
UK User
   ↓
CDN UK
```

```text
Japan User
    ↓
CDN Japan
```

Users receive files from the nearest location.

---

# Real-Life Example

Imagine Amazon has only one warehouse.

```text
All Customers
      ↓
Bangalore Warehouse
```

Delivery takes time.

Now Amazon creates warehouses everywhere.

```text
Warehouse USA
Warehouse UK
Warehouse Japan
Warehouse India
```

Customers get products from the nearest warehouse.

CDN works exactly the same way.

---

# What Gets Cached?

CDN is mainly used for static content.

Examples:

```text
Images
CSS Files
JavaScript Files
Fonts
PDF Files
Videos
Static HTML
```

Examples:

```text
logo.png
product.jpg
site.css
app.js
manual.pdf
intro.mp4
```

---

# What Should NOT Be Cached?

User-specific and frequently changing data.

Examples:

```text
Shopping Cart
Bank Balance
Payment Information
User Profile
JWT Token
Order Status
```

These should come from APIs or databases.

---

# CDN Request Flow

## First Request

User requests:

```text
product101.jpg
```

Flow:

```text
User
  ↓
CDN
  ↓
Cache Miss
  ↓
Origin Server
  ↓
Get File
  ↓
Store In CDN
  ↓
Return File
```

---

## Second Request

Another user requests same image.

```text
User
  ↓
CDN
  ↓
Cache Hit
  ↓
Return File
```

Origin server is not contacted.

---

# Architecture

```text
             Users
                |
        ----------------
        |      |       |
      USA     UK    Japan
        |      |       |
       CDN    CDN     CDN
           \   |   /
             Origin
          ASP.NET Core
```

---

# CDN in Azure

Most common enterprise setup:

```text
ASP.NET Core API
       ↓
Azure Blob Storage
       ↓
Azure CDN
       ↓
Users
```

---

## Example

Image stored in Blob:

```text
https://storage.blob.core.windows.net/images/product101.jpg
```

CDN URL:

```text
https://cdn.company.com/images/product101.jpg
```

Users access:

```text
https://cdn.company.com/images/product101.jpg
```

CDN serves the file.

---

# Benefits

## Faster Performance

Without CDN:

```text
USA User
      ↓
India Server
```

High latency.

With CDN:

```text
USA User
      ↓
USA CDN
```

Low latency.

---

## Reduced Server Load

Without CDN:

```text
10000 Users
      ↓
10000 Image Requests
      ↓
Web Server
```

With CDN:

```text
10000 Users
      ↓
CDN
      ↓
Few Requests To Origin
```

Server handles less traffic.

---

## Better Scalability

Application server focuses on:

```text
Business Logic
Authentication
Database Calls
Payments
Orders
```

CDN handles static content.

---

# Cache Expiration

CDN stores files for a configurable duration.

Example:

```http
Cache-Control: public,max-age=86400
```

Meaning:

```text
Cache For 24 Hours
```

---

# Cache Invalidation

Suppose:

```text
logo.png changed
```

CDN may still serve old file.

---

## Option 1: Purge CDN

Remove cached copy.

```text
CDN Cache
      ↓
Delete
```

Next request fetches new file.

---

## Option 2: Versioning (Recommended)

Old:

```text
logo.png
```

New:

```text
logo-v2.png
```

or

```text
logo.png?v=2
```

CDN treats it as a new file.

---

# CDN vs Redis

| Feature | CDN | Redis |
|----------|-------|--------|
| Purpose | Static Files | Application Data |
| Stores | Images, CSS, JS, Videos | Objects, JSON, Query Results |
| Location | Edge Servers Worldwide | Central Cache Server |
| Used For | Faster Content Delivery | Faster Database Access |
| Example | product.jpg | product:101 |

---

# CDN vs Output Cache

| Feature | CDN | Output Cache |
|----------|-----|--------------|
| Stores | Static Files | HTTP Responses |
| Location | Edge Servers | ASP.NET Core |
| Used For | Images, CSS, JS | API Responses |
| Example | logo.png | GET /products/101 |

---

# Common CDN Providers

## Azure

```text
Azure CDN
```

---

## AWS

```text
CloudFront
```

---

## Cloudflare

```text
Cloudflare CDN
```

---

## Akamai

```text
Akamai CDN
```

---

# Interview Questions

## What is CDN?

A Content Delivery Network that stores copies of static content across multiple geographic locations and serves them from the nearest location to users.

---

## Why Use CDN?

- Faster content delivery
- Reduced latency
- Reduced server load
- Better scalability

---

## What Content Should Be Cached?

```text
Images
CSS
JavaScript
Fonts
PDFs
Videos
```

---

## What Content Should Not Be Cached?

```text
Shopping Cart
User Data
Payments
Authentication Data
```

---

## How Does CDN Improve Performance?

Instead of downloading files from the origin server, users download them from the nearest CDN edge location.

---

# Interview Answer

In ASP.NET Core applications, CDN Cache is used to store static content such as images, CSS, JavaScript, videos, and PDFs in edge servers distributed across the world. When a user requests a file, the CDN serves it from the nearest location rather than the origin server. This reduces latency, improves page load times, decreases server load, and improves scalability. A common Azure architecture is ASP.NET Core + Azure Blob Storage + Azure CDN, where product images and static assets are delivered through the CDN.