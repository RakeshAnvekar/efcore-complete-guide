# Azure Key Vault Across Dev, UAT, and Production - Complete Guide

---

# Overview

In enterprise applications, we do not use a single Key Vault for all environments.

Instead, each environment gets its own:

- App Service
- Key Vault
- Database
- Storage Account

This ensures complete isolation between environments.

---

# Environment Architecture

```text
Development
    |
    +--> Employee-Dev-API
    +--> Employee-Dev-KV
    +--> EmployeeDevDB

-----------------------------------

UAT
    |
    +--> Employee-UAT-API
    +--> Employee-UAT-KV
    +--> EmployeeUATDB

-----------------------------------

Production
    |
    +--> Employee-Prod-API
    +--> Employee-Prod-KV
    +--> EmployeeProdDB
```

---

# Why Separate Key Vaults?

Suppose all environments share the same Key Vault.

```text
Employee-KeyVault

DbConnectionString
StorageConnectionString
RedisConnectionString
```

Problems:

- Dev may accidentally access Production database.
- UAT may overwrite Production secrets.
- Security risks.
- No environment isolation.

Therefore, separate Key Vaults are recommended.

---

# Recommended Enterprise Setup

## Development

```text
App Service:
employee-dev-api

Key Vault:
employee-dev-kv

Database:
EmployeeDevDB
```

---

## UAT

```text
App Service:
employee-uat-api

Key Vault:
employee-uat-kv

Database:
EmployeeUATDB
```

---

## Production

```text
App Service:
employee-prod-api

Key Vault:
employee-prod-kv

Database:
EmployeeProdDB
```

---

# Key Vault Secrets Example

## Development Key Vault

```text
employee-dev-kv

Secrets

DbConnectionString
StorageConnectionString
RedisConnectionString
OpenAIApiKey
```

Example:

```text
DbConnectionString

Server=devsql.database.windows.net;
Database=EmployeeDevDB;
```

---

## UAT Key Vault

```text
employee-uat-kv

Secrets

DbConnectionString
StorageConnectionString
RedisConnectionString
OpenAIApiKey
```

Example:

```text
DbConnectionString

Server=uatsql.database.windows.net;
Database=EmployeeUATDB;
```

---

## Production Key Vault

```text
employee-prod-kv

Secrets

DbConnectionString
StorageConnectionString
RedisConnectionString
OpenAIApiKey
```

Example:

```text
DbConnectionString

Server=prodsql.database.windows.net;
Database=EmployeeProdDB;
```

---

# Managed Identity Setup

Every App Service receives its own Managed Identity.

## Development

```text
employee-dev-api
      |
      +--> Managed Identity A
```

---

## UAT

```text
employee-uat-api
      |
      +--> Managed Identity B
```

---

## Production

```text
employee-prod-api
      |
      +--> Managed Identity C
```

---

# Important Concept

Managed Identity only creates an identity.

It DOES NOT grant permissions.

```text
Managed Identity
      ≠
Permission
```

Permissions must be granted separately using IAM.

---

# IAM Configuration

## Development

```text
employee-dev-kv
       |
       +--> Access Control (IAM)
                |
                +--> Key Vault Secrets User
                         |
                         +--> employee-dev-api
```

Only Development API can access Development Key Vault.

---

## UAT

```text
employee-uat-kv
       |
       +--> Access Control (IAM)
                |
                +--> Key Vault Secrets User
                         |
                         +--> employee-uat-api
```

Only UAT API can access UAT Key Vault.

---

## Production

```text
employee-prod-kv
       |
       +--> Access Control (IAM)
                |
                +--> Key Vault Secrets User
                         |
                         +--> employee-prod-api
```

Only Production API can access Production Key Vault.

---

# Security Isolation

Correct Setup:

```text
employee-dev-api
       |
       +--> employee-dev-kv

employee-uat-api
       |
       +--> employee-uat-kv

employee-prod-api
       |
       +--> employee-prod-kv
```

---

Incorrect Setup:

```text
employee-dev-api
employee-uat-api
employee-prod-api
       |
       +--> One Shared Key Vault
```

This is not recommended.

---

# Configuration Per Environment

Usually only the Key Vault URL changes.

## Development

```json
{
  "KeyVaultUrl":
  "https://employee-dev-kv.vault.azure.net/"
}
```

---

## UAT

```json
{
  "KeyVaultUrl":
  "https://employee-uat-kv.vault.azure.net/"
}
```

---

## Production

```json
{
  "KeyVaultUrl":
  "https://employee-prod-kv.vault.azure.net/"
}
```

---

# ASP.NET Core Configuration

Program.cs

```csharp
using Azure.Identity;

var builder = WebApplication.CreateBuilder(args);

builder.Configuration.AddAzureKeyVault(
    new Uri(builder.Configuration["KeyVaultUrl"]),
    new DefaultAzureCredential());
```

---

# Reading Secrets

```csharp
var connectionString =
    builder.Configuration["DbConnectionString"];
```

---

# EF Core Example

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
{
    options.UseSqlServer(
        builder.Configuration["DbConnectionString"]);
});
```

---

# Runtime Flow

Development Example

```text
employee-dev-api Starts
          |
          v
DefaultAzureCredential()
          |
          v
Managed Identity Token
          |
          v
employee-dev-kv
          |
          v
IAM Permission Check
          |
          v
DbConnectionString Returned
          |
          v
EmployeeDevDB Connected
```

---

# Complete Enterprise Architecture

```text
DEV

employee-dev-api
      |
      +--> Managed Identity
      |
      +--> employee-dev-kv
      |
      +--> EmployeeDevDB

=================================================

UAT

employee-uat-api
      |
      +--> Managed Identity
      |
      +--> employee-uat-kv
      |
      +--> EmployeeUATDB

=================================================

PROD

employee-prod-api
      |
      +--> Managed Identity
      |
      +--> employee-prod-kv
      |
      +--> EmployeeProdDB
```

---

# Interview Questions

## Why separate Key Vaults for Dev, UAT, and Production?

To ensure:

- Security
- Environment isolation
- Independent secret management
- Reduced risk of accessing Production resources from lower environments

---

## Why separate Managed Identities?

Each environment should have its own identity and permissions.

This prevents:

```text
Dev Application
      |
      +--> Accessing Production Secrets
```

---

## What changes between environments?

Typically:

- Key Vault URL
- Database Connection String
- Storage Account Connection String
- API Keys

The application code remains the same.

---

# Key Interview Answer

In enterprise applications, each environment (Dev, UAT, and Production) typically has its own App Service, Managed Identity, Key Vault, and Database. Each App Service is granted access only to its corresponding Key Vault through IAM roles such as "Key Vault Secrets User". This ensures security, environment isolation, and prevents accidental access to production secrets from non-production environments.