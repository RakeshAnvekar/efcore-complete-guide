# Azure App Service Scaling - Complete Guide

---

# Overview

Scaling is the process of increasing or decreasing application resources based on traffic and workload.

As the number of users increases, the application may require:

- More CPU
- More Memory (RAM)
- More Application Instances

Azure App Service provides built-in scaling capabilities to handle increased demand.

---

# Why Do We Need Scaling?

Suppose we have:

```text
Employee API
```

Initially:

```text
100 Users
```

Application performs well.

Later:

```text
10,000 Users
```

Application becomes slow.

Possible symptoms:

- Slow response times
- Request timeouts
- High CPU utilization
- High memory consumption

Solution:

```text
Scaling
```

---

# Types of Scaling

Azure App Service supports:

1. Vertical Scaling (Scale Up)
2. Horizontal Scaling (Scale Out)

---

# 1. Vertical Scaling (Scale Up)

Scale Up means increasing the power of the existing machine.

Example:

Before:

```text
B1 Plan

1 CPU
1.75 GB RAM
```

After:

```text
P1V3 Plan

2 CPU
8 GB RAM
```

Architecture:

```text
Before

Employee API
      |
      v
1 CPU
1.75 GB RAM
```

```text
After

Employee API
      |
      v
2 CPU
8 GB RAM
```

The application remains on a single instance but gets more resources.

---

# Azure Portal Location

```text
App Service Plan
      |
      +--> Scale Up
```

Choose a higher pricing tier.

Example:

```text
B1
 |
S1
 |
P1V3
 |
P2V3
```

---

# Scale Up Benefits

✔ Simple

✔ Fast

✔ No Code Changes

✔ Immediate Performance Improvement

---

# Scale Up Limitations

Eventually machine size reaches its maximum.

```text
Cannot Scale Forever
```

There is a hardware limit.

---

# Example

Suppose CPU usage is:

```text
95%
```

Upgrade:

```text
2 CPU
   |
4 CPU
```

Application gets more processing power.

---

# 2. Horizontal Scaling (Scale Out)

Instead of increasing machine size:

```text
Create Multiple Copies
Of The Application
```

Example:

Before:

```text
1 Instance
```

After:

```text
5 Instances
```

---

# Scale Out Architecture

Before:

```text
Users
   |
Employee API
   |
1 Instance
```

After:

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

Azure distributes requests automatically.

---

# Load Balancer Role

Azure Load Balancer distributes incoming traffic.

Example:

```text
Request 1 --> Instance 1

Request 2 --> Instance 2

Request 3 --> Instance 3

Request 4 --> Instance 4

Request 5 --> Instance 5
```

Traffic is shared across instances.

---

# Azure Portal Location

```text
App Service Plan
      |
      +--> Scale Out
```

Set:

```text
Instance Count = 5
```

Azure automatically creates:

```text
5 Running Copies
Of The Application
```

---

# Scale Up vs Scale Out

| Scale Up | Scale Out |
|-----------|-----------|
| Bigger Machine | More Machines |
| Increase CPU/RAM | Increase Instances |
| Vertical Scaling | Horizontal Scaling |
| Hardware Limited | Highly Scalable |
| Simpler | More Powerful |

---

# Important Interview Question

## Where Does Scaling Happen?

Many developers answer incorrectly.

Scaling happens at:

```text
App Service Plan
```

NOT:

```text
App Service
```

Reason:

```text
App Service Plan
      |
      +--> CPU
      +--> RAM
      +--> Instances
```

The App Service Plan owns the infrastructure.

---

# Auto Scaling

Azure can automatically scale based on rules.

Example:

```text
CPU > 70%
```

Action:

```text
Add 1 Instance
```

---

# Scale Out Rule

```text
CPU > 70%
      |
Add Instance
```

Result:

```text
2 Instances
      |
3 Instances
      |
4 Instances
```

---

# Scale In Rule

When traffic decreases:

```text
CPU < 30%
      |
Remove Instance
```

Azure reduces costs automatically.

---

# Auto Scaling Example

Morning:

```text
2 Instances
```

Lunch Time:

```text
8 Instances
```

Night:

```text
2 Instances
```

Azure adjusts automatically.

---

# Real Enterprise Autoscaling Rule

```text
Minimum Instances = 2

Maximum Instances = 10

Scale Out:
CPU > 70%

Scale In:
CPU < 30%
```

---

# Session State Problem

Suppose login information is stored in memory.

Instance 1:

```text
User Logged In
```

Next request:

```text
Routed To Instance 2
```

Problem:

```text
Instance 2
Does Not Know
About Login State
```

User appears logged out.

---

# Enterprise Solution

Use:

```text
Azure Redis Cache
```

Store session information centrally.

Architecture:

```text
Instance 1
Instance 2
Instance 3
      |
      v
Azure Redis Cache
```

All instances share session data.

---

# Stateless APIs

Modern APIs should be:

```text
Stateless
```

Meaning:

```text
No User Data
Stored In Memory
```

Benefits:

✔ Easy Scale Out

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
   |
Azure Redis Cache
   |
Azure SQL Database
```

---

# Benefits of Scaling

## High Availability

Multiple instances improve reliability.

---

## Better Performance

More resources available.

---

## Cost Optimization

Scale down during low traffic.

---

## Improved User Experience

Faster response times.

---

# Common Interview Questions

## What is Scaling?

Scaling is the process of increasing or decreasing resources to handle changing workloads.

---

## What is Scale Up?

Scale Up (Vertical Scaling) increases CPU, memory, and machine size.

Example:

```text
2 CPU → 4 CPU
```

---

## What is Scale Out?

Scale Out (Horizontal Scaling) increases the number of application instances.

Example:

```text
1 Instance → 5 Instances
```

---

## Which Scaling Method Is Preferred?

Enterprise applications generally prefer:

```text
Scale Out
```

because it supports larger workloads and higher availability.

---

## What is Auto Scaling?

Auto Scaling automatically increases or decreases application instances based on predefined rules.

---

## Where is Scaling Configured?

```text
App Service Plan
```

not App Service.

---

## Why Should APIs Be Stateless?

Stateless APIs can easily scale out because requests can be processed by any application instance.

---

# Interview Answer

Azure App Service supports both Vertical Scaling (Scale Up) and Horizontal Scaling (Scale Out). Scale Up increases CPU and memory by moving to a larger App Service Plan tier, while Scale Out increases the number of application instances behind an Azure load balancer. Scaling is configured at the App Service Plan level, and Azure supports automatic scaling based on metrics such as CPU utilization and memory usage. For scale-out scenarios, applications should be stateless, and shared state should be stored in services such as Azure Redis Cache.

---

# Quick Revision

```text
Scale Up
      =
Bigger Machine

Scale Out
      =
More Machines

Auto Scaling
      =
Azure Adds/Removes Instances

App Service Plan
      =
Where Scaling Happens

Redis Cache
      =
Shared Session Storage

Stateless API
      =
Best For Scale Out
```

---

# Memory Trick

```text
App Service
      =
Application

App Service Plan
      =
Machine

Scale Up
      =
Upgrade Machine

Scale Out
      =
Add More Machines
```