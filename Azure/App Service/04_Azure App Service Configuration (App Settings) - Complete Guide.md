# Azure App Service Configuration (App Settings) - Complete Guide

---

# Overview

Azure App Service Configuration is used to store application settings and environment variables outside the application code.

Instead of hardcoding values inside the application, we store them in Azure and read them at runtime.

Examples:

- Key Vault URL
- API Endpoints
- Feature Flags
- Environment Names
- External Service URLs

---

# Why Do We Need App Service Configuration?

Suppose we write:

```csharp
var keyVaultUrl =
"https://employee-prod-kv.vault.azure.net/";
```

Problems:

- Hardcoded value
- Difficult to change
- Requires redeployment
- Different values needed for DEV/UAT/PROD

Not recommended.

---

# Better Approach

Store values in Azure App Service Configuration.

```text
App Service
      |
      +--> Configuration
               |
               +--> KeyVaultUrl
               |
               +--> ApiUrl
               |
               +--> FeatureFlag
```

Application reads values dynamically.

---

# Azure Portal Location

Navigate to:

```text
Azure Portal
      |
      +--> App Service
                |
                +--> Settings
                         |
                         +--> Environment Variables
```

Older Azure Portal versions may show:

```text
App Service
      |
      +--> Configuration
```

---

# Example Configuration

## Development

```text
KeyVaultUrl

https://employee-dev-kv.vault.azure.net/
```

---

## UAT

```text
KeyVaultUrl

https://employee-uat-kv.vault.azure.net/
```

---

## Production

```text
KeyVaultUrl

https://employee-prod-kv.vault.azure.net/
```

Same application code.

Different configuration values.

---

# Reading Configuration in ASP.NET Core

Example:

```csharp
var keyVaultUrl =
builder.Configuration["KeyVaultUrl"];
```

ASP.NET Core automatically reads values from App Service Configuration.

---

# Real World Example

Azure Configuration:

```text
KeyVaultUrl
=
https://employee-prod-kv.vault.azure.net/

--------------------------------

OpenAIEndpoint
=
https://prod-openai.openai.azure.com

--------------------------------

FeatureXEnabled
=
true
```

Application:

```csharp
var keyVaultUrl =
builder.Configuration["KeyVaultUrl"];

var openAiEndpoint =
builder.Configuration["OpenAIEndpoint"];

var featureEnabled =
builder.Configuration["FeatureXEnabled"];
```

---

# Environment Variables

Azure App Service Configuration is essentially a collection of environment variables.

Example:

```text
ASPNETCORE_ENVIRONMENT
=
Production
```

Code:

```csharp
var environment =
builder.Configuration["ASPNETCORE_ENVIRONMENT"];
```

Output:

```text
Production
```

---

# Development vs UAT vs Production

## Development

```text
employee-dev-api

Configuration

KeyVaultUrl
=
https://employee-dev-kv.vault.azure.net/
```

---

## UAT

```text
employee-uat-api

Configuration

KeyVaultUrl
=
https://employee-uat-kv.vault.azure.net/
```

---

## Production

```text
employee-prod-api

Configuration

KeyVaultUrl
=
https://employee-prod-kv.vault.azure.net/
```

---

# Configuration Loading Order

ASP.NET Core loads configuration from multiple sources.

Typical order:

```text
appsettings.json
       |
       v
appsettings.{Environment}.json
       |
       v
Environment Variables
       |
       v
Azure App Service Configuration
```

Values loaded later override earlier values.

---

# Example

## appsettings.json

```json
{
  "ApiUrl": "LocalApi"
}
```

---

## Azure Configuration

```text
ApiUrl
=
ProductionApi
```

---

## Result

```csharp
builder.Configuration["ApiUrl"]
```

Returns:

```text
ProductionApi
```

Azure value overrides local configuration.

---

# Connection Strings

Azure App Service provides a dedicated Connection Strings section.

Example:

```text
Connection Strings

EmployeeDB
=
Server=...
```

Historically used for database connection strings.

---

# Modern Enterprise Approach

Instead of storing secrets directly in App Service:

```text
App Service
      |
      +--> Configuration
               |
               +--> KeyVaultUrl
```

Application:

```csharp
builder.Configuration.AddAzureKeyVault(
    new Uri(builder.Configuration["KeyVaultUrl"]),
    new DefaultAzureCredential());
```

Secrets remain in Azure Key Vault.

---

# Why Not Store Secrets in App Settings?

Bad Example:

```text
DbPassword
=
Admin123
```

Problems:

- Security risk
- Secret exposure
- Difficult auditing

---

# Recommended Approach

Store secrets in:

```text
Azure Key Vault
```

Store only:

```text
KeyVaultUrl
```

in App Service Configuration.

---

# Real Enterprise Architecture

```text
Employee API
      |
      v
Azure App Service
      |
      +--> Configuration
      |        |
      |        +--> KeyVaultUrl
      |
      +--> Managed Identity
      |
      v
Azure Key Vault
      |
      +--> DbConnectionString
      +--> ApiKeys
      +--> Secrets
```

---

# Benefits of App Service Configuration

## Environment Specific Settings

```text
DEV
UAT
PROD
```

can have different values.

---

## No Code Changes

Application code remains identical.

---

## No Redeployment Required

Configuration changes can often be applied without changing source code.

---

## Better Security

No hardcoded values.

---

## Easier Maintenance

Operations teams can update configuration without modifying code.

---

# Common Interview Questions

## What is App Service Configuration?

App Service Configuration is a centralized location for storing environment variables and application settings used by Azure-hosted applications.

---

## How do you access App Service settings in ASP.NET Core?

```csharp
builder.Configuration["SettingName"]
```

---

## Why use App Service Configuration?

To manage environment-specific settings without changing application code.

---

## Should connection strings be stored here?

They can be, but modern applications typically store secrets in Azure Key Vault and store only the Key Vault URL in App Service Configuration.

---

## What types of values are commonly stored?

Examples:

- Key Vault URL
- API URLs
- Feature Flags
- Environment Variables
- Service Endpoints

---

# Interview Answer

Azure App Service Configuration is used to store application settings and environment variables outside the application code. ASP.NET Core automatically reads these settings through the Configuration API. This allows different values for Development, UAT, and Production environments without modifying the codebase. Sensitive information is typically stored in Azure Key Vault, while App Service Configuration stores non-sensitive values such as the Key Vault URL and application-specific settings.

---

# Quick Revision

```text
App Service
      |
      +--> Configuration
               |
               +--> Environment Variables
               |
               +--> App Settings
               |
               +--> KeyVaultUrl
```

Code:

```csharp
builder.Configuration["KeyVaultUrl"]
```

Use App Service Configuration for:

✔ Environment Variables

✔ Feature Flags

✔ API URLs

✔ Key Vault URL

Avoid storing:

❌ Passwords

❌ API Secrets

❌ Database Credentials

Store those in Azure Key Vault.