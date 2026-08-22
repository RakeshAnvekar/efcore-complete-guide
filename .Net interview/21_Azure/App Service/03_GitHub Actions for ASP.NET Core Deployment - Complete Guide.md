# GitHub Actions for ASP.NET Core Deployment - Complete Guide

---

# Overview

GitHub Actions is GitHub's built-in CI/CD platform.

It helps automate:

- Build
- Test
- Publish
- Deploy

whenever code changes occur.

Instead of manually deploying applications, GitHub Actions performs everything automatically.

---

# What Problem Does GitHub Actions Solve?

Without GitHub Actions:

```text
Developer
    |
    +--> Build Project
    +--> Run Tests
    +--> Publish Project
    +--> Login Azure
    +--> Deploy Application
```

Every deployment requires manual effort.

---

# With GitHub Actions

```text
Developer
      |
      +--> Git Push
               |
               v
        GitHub Action
               |
               +--> Build
               +--> Test
               +--> Publish
               +--> Deploy
```

Everything is automated.

---

# Real World Example

Suppose we have:

```text
EmployeeAPI
```

Stored in GitHub:

```text
https://github.com/company/employee-api
```

Developer modifies code:

```csharp
public string GetEmployee()
{
    return "Employee Added";
}
```

Commits:

```bash
git add .
git commit -m "Added Employee API"
git push
```

GitHub automatically starts the deployment workflow.

---

# Workflow Trigger

A workflow is usually triggered when code is merged into the main branch.

Example:

```yaml
on:
  push:
    branches:
      - main
```

Meaning:

```text
Whenever code reaches main branch,
start the workflow.
```

---

# Complete Flow

```text
Developer
     |
Pull Request
     |
Code Review
     |
Merge to Main
     |
GitHub Action Triggered
     |
GitHub Creates Temporary VM
     |
Build
     |
Test
     |
Publish
     |
Deploy
     |
Delete VM
```

---

# What Happens Internally?

## Step 1 - Merge to Main

Developer merges:

```text
feature/add-employee
          |
          v
        main
```

This triggers the workflow.

---

# Step 2 - GitHub Creates a Runner

GitHub automatically creates a temporary machine.

Example:

```text
ubuntu-latest
```

Think of it as:

```text
Fresh Linux VM
```

created only for this workflow.

---

# Important

The VM does not already exist.

GitHub creates it automatically.

```text
Workflow Starts
       |
       v
Create Runner VM
```

---

# Step 3 - Checkout Source Code

Workflow Step:

```yaml
- uses: actions/checkout@v4
```

Purpose:

```text
Download Repository
to Runner VM
```

Flow:

```text
GitHub Repository
       |
       v
Runner VM
```

---

# Step 4 - Install .NET SDK

Workflow Step:

```yaml
- uses: actions/setup-dotnet@v4
```

Purpose:

```text
Install .NET SDK
```

Example:

```text
.NET 8 SDK
```

installed on the VM.

---

# Step 5 - Restore Dependencies

Workflow Step:

```yaml
dotnet restore
```

Purpose:

Download NuGet packages.

Examples:

```text
Entity Framework Core
Serilog
Azure SDK
Newtonsoft.Json
FluentValidation
```

Flow:

```text
Project
      |
      v
Restore NuGet Packages
```

---

# Step 6 - Build Application

Workflow Step:

```yaml
dotnet build
```

Purpose:

Compile source code.

Flow:

```text
.cs Files
      |
      v
.dll Files
```

Example:

```text
EmployeeAPI.cs
       |
       v
EmployeeAPI.dll
```

---

# Step 7 - Run Tests

Workflow Step:

```yaml
dotnet test
```

Purpose:

Run automated tests.

Examples:

```text
Unit Tests
Integration Tests
```

---

# If Tests Fail

```text
Build Success

Test Failure

Deployment Stops
```

Application is NOT deployed.

---

# If Tests Pass

```text
Build Success

Test Success

Continue Deployment
```

---

# Step 8 - Publish Application

Workflow Step:

```yaml
dotnet publish
```

Purpose:

Create deployment-ready artifacts.

Generated Output:

```text
publish/

EmployeeAPI.dll

appsettings.json

Dependencies
```

---

# Step 9 - Deploy to Azure App Service

Workflow Step:

```yaml
azure/webapps-deploy
```

Purpose:

Upload published files to Azure.

Flow:

```text
Publish Folder
      |
      v
Azure App Service
```

Example:

```text
employee-api.azurewebsites.net
```

---

# Step 10 - Delete Runner

After deployment completes:

```text
Runner VM Destroyed
```

GitHub removes the temporary machine.

---

# Important Concept

Every workflow execution receives:

```text
New VM
```

Nothing is reused.

Example:

```text
Run 1
  |
  +--> VM Created
  +--> VM Deleted

Run 2
  |
  +--> New VM Created
  +--> VM Deleted
```

This guarantees clean builds.

---

# Visual End-to-End Flow

```text
Developer
     |
Pull Request
     |
Merge to Main
     |
GitHub Action
     |
Create Runner VM
     |
Checkout Code
     |
Install .NET SDK
     |
Restore Packages
     |
Build
     |
Run Tests
     |
Publish
     |
Deploy to Azure
     |
Delete VM
```

---

# Sample Workflow File

Location:

```text
.github/workflows/deploy.yml
```

Example:

```yaml
name: Deploy Employee API

on:
  push:
    branches:
      - main

jobs:

  build-and-deploy:

    runs-on: ubuntu-latest

    steps:

    - name: Checkout Code
      uses: actions/checkout@v4

    - name: Setup .NET
      uses: actions/setup-dotnet@v4
      with:
        dotnet-version: 8.0.x

    - name: Restore Packages
      run: dotnet restore

    - name: Build
      run: dotnet build --configuration Release

    - name: Test
      run: dotnet test

    - name: Publish
      run: dotnet publish -c Release -o publish

    - name: Deploy
      uses: azure/webapps-deploy@v3
```

---

# How Does GitHub Login to Azure?

GitHub needs permission to deploy.

Common approaches:

## Publish Profile

```text
Azure App Service
      |
      +--> Download Publish Profile
```

Store inside:

```text
GitHub Repository

Settings
     |
     +--> Secrets
```

Example:

```text
AZURE_WEBAPP_PUBLISH_PROFILE
```

---

# Enterprise Deployment Flow

```text
Developer
      |
Feature Branch
      |
Pull Request
      |
Code Review
      |
Merge Main
      |
GitHub Action
      |
Build
      |
Run Tests
      |
Security Scan
      |
Deploy Staging
      |
Approval
      |
Deploy Production
```

---

# Advantages of GitHub Actions

## Automation

No manual deployment.

---

## Consistent Builds

Every deployment uses a fresh VM.

---

## Faster Releases

Deploy automatically after merge.

---

## Better Quality

Tests run before deployment.

---

## Secure

Secrets stored securely in GitHub Secrets.

---

# Interview Questions

## What is GitHub Actions?

GitHub Actions is GitHub's built-in CI/CD platform used to automate build, test, and deployment workflows.

---

## What triggers a workflow?

Examples:

```text
Push

Pull Request

Manual Trigger

Schedule
```

---

## What is a Runner?

A runner is a temporary machine that executes workflow steps.

---

## What happens when code is merged to main?

GitHub creates a temporary runner, downloads the code, installs dependencies, restores packages, builds the application, runs tests, publishes artifacts, deploys to Azure App Service, and then destroys the runner.

---

## Where are workflow files stored?

```text
.github/workflows
```

---

# Key Interview Answer

When code is merged into the main branch, GitHub Actions automatically triggers a workflow. GitHub creates a temporary runner (VM), checks out the source code, installs required dependencies such as the .NET SDK, restores NuGet packages, builds the application, runs tests, publishes deployment artifacts, and deploys them to Azure App Service. Once the workflow completes, the temporary runner is deleted, ensuring a clean and consistent build environment for every deployment.