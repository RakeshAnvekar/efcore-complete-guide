# JWT Authentication in ASP.NET Core

# README 4 – Claims, Roles & Authorization

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What are Claims?
2. Why are Claims Needed?
3. JWT Payload and Claims
4. Creating Claims
5. JWT Authentication Flow
6. ClaimsIdentity
7. ClaimsPrincipal
8. Reading Claims
9. Role-Based Authorization
10. Multiple Roles
11. Authentication vs Authorization
12. 401 vs 403
13. Real-World Example
14. Best Practices
15. Common Interview Questions
16. Key Takeaways

---

# 1. What are Claims?

A **Claim** is a piece of information about an authenticated user.

Think of a claim as a **Key–Value Pair**.

Examples:

```text
UserId  → 101
Name    → Rakesh
Email   → rakesh@gmail.com
Role    → Admin
Country → India
```

Claims are stored inside the **JWT Payload** and become available after the token is validated.

---

# 2. Why are Claims Needed?

Once a user is authenticated, the application often needs information about that user.

Examples:

* Who is the current user?
* What is the user's role?
* What is the user's email?
* Which department does the user belong to?

Instead of querying the database on every request, this information can be stored as claims inside the JWT.

---

# 3. JWT Payload and Claims

Example JWT Payload

```json
{
  "sub": "101",
  "name": "Rakesh",
  "email": "rakesh@gmail.com",
  "role": "Admin"
}
```

Meaning

| Claim | Value                                       |
| ----- | ------------------------------------------- |
| sub   | 101                                         |
| name  | Rakesh                                      |
| email | [rakesh@gmail.com](mailto:rakesh@gmail.com) |
| role  | Admin                                       |

---

# 4. Creating Claims

Example

```csharp
var claims = new[]
{
    new Claim(JwtRegisteredClaimNames.Sub, user.Id.ToString()),
    new Claim(JwtRegisteredClaimNames.Name, user.Username),
    new Claim(JwtRegisteredClaimNames.Email, user.Email),
    new Claim(ClaimTypes.Role, user.Role)
};
```

Purpose of each claim:

| Claim | Purpose        |
| ----- | -------------- |
| Sub   | Unique User ID |
| Name  | User Name      |
| Email | Email Address  |
| Role  | User Role      |

These claims are included in the JWT when it is generated.

---

# 5. JWT Authentication Flow

```text
User Login

     │

Validate Username & Password

     │

Create Claims

     │

Generate JWT

     │

Return JWT

     │

Client Stores JWT

     │

API Request

Authorization: Bearer <JWT>

     │

JWT Authentication Middleware

     │

Validate Token

     │

Create ClaimsPrincipal

     │

HttpContext.User

     │

Controller Executes
```

---

# 6. ClaimsIdentity

A **ClaimsIdentity** represents one authenticated identity.

It contains all claims associated with that identity.

Conceptually:

```text
ClaimsIdentity

↓

UserId = 101

↓

Name = Rakesh

↓

Email = rakesh@gmail.com

↓

Role = Admin
```

An application may contain multiple identities (for example, external providers), but most APIs use a single identity.

---

# 7. ClaimsPrincipal

A **ClaimsPrincipal** represents the authenticated user.

It contains one or more `ClaimsIdentity` objects.

Structure:

```text
ClaimsPrincipal

       │

       ▼

ClaimsIdentity

       │

       ▼

Claims

UserId

Name

Email

Role
```

ASP.NET Core exposes the current user through:

```csharp
HttpContext.User
```

`HttpContext.User` is a `ClaimsPrincipal`.

---

# 8. Reading Claims

## User Name

```csharp
var username = User.Identity?.Name;
```

---

## Email

```csharp
var email =
User.FindFirst(ClaimTypes.Email)?.Value;
```

---

## Role

```csharp
var role =
User.FindFirst(ClaimTypes.Role)?.Value;
```

---

## User ID

```csharp
var userId =
User.FindFirst(JwtRegisteredClaimNames.Sub)?.Value;
```

These values come directly from the validated JWT.

---

# 9. Role-Based Authorization

Suppose the JWT contains:

```text
Role = Admin
```

Controller

```csharp
[Authorize(Roles = "Admin")]
[HttpDelete]
public IActionResult DeleteProduct()
{
    return Ok();
}
```

Execution Flow

```text
Request

↓

JWT Middleware

↓

Read Role Claim

↓

Role = Admin?

↓

Yes

↓

Controller Executes
```

---

# 10. Multiple Roles

Example

```csharp
[Authorize(Roles = "Admin,Manager")]
```

Meaning

```text
Admin

OR

Manager
```

Either role is allowed to access the endpoint.

---

# 11. Authentication vs Authorization

| Authentication      | Authorization                  |
| ------------------- | ------------------------------ |
| Identifies the user | Checks permissions             |
| Uses JWT validation | Uses Roles / Claims / Policies |
| Happens first       | Happens after authentication   |

Flow

```text
Authentication

↓

User Identified

↓

Authorization

↓

Permission Check

↓

Controller
```

---

# 12. 401 vs 403

## 401 Unauthorized

Authentication failed.

Examples:

* Missing JWT
* Invalid JWT
* Expired JWT

The user is **not authenticated**.

---

## 403 Forbidden

Authentication succeeded.

Authorization failed.

Example:

```text
JWT

↓

Role = Customer

↓

Endpoint Requires Admin

↓

403 Forbidden
```

The user is authenticated but does not have permission.

---

# 13. Real-World E-Commerce Example

### Customer

Permissions

* View Products
* Add to Cart
* Place Orders

---

### Manager

Permissions

* View Reports
* Approve Orders

---

### Admin

Permissions

* Create Products
* Delete Products
* Manage Users
* Update Inventory

Controller Examples

```csharp
[Authorize(Roles = "Customer")]
public IActionResult PlaceOrder()
{
}
```

```csharp
[Authorize(Roles = "Manager")]
public IActionResult ViewSalesReport()
{
}
```

```csharp
[Authorize(Roles = "Admin")]
public IActionResult DeleteProduct()
{
}
```

---

# 14. Best Practices

* Store only necessary information as claims.
* Avoid putting sensitive data (passwords, credit card numbers, etc.) into JWT claims.
* Keep claims small to reduce token size.
* Use roles for coarse-grained authorization.
* Use policies when authorization rules become more complex.

---

# 15. Common Interview Questions

### What is a Claim?

A Claim is a key-value pair containing information about the authenticated user.

---

### What is ClaimsIdentity?

A ClaimsIdentity is a collection of claims representing a single authenticated identity.

---

### What is ClaimsPrincipal?

A ClaimsPrincipal represents the authenticated user and contains one or more ClaimsIdentity objects. In ASP.NET Core it is available as `HttpContext.User`.

---

### Where are claims stored after authentication?

Inside `HttpContext.User`.

---

### What is Role-Based Authorization?

It restricts access to endpoints based on the user's role.

Example:

```csharp
[Authorize(Roles = "Admin")]
```

---

### Difference between 401 and 403?

| Status Code      | Meaning                                                     |
| ---------------- | ----------------------------------------------------------- |
| 401 Unauthorized | Authentication failed (missing, invalid, or expired token). |
| 403 Forbidden    | Authentication succeeded but the user lacks permission.     |

---

# 16. Key Takeaways

* Claims are **key-value pairs** describing the authenticated user.
* Claims are stored inside the **JWT Payload**.
* After validation, claims become available through `HttpContext.User`.
* `ClaimsIdentity` contains the claims for one identity.
* `ClaimsPrincipal` represents the current authenticated user.
* Role-based authorization uses the **Role** claim to secure endpoints.
* Authentication identifies the user; authorization checks permissions.
* **401** means the user is not authenticated.
* **403** means the user is authenticated but not authorized.
* Keep JWT claims small and never store sensitive information in them.

---

# What's Next?

The next module is **Policy-Based Authorization**, where you'll learn:

* Why policies are more powerful than roles
* Creating custom authorization policies
* `AddAuthorization()`
* `options.AddPolicy()`
* Custom authorization requirements
* Custom authorization handlers
* Enterprise authorization patterns used in banking, healthcare, and e-commerce systems
