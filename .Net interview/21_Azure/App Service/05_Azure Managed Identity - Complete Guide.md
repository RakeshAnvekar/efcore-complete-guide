# Azure Managed Identity - Complete Guide

---

# Overview

Managed Identity is an Azure feature that allows Azure resources to securely authenticate with other Azure services without storing usernames, passwords, client IDs, or client secrets.

It is commonly used with:

- Azure Key Vault
- Azure Storage Account
- Azure SQL Database
- Azure Service Bus
- Azure Event Hub

---

# Why Do We Need Managed Identity?

Imagine your application needs to access Azure Key Vault.

Traditional approach:

```csharp
ClientId = "abc123";
ClientSecret = "secret123";
TenantId = "tenant123";
```

Problems:

- Secret stored in code
- Secret stored in appsettings.json
- Secret rotation issues
- Security risks
- Possible credential leaks

---

# Managed Identity Solution

Azure creates and manages the identity automatically.

```text
Employee API
      |
      +--> Managed Identity
```

No username.

No password.

No client secret.

---

# Real World Example

Suppose:

```text
Employee API
```

needs:

```text
Database Connection String
```

stored inside:

```text
Azure Key Vault
```

Architecture:

```text
Employee API
      |
Managed Identity
      |
Azure Key Vault
      |
DbConnectionString
```

The application accesses Key Vault securely without credentials.

---

# Managed Identity Types

Azure provides two types of Managed Identity.

---

# 1. System Assigned Managed Identity

Most commonly used.

Created directly on an Azure resource.

Example:

```text
App Service
      |
      +--> Identity
               |
               +--> System Assigned = ON
```

Azure automatically creates an identity.

---

# Lifecycle

When App Service is created:

```text
App Service Created
       |
       v
Managed Identity Created
```

When App Service is deleted:

```text
App Service Deleted
       |
       v
Managed Identity Deleted
```

Identity exists only for that resource.

---

# Example

```text
employee-api

Managed Identity
```

Identity belongs only to:

```text
employee-api
```

---

# 2. User Assigned Managed Identity

Created as a separate Azure resource.

Example:

```text
User Assigned Identity
        |
        +--> Employee API
        +--> Customer API
        +--> Payment API
```

One identity can be shared by multiple applications.

---

# Lifecycle

```text
Delete App Service
      |
      v
Identity Still Exists
```

Because it is an independent resource.

---

# Comparison

## System Assigned

```text
One Identity
One Resource
```

Deleted with resource.

---

## User Assigned

```text
One Identity
Multiple Resources
```

Independent lifecycle.

---

# Most Enterprise Applications Use

```text
System Assigned Managed Identity
```

because it is simple and secure.

---

# Enabling Managed Identity

Azure Portal:

```text
Azure Portal
      |
      +--> App Service
                |
                +--> Identity
                          |
                          +--> System Assigned
                                    |
                                    +--> ON
```

Click:

```text
Save
```

Azure automatically creates the identity.

---

# Important Concept

Enabling Managed Identity does NOT grant permissions.

Many beginners misunderstand this.

Example:

```text
Managed Identity Created
```

does NOT mean:

```text
Access Granted
```

---

# Think Like an Office Building

Employee receives:

```text
Employee ID Card
```

This means:

```text
Identity Exists
```

But can the employee enter every room?

```text
No
```

Permission is still required.

Managed Identity works the same way.

---

# Identity vs Permission

```text
Managed Identity
      =
Who Am I?

IAM Role
      =
What Can I Do?
```

Very important interview concept.

---

# Step 1 - Enable Managed Identity

```text
Employee API
      |
      +--> Managed Identity Created
```

---

# Step 2 - Grant Access

Navigate to:

```text
Azure Key Vault
      |
      +--> Access Control (IAM)
```

Add Role Assignment:

```text
Key Vault Secrets User
```

Assign To:

```text
Employee API
```

Now access is granted.

---

# Complete Flow

```text
Employee API
      |
Managed Identity
      |
IAM Role Check
      |
Azure Key Vault
      |
Secret Returned
```

---

# Azure Portal Flow

## App Service

```text
App Service
      |
      +--> Identity
               |
               +--> System Assigned = ON
```

---

## Key Vault

```text
Key Vault
      |
      +--> Access Control (IAM)
      |
      +--> Add Role Assignment
```

Select:

```text
Key Vault Secrets User
```

Assign to:

```text
Employee API
```

---

# Runtime Authentication Flow

Application starts.

```text
Employee API
      |
DefaultAzureCredential()
      |
Managed Identity Token
      |
Azure Key Vault
      |
Role Validation
      |
Secret Returned
```

---

# Code Example

Install package:

```bash
dotnet add package Azure.Identity
```

---

# Reading Key Vault

```csharp
using Azure.Identity;

builder.Configuration.AddAzureKeyVault(
    new Uri("https://employee-kv.vault.azure.net/"),
    new DefaultAzureCredential());
```

---

# What is DefaultAzureCredential?

Azure SDK authentication helper.

Example:

```csharp
new DefaultAzureCredential()
```

Automatically chooses the correct authentication method.

---

# Local Development

While running locally:

```text
Visual Studio Login

or

Azure CLI Login
```

Used automatically.

---

# Azure Deployment

While running inside Azure:

```text
Managed Identity
```

Used automatically.

No code changes required.

---

# Complete Architecture

```text
Employee API
(App Service)
      |
      +--> Managed Identity
      |
      v
Azure Key Vault
      |
      +--> DbConnectionString
      +--> StorageConnectionString
      +--> OpenAIApiKey
      +--> ServiceBusConnection
```

---

# Environment Setup

## Development

```text
employee-dev-api
      |
Managed Identity
      |
employee-dev-kv
```

---

## UAT

```text
employee-uat-api
      |
Managed Identity
      |
employee-uat-kv
```

---

## Production

```text
employee-prod-api
      |
Managed Identity
      |
employee-prod-kv
```

Each environment has:

- Separate App Service
- Separate Managed Identity
- Separate Key Vault

---

# Benefits

## No Passwords

No credentials stored in code.

---

## Better Security

Azure manages authentication.

---

## Automatic Credential Rotation

Azure rotates credentials automatically.

---

## Easy Azure Integration

Works with:

- Key Vault
- Storage Account
- SQL Database
- Service Bus
- Event Hub

---

## Least Privilege Access

Grant only required permissions.

Example:

```text
Read Secrets Only
```

instead of full administrator access.

---

# Common Interview Questions

## What is Managed Identity?

Managed Identity is an Azure-managed identity that allows Azure resources to authenticate securely with other Azure services without storing credentials.

---

## What are the types of Managed Identity?

1. System Assigned Managed Identity
2. User Assigned Managed Identity

---

## Which is commonly used?

System Assigned Managed Identity.

---

## Does enabling Managed Identity automatically grant access?

No.

Permissions must be granted separately using IAM roles.

---

## What role is commonly assigned for Key Vault?

```text
Key Vault Secrets User
```

---

## What is DefaultAzureCredential?

An Azure SDK credential provider that automatically selects the appropriate authentication mechanism based on the environment.

---

# Interview Answer

Managed Identity is an Azure feature that provides an automatically managed identity for Azure resources such as App Services, Virtual Machines, and Functions. It allows applications to securely access Azure services like Key Vault, Storage Accounts, and SQL Database without storing client secrets or passwords. After enabling Managed Identity, permissions must be granted through IAM roles or resource-specific access controls. Applications typically use DefaultAzureCredential to authenticate using the Managed Identity at runtime.

---

# Quick Revision

```text
App Service
      |
      +--> Managed Identity
               |
               +--> Identity Created

Key Vault
      |
      +--> IAM
               |
               +--> Permission Granted

Application
      |
DefaultAzureCredential()
      |
Managed Identity
      |
Azure Key Vault
      |
Secret Returned
```

---

# Memory Trick

```text
Managed Identity
      =
Who Am I?

IAM Role
      =
What Am I Allowed To Do?
```

Example:

```text
Employee ID Card
      =
Managed Identity

Room Access Permission
      =
IAM Role
```

Identity proves who you are.

IAM determines what you can access.