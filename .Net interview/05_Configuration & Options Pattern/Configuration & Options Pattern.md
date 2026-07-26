# Module 33.1 – Configuration & Options Pattern
# Introduction to Configuration in ASP.NET Core

> **Level:** Beginner → Advanced  
> **Target Audience:** ASP.NET Core Developers | Senior .NET Interview Preparation

---

# Table of Contents

1. What is Configuration?
2. Why Do We Need Configuration?
3. Problems with Hardcoded Values
4. Configuration in ASP.NET Core
5. Configuration Providers
6. IConfiguration
7. Internal Working
8. Configuration Provider Order
9. Real-World Example
10. Advantages
11. Common Mistakes
12. Interview Questions
13. Summary

---

# 1. What is Configuration?

Configuration is the process of storing application settings **outside the source code**, allowing them to be changed without modifying or recompiling the application.

Instead of hardcoding values in your application, they are stored in configuration files, environment variables, secret stores, or other external sources.

Examples of configuration values:

- Database Connection String
- JWT Secret Key
- Email SMTP Settings
- Logging Levels
- Cache Duration
- API Keys
- External API URLs
- Feature Flags

---

# 2. Why Do We Need Configuration?

Imagine an Online Shopping API.

Without configuration:

```csharp
public class ProductService
{
    private readonly string connectionString =
        "Server=SQL01;Database=Shopping;User Id=sa;Password=123456;";
}
```

Problems:

- Password is exposed in source code
- Every environment requires code changes
- Requires recompilation
- Difficult to maintain
- Security risk

---

Instead, we store it in configuration.

Example:

```json
{
  "ConnectionStrings": {
    "DefaultConnection":
      "Server=SQL01;Database=Shopping;Trusted_Connection=True;"
  }
}
```

Access it using:

```csharp
string connectionString =
    configuration.GetConnectionString("DefaultConnection");
```

Now:

- No code changes
- Easy deployment
- Secure
- Environment-specific
- Easy maintenance

---

# 3. Real-World Analogy

Imagine a restaurant.

The chef knows how to cook.

But the chef does not decide:

- Today's menu
- Prices
- Working hours
- Phone number
- Delivery partner

The restaurant manager updates these values whenever required.

The chef simply follows them.

Similarly,

Application = Chef

Configuration = Manager

The application knows **how** to work.

Configuration tells it **what values** to use.

---

# 4. Why Configuration Exists

Applications usually move through multiple environments.

```
Developer Machine

↓

Testing

↓

QA

↓

Staging

↓

Production
```

Each environment has different settings.

Development

```
Database = localhost
Logging = Debug
```

Testing

```
Database = test-server
Logging = Warning
```

Production

```
Database = prod-server
Logging = Error
```

Notice that the application code remains exactly the same.

Only configuration changes.

---

# 5. Configuration in ASP.NET Core

ASP.NET Core includes a built-in configuration system.

When the application starts, it automatically loads configuration from multiple sources.

These sources are called **Configuration Providers**.

Examples:

- appsettings.json
- appsettings.Development.json
- Environment Variables
- Command-Line Arguments
- User Secrets
- Azure Key Vault
- Azure App Configuration
- In-Memory Collection

---

# 6. IConfiguration

ASP.NET Core combines all configuration providers into a single object.

```
IConfiguration
```

This interface provides access to every configuration value.

Example:

```csharp
var builder = WebApplication.CreateBuilder(args);

IConfiguration configuration = builder.Configuration;
```

Injecting IConfiguration:

```csharp
public class ProductService
{
    private readonly IConfiguration configuration;

    public ProductService(IConfiguration configuration)
    {
        this.configuration = configuration;
    }
}
```

Reading a value:

```csharp
string appName =
    configuration["ApplicationName"];
```

Reading a connection string:

```csharp
string connectionString =
    configuration.GetConnectionString("DefaultConnection");
```

---

# 7. Configuration Providers

Configuration providers are different sources that supply configuration values.

Examples:

```
Configuration

├── appsettings.json
├── appsettings.Development.json
├── Environment Variables
├── User Secrets
├── Azure Key Vault
├── Azure App Configuration
├── Command-Line Arguments
└── In-Memory Collection
```

All these providers work together to build one IConfiguration object.

---

# 8. Internal Working

When the application starts:

```
Program.cs

↓

WebApplication.CreateBuilder()

↓

ConfigurationBuilder

↓

Loads Providers

↓

Combines Values

↓

Builds IConfiguration

↓

Application Starts
```

The configuration pipeline is created before any HTTP request reaches your application.

---

# 9. Configuration Provider Priority

Sometimes the same key exists in multiple providers.

Example:

appsettings.json

```json
{
  "Logging": {
    "Level": "Information"
  }
}
```

Environment Variable

```
Logging__Level=Error
```

Final Result

```
Logging Level = Error
```

Reason:

Environment Variables override appsettings.json.

General Rule:

**The last provider added has the highest priority.**

---

# 10. Why Multiple Providers?

Different values belong in different places.

Example:

Connection String

→ appsettings.json

API Secret

→ Azure Key Vault

Temporary Override

→ Environment Variable

Testing Value

→ Command-Line Argument

Although values come from different locations, your application reads them through the same IConfiguration interface.

---

# 11. Advantages

✔ No hardcoded values

✔ Better security

✔ Easy deployment

✔ Environment-specific settings

✔ No recompilation

✔ Cloud-friendly

✔ Easy maintenance

✔ Better separation of concerns

---

# 12. Common Mistakes

## Hardcoding Connection Strings

❌ Bad

```csharp
string cs =
"Server=SQL01;Database=Shop;Password=123";
```

✔ Good

```csharp
configuration.GetConnectionString("DefaultConnection");
```

---

## Storing Secrets in Git

Never commit:

- API Keys
- Passwords
- Connection Strings
- JWT Secrets

Use:

- User Secrets
- Environment Variables
- Azure Key Vault

---

## Editing Code for Every Environment

Bad Practice

```
if(Debug)
Database="Local";

else

Database="Production";
```

Instead,

Use environment-specific configuration files.

---

# 13. Real-World Example

Suppose you have:

```
Shopping API
```

Development

```
Database

localhost

Logging

Debug
```

Production

```
Database

Azure SQL

Logging

Error
```

Only configuration changes.

Application code remains exactly the same.

---

# 14. Interview Questions

## Q1. What is Configuration?

Configuration is a mechanism to store application settings outside the source code so they can be modified without recompiling the application.

---

## Q2. Why is Configuration Important?

Configuration:

- Improves maintainability
- Increases security
- Supports multiple environments
- Removes hardcoded values

---

## Q3. What is IConfiguration?

IConfiguration is the central interface used to access configuration values from multiple providers.

---

## Q4. What are Configuration Providers?

Configuration providers are sources that supply configuration values.

Examples:

- appsettings.json
- Environment Variables
- User Secrets
- Azure Key Vault
- Command-Line Arguments

---

## Q5. Which Provider Takes Priority?

The provider added last overrides previous providers if duplicate keys exist.

---

## Q6. Why Not Hardcode Values?

Hardcoded values:

- Reduce flexibility
- Increase maintenance effort
- Create security risks
- Require recompilation

---

# 15. Key Takeaways

✅ Configuration stores application settings outside the source code.

✅ ASP.NET Core automatically loads configuration during application startup.

✅ IConfiguration provides access to all configuration values.

✅ Configuration Providers include JSON files, Environment Variables, User Secrets, Azure Key Vault, and more.

✅ Provider order determines which value wins when duplicate keys exist.

✅ Never hardcode secrets or connection strings.

---

# Architecture Diagram

```
                 Configuration Sources

                appsettings.json
                        │
                        ▼

        appsettings.Development.json
                        │
                        ▼

            Environment Variables
                        │
                        ▼

                User Secrets
                        │
                        ▼

               Azure Key Vault
                        │
                        ▼

          Command-Line Arguments
                        │
                        ▼

             Configuration Builder
                        │
                        ▼

                IConfiguration
                        │
                        ▼

      Controllers • Services • Repositories
```

---

# Next Module

## Module 33.2 – appsettings.json

In the next module, we will learn:

- Structure of appsettings.json
- Connection Strings
- Nested Configuration
- Arrays
- appsettings.Development.json
- appsettings.Production.json
- Environment-specific Configuration
- Best Practices
- Real-world Examples
- Interview Questions