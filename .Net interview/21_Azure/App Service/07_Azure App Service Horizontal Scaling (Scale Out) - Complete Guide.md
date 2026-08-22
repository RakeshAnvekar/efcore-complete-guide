# Azure App Service Horizontal Scaling (Scale Out) - Complete Guide

---

# Overview

Horizontal Scaling (Scale Out) means increasing the number of application instances instead of increasing machine size.

Example:

Before:

```text
Employee API
      |
      v
1 Instance
```

After:

```text
Employee API
      |
      +--> Instance 1
      +--> Instance 2
      +--> Instance 3
      +--> Instance 4
      +--> Instance 5
```

Azure automatically distributes traffic across all instances.

---

# Why Do We Need Horizontal Scaling?

Suppose your API normally handles:

```text
100 Users
```

Everything works fine.

Suddenly:

```text
10,000 Users
```

start using the application.

Single instance becomes overloaded:

```text
High CPU

High Memory

Slow Response Time

Request Timeout
```

Solution:

```text
Scale Out
```

Add more instances.

---

# Scale Up vs Scale Out

## Scale Up (Vertical Scaling)

Increase machine power.

```text
1 CPU
  |
  v
4 CPU
```

---

## Scale Out (Horizontal Scaling)

Increase instance count.

```text
1 Instance
    |
    v
5 Instances
```

---

# Azure Architecture

```text
Users
   |
Azure Load Balancer
   |
   +--> Instance 1
   +--> Instance 2
   +--> Instance 3
   +--> Instance 4
   +--> Instance 5
```

Azure automatically distributes requests.

---

# Where Is Scale Out Configured?

Very Important Interview Question.

Scaling is configured on:

```text
App Service Plan
```

NOT:

```text
App Service
```

Because App Service Plan owns:

```text
CPU
RAM
Instances
```

---

# Azure Portal Navigation

```text
Azure Portal
      |
      +--> App Service
                |
                +--> Scale Out (App Service Plan)
```

or

```text
App Service Plan
      |
      +--> Scale Out
```

---

# Manual Scaling

Example:

```text
Instance Count = 5
```

Azure immediately creates:

```text
5 Running Copies
Of The Application
```

---

# Auto Scaling

Instead of manually changing instances:

```text
1
2
3
5
10
```

Azure can do it automatically.

This is called:

```text
Autoscale
```

---

# Enable Autoscale

Navigate to:

```text
App Service Plan
      |
      +--> Scale Out
      |
      +--> Custom Autoscale
```

Choose:

```text
Custom Autoscale
```

instead of:

```text
Manual
```

---

# Instance Limits

Before creating rules, define limits.

Example:

```text
Minimum Instances = 2

Default Instances = 2

Maximum Instances = 10
```

Meaning:

```text
Never go below 2

Start with 2

Never exceed 10
```

---

# Scale Out Rule

Click:

```text
Add Rule
```

Configure:

```text
Metric Source:
App Service Plan
```

```text
Metric:
CPU Percentage
```

```text
Operator:
Greater Than
```

```text
Threshold:
70
```

```text
Duration:
10 Minutes
```

```text
Action:
Increase Count By 1
```

---

# Rule Meaning

```text
IF

CPU > 70%

FOR 10 MINUTES

THEN

ADD 1 INSTANCE
```

---

# Scale In Rule

Create another rule.

Configuration:

```text
Metric:
CPU Percentage
```

```text
Operator:
Less Than
```

```text
Threshold:
30
```

```text
Duration:
10 Minutes
```

```text
Action:
Decrease Count By 1
```

---

# Rule Meaning

```text
IF

CPU < 30%

FOR 10 MINUTES

THEN

REMOVE 1 INSTANCE
```

---

# Complete Autoscale Configuration

```text
Minimum Instances = 2

Default Instances = 2

Maximum Instances = 10

---------------------------------

CPU > 70%
FOR 10 MINUTES

ADD 1 INSTANCE

---------------------------------

CPU < 30%
FOR 10 MINUTES

REMOVE 1 INSTANCE
```

---

# Real Enterprise Example

Production API:

```text
Employee API
```

Configuration:

```text
Minimum Instances = 3

Default Instances = 3

Maximum Instances = 20
```

Rules:

```text
CPU > 75%
FOR 10 MINUTES

ADD 2 INSTANCES
```

and

```text
CPU < 40%
FOR 15 MINUTES

REMOVE 1 INSTANCE
```

---

# Other Autoscale Metrics

Azure supports scaling based on many metrics.

---

## CPU Percentage

Most common.

```text
CPU > 70%
```

Scale Out.

---

## Memory Usage

```text
Memory > 80%
```

Scale Out.

---

## HTTP Queue Length

```text
Queue Length > 100
```

Scale Out.

---

## Requests Per Second

```text
Requests > 500/sec
```

Scale Out.

---

## Response Time

```text
Response Time > 2 Seconds
```

Scale Out.

---

# Schedule-Based Scaling

Useful when traffic is predictable.

Example:

```text
Monday-Friday

8 AM - 6 PM
```

Keep:

```text
5 Instances
```

At night:

```text
2 Instances
```

Azure automatically adjusts.

---

# Load Balancer Behavior

Azure automatically routes requests.

Example:

```text
Request 1 --> Instance 1

Request 2 --> Instance 2

Request 3 --> Instance 3

Request 4 --> Instance 1

Request 5 --> Instance 2
```

No manual configuration required.

---

# Session State Problem

Suppose user login information is stored in memory.

Instance 1:

```text
User Logged In
```

Next request:

```text
Routed To Instance 2
```

Instance 2 does not know the user.

Problem:

```text
Session Lost
```

---

# Enterprise Solution

Store session data in:

```text
Azure Redis Cache
```

Architecture:

```text
Instance 1
Instance 2
Instance 3
      |
      v
Azure Redis Cache
```

All instances share session information.

---

# Stateless APIs

Modern APIs should be:

```text
Stateless
```

Meaning:

```text
No User Data Stored
In Application Memory
```

Benefits:

✔ Easy Scale Out

✔ Better Reliability

✔ Better Performance

✔ High Availability

---

# Real Enterprise Architecture

```text
Users
   |
Azure Front Door
   |
Azure Load Balancer
   |
App Service Plan
   |
   +--> Employee API Instance 1
   +--> Employee API Instance 2
   +--> Employee API Instance 3
   +--> Employee API Instance 4
   |
Azure Redis Cache
   |
Azure SQL Database
```

---

# Benefits Of Horizontal Scaling

## High Availability

If one instance fails:

```text
Other Instances Continue
```

---

## Better Performance

Traffic distributed across instances.

---

## Fault Tolerance

Application remains available during failures.

---

## Cost Optimization

Scale down when traffic decreases.

---

# Common Interview Questions

## What Is Horizontal Scaling?

Horizontal Scaling means increasing the number of application instances to handle more traffic.

---

## Where Is Scale Out Configured?

```text
App Service Plan
```

---

## What Is Autoscale?

Autoscale automatically increases or decreases application instances based on predefined rules.

---

## What Metrics Can Trigger Scaling?

Examples:

- CPU
- Memory
- Queue Length
- Request Count
- Response Time

---

## Why Should APIs Be Stateless?

Because requests can be served by any instance without depending on server memory.

---

# Interview Answer

Horizontal Scaling (Scale Out) in Azure App Service increases the number of application instances running behind an Azure load balancer. Scaling is configured at the App Service Plan level and can be manual or automatic using Autoscale rules. Common autoscale metrics include CPU utilization, memory usage, request count, and queue length. Enterprise applications are typically designed as stateless services so they can scale horizontally without session-related issues.

---

# Quick Revision

```text
Scale Out
      =
More Instances

Autoscale
      =
Automatic Scaling

CPU High
      =
Add Instance

CPU Low
      =
Remove Instance

Configured On
      =
App Service Plan

Best Practice
      =
Stateless APIs
```

---

# Memory Trick

```text
Scale Up
      =
Bigger Server

Scale Out
      =
More Servers

App Service Plan
      =
Controls Scaling

Load Balancer
      =
Distributes Requests
```