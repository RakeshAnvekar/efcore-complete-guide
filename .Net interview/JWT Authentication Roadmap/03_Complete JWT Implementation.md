# JWT Authentication in ASP.NET Core

# README 3 – Complete JWT Implementation

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. JWT Authentication Flow
2. Project Architecture
3. Login Request Model
4. User Model
5. User Validation Service
6. JWT Service
7. Claims
8. Secret Key
9. Signing Credentials
10. Generating JWT Token
11. AuthController
12. Login API Flow
13. Protecting APIs
14. Reading Claims
15. Request Lifecycle
16. Best Practices
17. Interview Questions
18. Key Takeaways

---

# 1. JWT Authentication Flow

The complete authentication process is shown below.

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

                  Yes

                    │

                    ▼

               JwtService

                    │

             Create Claims

                    │

          Generate JWT Token

                    │

                    ▼

         Return Access Token

                    │

Client Stores Token

(Local Storage / HttpOnly Cookie)

                    │

Authorization: Bearer <Token>

                    │

                    ▼

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

# 2. Project Architecture

```text
JwtDemo

│

├── Controllers

│      AuthController.cs

│      ProductController.cs

│

├── Services

│      IUserService.cs

│      UserService.cs

│

│      IJwtService.cs

│      JwtService.cs

│

├── Models

│      LoginRequest.cs

│      User.cs

│

├── Program.cs

│

└── appsettings.json
```

Keeping authentication logic separate improves maintainability and testing.

---

# 3. Login Request Model

The client sends login credentials.

Example Request

```json
{
    "username":"rakesh",
    "password":"Password123"
}
```

Model

```csharp
public class LoginRequest
{
    public string Username { get; set; }

    public string Password { get; set; }
}
```

---

# 4. User Model

Example entity returned from the database.

```csharp
public class User
{
    public int Id { get; set; }

    public string Username { get; set; }

    public string Email { get; set; }

    public string Role { get; set; }
}
```

---

# 5. User Validation Service

Purpose:

Validate username and password.

Interface

```csharp
public interface IUserService
{
    Task<User?> ValidateUserAsync(LoginRequest request);
}
```

Implementation

```csharp
public class UserService : IUserService
{
    private readonly AppDbContext _context;

    public UserService(AppDbContext context)
    {
        _context = context;
    }

    public async Task<User?> ValidateUserAsync(LoginRequest request)
    {
        return await _context.Users
            .FirstOrDefaultAsync(x =>
                x.Username == request.Username &&
                x.Password == request.Password);
    }
}
```

> **Note:** In production, passwords should never be stored in plain text. Store hashed passwords and verify them using a secure password hashing algorithm.

---

# 6. JWT Service

Purpose:

Generate JWT tokens after successful authentication.

Interface

```csharp
public interface IJwtService
{
    string GenerateToken(User user);
}
```

---

# 7. Creating Claims

Claims are pieces of information about the authenticated user.

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

Example JWT Payload

```json
{
    "sub":"101",
    "name":"Rakesh",
    "email":"rakesh@example.com",
    "role":"Admin"
}
```

Claims are later available through `HttpContext.User`.

---

# 8. Secret Key

Read the secret key from configuration.

```csharp
var key =
new SymmetricSecurityKey(
Encoding.UTF8.GetBytes(
_configuration["Jwt:Key"]));
```

Purpose:

Create the cryptographic key used to sign the JWT.

---

# 9. Signing Credentials

```csharp
var credentials =
new SigningCredentials(
key,
SecurityAlgorithms.HmacSha256);
```

Purpose:

Sign the JWT using the HS256 algorithm.

---

# 10. Generate JWT Token

```csharp
var token =
new JwtSecurityToken(
issuer: _configuration["Jwt:Issuer"],
audience: _configuration["Jwt:Audience"],
claims: claims,
expires: DateTime.UtcNow.AddMinutes(60),
signingCredentials: credentials);
```

Convert to string

```csharp
var jwt =
new JwtSecurityTokenHandler()
.WriteToken(token);
```

The client receives this JWT.

---

# 11. AuthController

Example Controller

```csharp
[ApiController]
[Route("api/auth")]
public class AuthController : ControllerBase
{
    [HttpPost("login")]
    public async Task<IActionResult> Login(LoginRequest request)
    {

    }
}
```

---

# 12. Login API Flow

```csharp
var user =
await _userService.ValidateUserAsync(request);

if(user == null)
    return Unauthorized();

var token =
_jwtService.GenerateToken(user);

return Ok(new
{
    AccessToken = token
});
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

Return Token
```

---

# 13. Protecting APIs

Require authentication.

```csharp
[Authorize]
[HttpGet]
public IActionResult GetOrders()
{
    return Ok();
}
```

Requests without a valid JWT receive:

```text
401 Unauthorized
```

Public endpoints can use:

```csharp
[AllowAnonymous]
```

Examples:

* Login
* Register
* Forgot Password

---

# 14. Reading Claims

Retrieve the current user's information.

Username

```csharp
User.Identity?.Name
```

Email

```csharp
User.FindFirst(
ClaimTypes.Email)?.Value
```

Role

```csharp
User.FindFirst(
ClaimTypes.Role)?.Value
```

User ID

```csharp
User.FindFirst(
JwtRegisteredClaimNames.Sub)?.Value
```

These values come directly from the validated JWT.

---

# 15. Internal Request Lifecycle

```text
Client

↓

Authorization Header

↓

Authentication Middleware

↓

Read Bearer Token

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

Controller Executes
```

---

# 16. Best Practices

* Store passwords as hashes, never plain text.
* Use short-lived access tokens (15–60 minutes).
* Use Refresh Tokens for long sessions.
* Store the signing key securely (Key Vault, Secrets Manager, environment variables).
* Always use HTTPS.
* Do not place sensitive information in JWT claims.
* Keep claims small to reduce token size.

---

# 17. Common Interview Questions

### What is JwtSecurityToken?

Represents a JWT before it is serialized into a string.

---

### Why are Claims used?

Claims carry user identity and authorization data inside the token.

---

### What is SymmetricSecurityKey?

A cryptographic key created from the application's secret key for signing and validating JWTs.

---

### What is SigningCredentials?

Defines which key and algorithm are used to sign the JWT.

---

### What is JwtSecurityTokenHandler?

Used to serialize (`WriteToken`) and deserialize/validate JWTs.

---

### How does `[Authorize]` work?

The authentication middleware validates the JWT and creates a `ClaimsPrincipal` stored in `HttpContext.User`. The authorization middleware then checks whether the endpoint requires authentication, roles, or policies before allowing execution.

---

### What happens if the token has been modified?

The signature validation fails and ASP.NET Core returns **401 Unauthorized**.

---

### What happens if the token has expired?

Lifetime validation fails and the request is rejected with **401 Unauthorized**.

---

# 18. Key Takeaways

* Login requests are validated by the `UserService`.
* After successful authentication, `JwtService` generates the JWT.
* JWTs contain **Claims** describing the authenticated user.
* The token is digitally signed using a **Secret Key**.
* Clients send the JWT in the `Authorization: Bearer <token>` header.
* JWT Middleware validates the token and creates `HttpContext.User`.
* `[Authorize]` protects endpoints by requiring a valid authenticated user.
* Always secure JWT implementations using HTTPS, hashed passwords, short-lived access tokens, and secure key storage.
