# JWT Authentication in ASP.NET Core

# README 2 – JWT Authentication Implementation

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. Project Architecture
2. Required NuGet Package
3. Project Structure
4. JWT Configuration (`appsettings.json`)
5. JWT Settings Explained
6. Registering Authentication
7. Configuring JWT Bearer
8. Token Validation Parameters
9. Authentication Middleware
10. Authorization Middleware
11. Login Request Flow
12. Protected API Flow
13. Internal Request Pipeline
14. Interview Questions
15. Key Takeaways

---

# 1. Project Architecture

A typical JWT-based Web API follows this architecture:

```text
                 Client

                   │

             Login Request

                   │

                   ▼

            AuthController

                   │

              UserService

                   │

        Validate Credentials

                   │

                   ▼

              SQL Server

                   │

             User Exists?

                   │

                   ▼

             JwtService

                   │

        Generate JWT Token

                   │

                   ▼

        Return Access Token

                   │

Client Stores Token

(Local Storage / Secure Cookie)

                   │

Authorization: Bearer <Token>

                   │

                   ▼

JWT Authentication Middleware

                   │

Validate Token

                   │

Controller Executes
```

---

# 2. Install Required Package

```bash
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

This package provides:

* JWT Authentication Middleware
* Bearer Token Support
* Token Validation
* ClaimsPrincipal Creation

Without this package, ASP.NET Core cannot validate JWT tokens.

---

# 3. Recommended Project Structure

```text
JwtDemo

│

├── Controllers

│      AuthController.cs

│      ProductController.cs

│

├── Services

│      JwtService.cs

│      UserService.cs

│

├── Models

│      LoginRequest.cs

│      User.cs

│

├── Program.cs

│

└── appsettings.json
```

Keeping authentication logic inside dedicated services makes the application easier to maintain and test.

---

# 4. Configure appsettings.json

```json
{
  "Jwt": {
    "Key": "ThisIsMyVerySecretKey123456789",
    "Issuer": "MyCompany",
    "Audience": "MyCompanyUsers",
    "ExpiryMinutes": 60
  }
}
```

---

# 5. Understanding JWT Settings

## Key

The secret key used to sign and validate tokens.

Never hardcode it in production code.

Prefer:

* Azure Key Vault
* AWS Secrets Manager
* Environment Variables
* Secret Manager (development)

---

## Issuer

Identifies who created the token.

Example:

```
MyCompany Authentication Server
```

---

## Audience

Identifies who is allowed to use the token.

Example:

```
MyCompany Web API
```

---

## ExpiryMinutes

Determines how long the Access Token remains valid.

Example:

```
60 Minutes
```

After expiration the token is rejected.

---

# 6. Register Authentication

```csharp
builder.Services
    .AddAuthentication(JwtBearerDefaults.AuthenticationScheme);
```

Purpose:

This tells ASP.NET Core:

> "Use JWT Bearer Authentication as the default authentication mechanism."

Without this registration:

* JWT Middleware is not enabled.
* `[Authorize]` will not authenticate users.

---

# 7. Configure JWT Bearer

```csharp
builder.Services
.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
.AddJwtBearer(options =>
{
    options.TokenValidationParameters =
        new TokenValidationParameters
        {
        };
});
```

This configures how every incoming JWT will be validated.

---

# 8. Token Validation Parameters

## ValidateIssuer

```csharp
ValidateIssuer = true
```

Ensures the token was issued by the expected authority.

---

## ValidateAudience

```csharp
ValidateAudience = true
```

Ensures the token was created for this application.

---

## ValidateLifetime

```csharp
ValidateLifetime = true
```

Checks whether the token has expired.

Expired tokens result in:

```
401 Unauthorized
```

---

## ValidateIssuerSigningKey

```csharp
ValidateIssuerSigningKey = true
```

Verifies that the JWT signature matches the configured secret key.

This protects against token tampering.

---

## IssuerSigningKey

```csharp
IssuerSigningKey =
new SymmetricSecurityKey(
Encoding.UTF8.GetBytes(
builder.Configuration["Jwt:Key"]))
```

Flow:

```
Read Secret Key

↓

Convert to Bytes

↓

Create Security Key

↓

Validate Signature
```

---

# 9. Authentication Middleware

```csharp
app.UseAuthentication();
```

Responsibilities:

* Reads Authorization header
* Extracts Bearer Token
* Validates token
* Creates ClaimsPrincipal
* Populates HttpContext.User

Without this middleware every request remains anonymous.

---

# 10. Authorization Middleware

```csharp
app.UseAuthorization();
```

Responsibilities:

* Processes `[Authorize]`
* Checks Roles
* Checks Policies
* Grants or denies access

Authentication must always execute before Authorization.

Correct order:

```text
Request

↓

Authentication

↓

Authorization

↓

Controller
```

---

# 11. Login Request Flow

Client Request

```json
{
    "username":"rakesh",
    "password":"Password123"
}
```

Execution Flow

```text
Client

↓

AuthController

↓

UserService

↓

Database

↓

User Found?

↓

JwtService

↓

Generate Token

↓

Return JWT
```

---

# 12. Protected API Flow

Client Request

```http
GET /api/orders
```

Headers

```text
Authorization:

Bearer eyJhbGc...
```

Pipeline

```text
Request

↓

UseAuthentication()

↓

Validate JWT

↓

Create ClaimsPrincipal

↓

UseAuthorization()

↓

[Authorize]

↓

Controller Executes
```

---

# 13. Internal Request Pipeline

```text
Client

↓

Authorization Header

↓

Authentication Middleware

↓

Read Token

↓

Validate Signature

↓

Validate Issuer

↓

Validate Audience

↓

Validate Expiration

↓

Create ClaimsPrincipal

↓

HttpContext.User

↓

Authorization Middleware

↓

Controller
```

---

# 14. Common Interview Questions

### Why do we call AddAuthentication()?

To register JWT as the application's authentication mechanism.

---

### Why do we call AddJwtBearer()?

To configure how incoming JWT tokens are validated.

---

### Why is ValidateIssuer important?

It prevents tokens issued by unknown authorities from being accepted.

---

### Why validate the Audience?

To ensure the token is intended for this application.

---

### Why validate Lifetime?

To reject expired tokens.

---

### Why validate the Signature?

To detect any modification to the token.

---

### Why is the Secret Key important?

The Secret Key signs and validates JWTs.

Anyone possessing the key can generate valid tokens.

Protect it carefully.

---

### What does UseAuthentication() do?

It authenticates the user and populates `HttpContext.User`.

---

### What does UseAuthorization() do?

It enforces authorization rules such as:

* `[Authorize]`
* Roles
* Policies

---

### What happens if UseAuthentication() is missing?

Every request is treated as anonymous.

Protected endpoints return **401 Unauthorized**.

---

### What happens if the token is expired?

JWT validation fails and the request receives **401 Unauthorized**.

---

# 15. Key Takeaways

* Install `Microsoft.AspNetCore.Authentication.JwtBearer`.
* Store JWT configuration in `appsettings.json`.
* Register JWT using `AddAuthentication()` and `AddJwtBearer()`.
* Configure:

  * Issuer
  * Audience
  * Lifetime
  * Signing Key
* Add middleware in the correct order:

  * `UseAuthentication()`
  * `UseAuthorization()`
* JWT Middleware validates the token and creates `HttpContext.User`.
* Authorization checks happen only after authentication succeeds.
* Keep the secret key secure and always use HTTPS in production.

---

# What's Next?

The next module will implement:

* `JwtService`
* `GenerateToken()`
* Claims
* `AuthController`
* Login API
* `[Authorize]`
* `[AllowAnonymous]`
* Swagger JWT Configuration
* Reading `HttpContext.User`
* Complete end-to-end working example

After completing the next module, you'll be able to implement JWT Authentication from scratch in an ASP.NET Core Web API and confidently explain every part during an interview.
