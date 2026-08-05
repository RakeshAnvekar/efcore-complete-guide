# JWT Authentication in ASP.NET Core

# README 1 – JWT Fundamentals

> **Interview Level:** Senior / Lead .NET Developer

---

# Table of Contents

1. What is Authentication?
2. What is Authorization?
3. Authentication vs Authorization
4. Why Authentication is Required
5. Traditional Session Authentication
6. Problems with Session Authentication
7. What is JWT?
8. Why JWT?
9. JWT Architecture
10. JWT Token Structure
11. Header
12. Payload (Claims)
13. Signature
14. JWT Authentication Flow
15. Stateless Authentication
16. JWT Lifecycle
17. Advantages & Disadvantages
18. Session vs JWT
19. Common Interview Questions
20. Key Takeaways

---

# 1. What is Authentication?

Authentication is the process of **verifying the identity of a user**.

In simple terms:

> **Authentication answers the question: "Who are you?"**

### Example

A user logs in with:

* Username: `rakesh`
* Password: `Password@123`

The server checks the credentials against the database.

```text
User

    │

Username + Password

    │

    ▼

Authentication Server

    │

Validate Credentials

    │

    ▼

Database

    │

User Exists?

    │

    ▼

Yes

    │

Authenticated
```

---

# 2. What is Authorization?

Authorization determines **what an authenticated user is allowed to access**.

> **Authorization answers the question: "What are you allowed to do?"**

Example:

| User   | Role     | Access                      |
| ------ | -------- | --------------------------- |
| Rakesh | Admin    | Create, Update, Delete      |
| John   | Customer | View Products, Place Orders |

---

# 3. Authentication vs Authorization

| Authentication      | Authorization                   |
| ------------------- | ------------------------------- |
| Identifies the user | Determines permissions          |
| Happens first       | Happens after authentication    |
| Uses credentials    | Uses roles, claims, or policies |
| Login process       | Access control                  |

Flow:

```text
User Login

     │

Authentication

     │

Authenticated User

     │

Authorization

     │

Access Granted / Denied
```

---

# 4. Why Authentication is Required?

Imagine an Online Banking API.

Endpoint:

```http
POST /api/account/transfer
```

Without authentication, anyone could call the API and transfer money.

Authentication protects sensitive APIs by ensuring only valid users can access them.

---

# 5. Traditional Session Authentication

Before JWT became popular, applications used **server-side sessions**.

Flow:

```text
Client

    │

Login

    │

    ▼

Server

    │

Validate User

    │

Create Session

    │

Store Session in Server Memory

    │

Return Session Cookie

    │

Browser Stores Cookie
```

Every subsequent request includes the session cookie.

The server looks up the session in memory to identify the user.

---

# Problems with Session Authentication

### 1. Server Memory Usage

Each logged-in user requires a session stored on the server.

```text
1000 Users

↓

1000 Sessions in Memory
```

---

### 2. Scaling Problems

Consider multiple servers behind a load balancer.

```text
              Load Balancer

              /          \

         Server 1      Server 2

        Session A       No Session
```

If the next request reaches another server, the session may not exist unless session state is shared.

---

# 6. What is JWT?

JWT stands for:

**JSON Web Token**

JWT is a compact, digitally signed token used for **stateless authentication**.

Instead of storing session information on the server, the server sends a token to the client.

The client sends that token with every request.

---

# 7. Why JWT?

Benefits:

* Stateless
* Scalable
* Suitable for APIs
* Works well with mobile apps
* Works well with microservices
* No server-side session storage

---

# 8. JWT Architecture

```text
                 Client

                   │

                Login

                   │

                   ▼

         Authentication API

                   │

         Validate Username

         Validate Password

                   │

                   ▼

             SQL Server

                   │

          Generate JWT Token

                   │

                   ▼

          Return JWT Token

                   │

Client Stores Token

(Local Storage / Secure Cookie)

                   │

Authorization Header

Bearer <token>

                   │

                   ▼

Protected API

                   │

JWT Middleware

                   │

Validate Token

                   │

Controller
```

---

# 9. JWT Token Structure

A JWT contains three parts separated by dots.

```text
Header.Payload.Signature
```

Example:

```text
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJzdWIiOiIxIiwibmFtZSI6IlJha2VzaCIsInJvbGUiOiJBZG1pbiJ9
.
abc123xyz456...
```

---

# 10. Header

The Header describes the token.

Example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Meaning:

* `alg` → Signing algorithm
* `typ` → Token type (JWT)

---

# 11. Payload

The Payload contains **Claims**.

Claims are pieces of information about the user.

Example:

```json
{
  "sub": "101",
  "name": "Rakesh",
  "email": "rakesh@example.com",
  "role": "Admin"
}
```

Common Claims:

| Claim | Meaning         |
| ----- | --------------- |
| sub   | User ID         |
| name  | User Name       |
| email | Email Address   |
| role  | User Role       |
| iss   | Issuer          |
| aud   | Audience        |
| exp   | Expiration Time |

**Important:** The payload is **Base64Url encoded, not encrypted**. Anyone with the token can decode it, so never store passwords or sensitive information in it.

---

# 12. Signature

The Signature protects the token from tampering.

It is created using:

* Header
* Payload
* Secret Key

```text
Header

+

Payload

+

Secret Key

↓

Hash Algorithm (HS256)

↓

Digital Signature
```

If someone changes the payload (for example, changes `"role": "Customer"` to `"role": "Admin"`), the signature no longer matches, and the server rejects the token.

---

# 13. JWT Authentication Flow

```text
User

    │

Login

    │

    ▼

Authentication API

    │

Validate Username & Password

    │

Generate JWT

    │

Return JWT

    │

Client Stores JWT

    │

Every Request:

Authorization: Bearer <JWT>

    │

JWT Middleware

    │

Validate Token

    │

Controller Executes
```

---

# 14. Stateless Authentication

JWT is called **Stateless Authentication** because:

* The server does **not** store user sessions.
* Every request contains all the information needed to identify the user.

Session Authentication:

```text
Server Stores Session
```

JWT Authentication:

```text
Client Stores Token

↓

Server Validates Token

↓

No Session Storage
```

---

# 15. JWT Lifecycle

```text
User Login

      │

Generate Token

      │

Return Token

      │

Client Stores Token

      │

Call Protected APIs

      │

Server Validates Token

      │

Token Expires

      │

Login Again or Use Refresh Token
```

---

# 16. Advantages

* Stateless authentication
* Easy to scale
* No session memory
* Works with mobile applications
* Works with microservices
* Fast authentication

---

# 17. Disadvantages

* Cannot easily revoke a token before it expires without additional infrastructure.
* Token size is larger than a session ID.
* Requires HTTPS to protect tokens in transit.
* Requires Refresh Tokens for long-lived sessions.

---

# 18. Session Authentication vs JWT Authentication

| Session Authentication      | JWT Authentication                        |
| --------------------------- | ----------------------------------------- |
| Server stores session       | Client stores token                       |
| Stateful                    | Stateless                                 |
| Uses cookies                | Uses Bearer token                         |
| Requires server memory      | No server-side session memory             |
| Harder to scale             | Easy to scale                             |
| Easy to invalidate sessions | Revocation requires additional mechanisms |

---

# 19. Common Interview Questions

### What is JWT?

JWT (JSON Web Token) is a compact, signed token used for stateless authentication and authorization.

---

### Why use JWT instead of Session Authentication?

JWT eliminates server-side session storage, making applications easier to scale and more suitable for APIs and microservices.

---

### What are the three parts of a JWT?

1. Header
2. Payload
3. Signature

---

### Is JWT encrypted?

No.

JWT is **encoded**, not encrypted.

The payload can be decoded by anyone.

Only the Signature protects against modification.

---

### Why should HTTPS always be used with JWT?

Because anyone intercepting the token can use it until it expires if it is transmitted over an insecure connection.

---

### What is Stateless Authentication?

The server does not store user session data. Each request includes the JWT, and the server validates it independently.

---

# 20. Key Takeaways

* Authentication verifies **who the user is**.
* Authorization determines **what the user can access**.
* JWT is a **stateless authentication mechanism**.
* A JWT consists of **Header**, **Payload**, and **Signature**.
* The payload contains **claims** and is readable.
* The signature ensures the token has not been modified.
* JWT is ideal for modern APIs, SPAs, mobile apps, and microservices.
* Always transmit JWTs over **HTTPS** and never include sensitive data in the payload.
