# ASP.NET Core Configuration with Azure Key Vault - Complete Guide

---

# Overview

In enterprise applications, we typically have multiple environments:

- Development (DEV)
- User Acceptance Testing (UAT)
- Production (PROD)

Each environment has:

- Separate App Service
- Separate Key Vault
- Separate Database

The application code remains the same, but configuration changes based on the environment.

---

# Typical Environment Setup

```text
Development
    |
    +--> employee-dev-api
    +--> employee-dev-kv
    +--> EmployeeDevDB

-----------------------------------

UAT
    |
    +--> employee-uat-api
    +--> employee-uat-kv
    +--> EmployeeUATDB

-----------------------------------

Production
    |
    +--> employee-prod-api
    +--> employee-prod-kv
    +--> EmployeeProdDB
```

---

# Do We Need Multiple appsettings Files?

Yes.

Typically:

```text
appsettings.json

appsettings.Development.json

appsettings.UAT.json

appsettings.Production.json
```

ASP.NET Core automatically loads the correct file based on the environment.

---

# Purpose of Each File

## appsettings.json

Contains common settings shared across all environments.

Example:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

---

## appsettings.Development.json

Contains Development-specific settings.

Example:

```json
{
  "KeyVaultUrl": "https://employee-dev-kv.vault.azure.net/"
}
```

---

## appsettings.UAT.json

Contains UAT-specific settings.

Example:

```json
{
  "KeyVaultUrl": "https://employee-uat-kv.vault.azure.net/"
}
```

---

## appsettings.Production.json

Contains Production-specific settings.

Example:

```json
{
  "KeyVaultUrl": "https://employee-prod-kv.vault.azure.net/"
}
```

---

# Important Security Rule

Never store secrets inside appsettings files.

❌ Bad Example

```json
{
  "DbConnectionString":
    "Server=prodsql.database.windows.net;Database=EmployeeProdDB",

  "OpenAIApiKey":
    "sk-123456"
}
```

Problems:

- Secrets exposed
- Source control risk
- Security vulnerability

---

# Correct Approach

Store secrets in Azure Key Vault.

Example:

```text
employee-prod-kv

Secrets

DbConnectionString
StorageConnectionString
OpenAIApiKey
RedisConnectionString
```

Only store:

```json
{
  "KeyVaultUrl":
    "https://employee-prod-kv.vault.azure.net/"
}
```

inside configuration.

---

# How ASP.NET Core Chooses the Correct File

ASP.NET Core uses:

```text
ASPNETCORE_ENVIRONMENT
```

Environment variable.

---

## Development

```text
ASPNETCORE_ENVIRONMENT=Development
```

Loads:

```text
appsettings.json

+

appsettings.Development.json
```

---

## UAT

```text
ASPNETCORE_ENVIRONMENT=UAT
```

Loads:

```text
appsettings.json

+

appsettings.UAT.json
```

---

## Production

```text
ASPNETCORE_ENVIRONMENT=Production
```

Loads:

```text
appsettings.json

+

appsettings.Production.json
```

---

# Configuration Loading Process

```text
Application Starts
        |
        v
appsettings.json
        |
        v
Environment Specific File
        |
        v
KeyVaultUrl Retrieved
        |
        v
Azure Key Vault Connected
        |
        v
Secrets Loaded
```

---

# Program.cs Example

```csharp
using Azure.Identity;

var builder = WebApplication.CreateBuilder(args);

var keyVaultUrl =
    builder.Configuration["KeyVaultUrl"];

builder.Configuration.AddAzureKeyVault(
    new Uri(keyVaultUrl),
    new DefaultAzureCredential());
```

---

# Reading Secrets

Suppose Key Vault contains:

```text
DbConnectionString
```

Read it like this:

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

# Runtime Example - Development

```text
employee-dev-api Starts
        |
        v
appsettings.Development.json
        |
        v
employee-dev-kv URL
        |
        v
Azure Key Vault
        |
        v
DbConnectionString
        |
        v
EmployeeDevDB
```

---

# Runtime Example - UAT

```text
employee-uat-api Starts
        |
        v
appsettings.UAT.json
        |
        v
employee-uat-kv URL
        |
        v
Azure Key Vault
        |
        v
DbConnectionString
        |
        v
EmployeeUATDB
```

---

# Runtime Example - Production

```text
employee-prod-api Starts
        |
        v
appsettings.Production.json
        |
        v
employee-prod-kv URL
        |
        v
Azure Key Vault
        |
        v
DbConnectionString
        |
        v
EmployeeProdDB
```

---

# Better Enterprise Approach

Many companies avoid storing even the Key Vault URL inside appsettings.

Instead they use Azure App Service Configuration.

Example:

```text
App Service

Configuration

KeyVaultUrl =
https://employee-prod-kv.vault.azure.net/
```

Then:

```csharp
var keyVaultUrl =
    builder.Configuration["KeyVaultUrl"];
```

Benefits:

- No environment-specific file changes
- Easier deployments
- Centralized configuration
- Better security

---

# Recommended Enterprise Architecture

```text
DEV

employee-dev-api
      |
      +--> employee-dev-kv
      |
      +--> EmployeeDevDB

================================

UAT

employee-uat-api
      |
      +--> employee-uat-kv
      |
      +--> EmployeeUATDB

================================

PROD

employee-prod-api
      |
      +--> employee-prod-kv
      |
      +--> EmployeeProdDB
```

---

# Interview Questions

## Why do we have multiple appsettings files?

To manage environment-specific configuration without changing application code.

---

## Should connection strings be stored in appsettings?

No.

Sensitive information should be stored in Azure Key Vault.

---

## What should appsettings contain?

Examples:

- Key Vault URL
- Logging configuration
- Feature flags
- Non-sensitive application settings

---

## What determines which appsettings file is loaded?

The ASPNETCORE_ENVIRONMENT environment variable.

---

## What changes between DEV, UAT, and PROD?

Usually:

- Key Vault URL
- Database
- Storage Account
- External Service Endpoints

Application code remains unchanged.

---

# Key Interview Answer

ASP.NET Core supports environment-specific configuration through files such as appsettings.Development.json, appsettings.UAT.json, and appsettings.Production.json. Sensitive values like connection strings, passwords, and API keys should not be stored in these files. Instead, they should be stored in Azure Key Vault, while the configuration files contain only non-sensitive values such as the Key Vault URL. This enables secure, environment-specific configuration with the same codebase across Development, UAT, and Production.