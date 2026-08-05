# JWT Authentication in ASP.NET Core

# README 5 – Policy-Based Authorization

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is Policy-Based Authorization?
2. Why Do We Need Policies?
3. Roles vs Policies
4. How Policy-Based Authorization Works
5. Configuring Authorization
6. Creating a Policy
7. Using Policies
8. RequireRole()
9. RequireClaim()
10. RequireAuthenticatedUser()
11. Combining Multiple Requirements
12. Real-World Examples
13. Authorization Flow
14. Best Practices
15. Common Interview Questions
16. Key Takeaways

---

# 1. What is Policy-Based Authorization?

Policy-Based Authorization is a flexible authorization mechanism that allows you to define **named authorization rules**.

Unlike Role-Based Authorization, a policy can evaluate:

* Roles
* Claims
* Authentication status
* Custom business rules

Example:

```text id="0p3pq5"
InventoryPolicy

↓

Role = Manager

AND

Department = Inventory

AND

Account Active
```

If all conditions are satisfied, access is granted.

---

# 2. Why Do We Need Policies?

Imagine an e-commerce application.

Users

| User   | Role    | Department |
| ------ | ------- | ---------- |
| Rakesh | Manager | Inventory  |
| John   | Manager | Sales      |
| David  | Admin   | IT         |

Business Requirement

> Only Managers from the Inventory department can approve stock.

Can Role-Based Authorization solve this?

```csharp id="r7h6xg"
[Authorize(Roles = "Manager")]
```

No.

Both Rakesh and John are Managers.

We need an additional condition.

---

# 3. Role-Based Authorization vs Policy-Based Authorization

## Role-Based

```csharp id="vzx8fk"
[Authorize(Roles = "Admin")]
```

Checks only:

```text id="zlo9jo"
Role
```

---

## Policy-Based

```csharp id="4rj7hj"
[Authorize(Policy = "InventoryPolicy")]
```

Can check:

```text id="0n0rti"
Role

+

Claims

+

Authentication

+

Custom Rules
```

Policies provide much more flexibility.

---

# 4. How Policy-Based Authorization Works

Request Flow

```text id="4jlqpa"
Client Request

        │

        ▼

Authentication Middleware

        │

Validate JWT

        │

Create ClaimsPrincipal

        │

        ▼

Authorization Middleware

        │

Load Policy

        │

Evaluate Requirements

        │

All Passed?

        │

        ▼

Controller Executes
```

---

# 5. Register Authorization

In Program.cs

```csharp id="ndjm7l"
builder.Services.AddAuthorization(options =>
{

});
```

This registers the Authorization system.

Policies are configured inside this block.

---

# 6. Creating a Policy

Example

```csharp id="jq0r75"
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy(
        "InventoryPolicy",
        policy =>
        {
            policy.RequireRole("Manager");
        });
});
```

Meaning

```text id="zzdu04"
InventoryPolicy

↓

Role must be Manager
```

---

# 7. Using a Policy

Controller

```csharp id="ctqkmn"
[Authorize(Policy = "InventoryPolicy")]
public IActionResult ApproveStock()
{
    return Ok();
}
```

Execution Flow

```text id="sm5kjk"
Request

↓

JWT Valid?

↓

InventoryPolicy

↓

Role = Manager?

↓

Yes

↓

Controller Executes
```

---

# 8. RequireRole()

Require one or more roles.

```csharp id="4kibne"
policy.RequireRole("Manager");
```

Multiple roles

```csharp id="khgdyu"
policy.RequireRole("Admin", "Manager");
```

Meaning

```text id="9tqqvo"
Admin

OR

Manager
```

Either role is accepted.

---

# 9. RequireClaim()

Suppose the JWT contains

```json id="q6d3p9"
{
    "department":"Inventory"
}
```

Policy

```csharp id="ygg6w5"
policy.RequireClaim(
    "department",
    "Inventory");
```

Meaning

```text id="wk2gtj"
department == Inventory
```

---

# 10. RequireAuthenticatedUser()

Example

```csharp id="jlwm1r"
policy.RequireAuthenticatedUser();
```

Meaning

```text id="1c5h5v"
JWT must be valid

↓

User must be authenticated
```

---

# 11. Combining Multiple Requirements

Policies can combine multiple conditions.

Example

```csharp id="mjlwm"
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy(
        "InventoryPolicy",
        policy =>
        {
            policy.RequireAuthenticatedUser();

            policy.RequireRole("Manager");

            policy.RequireClaim(
                "department",
                "Inventory");
        });
});
```

Business Rule

```text id="x4l9mx"
Authenticated User

AND

Manager

AND

Department = Inventory
```

All conditions must be satisfied.

---

# 12. Real-World Example

## E-Commerce

Business Requirement

Only Inventory Managers can approve stock.

JWT Payload

```json id="txv6pq"
{
  "name":"Rakesh",
  "role":"Manager",
  "department":"Inventory"
}
```

Policy Evaluation

```text id="b92yct"
Authenticated?

↓

Yes

↓

Role = Manager?

↓

Yes

↓

Department = Inventory?

↓

Yes

↓

Access Granted
```

Another User

```json id="vt09h9"
{
  "name":"John",
  "role":"Manager",
  "department":"Sales"
}
```

Evaluation

```text id="f5v0zj"
Authenticated?

↓

Yes

↓

Role = Manager?

↓

Yes

↓

Department = Inventory?

↓

No

↓

403 Forbidden
```

---

# 13. Authorization Pipeline

```text id="rz1x2b"
HTTP Request

        │

Authorization Header

        │

        ▼

UseAuthentication()

        │

Validate JWT

        │

Create ClaimsPrincipal

        │

        ▼

UseAuthorization()

        │

InventoryPolicy

        │

RequireAuthenticatedUser()

        │

RequireRole()

        │

RequireClaim()

        │

All Requirements Passed?

        │

        ▼

Controller
```

---

# 14. Best Practices

* Keep policies focused on one business rule.
* Use meaningful policy names.
* Prefer policies over complex role combinations.
* Store business data as claims only when appropriate.
* Use custom authorization handlers for complex business logic.
* Avoid putting sensitive data in JWT claims.

---

# 15. Common Interview Questions

### What is Policy-Based Authorization?

A flexible authorization mechanism that evaluates named authorization rules using roles, claims, authentication status, and custom requirements.

---

### Why use Policies instead of Roles?

Roles alone cannot represent many real-world business rules. Policies allow combining multiple conditions such as roles, claims, and custom logic.

---

### What does RequireRole() do?

It ensures the authenticated user belongs to one or more specified roles.

---

### What does RequireClaim() do?

It ensures the authenticated user contains a required claim with the expected value.

---

### What does RequireAuthenticatedUser() do?

It requires the current request to come from an authenticated user.

---

### Can a policy contain multiple requirements?

Yes.

All configured requirements must succeed before access is granted.

---

### What happens when a policy check fails?

The request is denied.

* If the user is **not authenticated**, the response is typically **401 Unauthorized**.
* If the user **is authenticated but does not satisfy the policy**, the response is typically **403 Forbidden**.

---

# 16. Key Takeaways

* Policies are **named authorization rules**.
* Policies are more powerful than simple role checks.
* Policies can evaluate:

  * Authentication status
  * Roles
  * Claims
  * Multiple requirements together
* `RequireRole()` checks user roles.
* `RequireClaim()` checks claim values.
* `RequireAuthenticatedUser()` requires a valid authenticated user.
* Multiple policy requirements are evaluated together.
* Policy-Based Authorization is widely used in enterprise applications because it models business rules more effectively than roles alone.

---

# What's Next?

The next module is **Custom Authorization Requirements & Authorization Handlers**, where you'll learn to implement business rules that cannot be expressed with the built-in methods.

Examples include:

* Only users with annual purchases above ₹1,00,000 can access premium features.
* Only employees with more than 5 years of experience can approve loans.
* Only order owners can edit their own orders.
* Time-based access (office hours only).

These scenarios are implemented using **IAuthorizationRequirement** and **AuthorizationHandler<TRequirement>**, and they are commonly discussed in Senior and Lead ASP.NET Core interviews.
