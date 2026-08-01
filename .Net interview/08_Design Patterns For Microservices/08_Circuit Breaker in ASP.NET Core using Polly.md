# Module 9.3 – Circuit Breaker in ASP.NET Core using Polly

> **Course:** ASP.NET Core Microservices with .NET

**Module:** Circuit Breaker using Polly

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Learning Objectives
2. What is Polly?
3. Why Do We Need Polly?
4. Why IHttpClientFactory?
5. Problems with new HttpClient()
6. Registering HttpClient
7. Dependency Injection
8. Making HTTP Calls
9. Retry Policy
10. Circuit Breaker Policy
11. Timeout Policy
12. Fallback Policy
13. Combining Policies
14. Enterprise Architecture
15. Best Practices
16. Interview Questions
17. Summary
18. Key Takeaways

---

# Learning Objectives

By the end of this module, you will understand

- What Polly is
- Why Polly is used in ASP.NET Core
- IHttpClientFactory
- Retry Policy
- Circuit Breaker Policy
- Timeout Policy
- Fallback Policy
- Enterprise HTTP Resiliency
- Best Practices

---

# What is Polly?

**Polly** is a .NET resilience library.

It helps applications handle temporary and long-running failures gracefully.

Instead of immediately failing when a service is unavailable, Polly allows us to apply resilience strategies such as

- Retry
- Circuit Breaker
- Timeout
- Fallback
- Bulkhead Isolation
- Rate Limiting (through newer .NET resilience integrations)

Think of Polly as a protection layer around your HTTP calls.

---

# Why Do We Need Polly?

Suppose Order Service calls Payment Service.

```
Customer

↓

Order Service

↓

Payment Service
```

Everything works until Payment Service becomes unavailable.

Without Polly

```
Order Service

↓

Call Payment Service

↓

Timeout

↓

Exception

↓

Customer Waits
```

Every request experiences the same problem.

Polly helps protect the application from these failures.

---

# Architecture

Without Polly

```
Order Service

↓

Payment Service
```

With Polly

```
Order Service

↓

Polly

↓

Payment Service
```

Every outgoing HTTP request passes through Polly.

---

# Why IHttpClientFactory?

Many beginners create HttpClient like this

```csharp
var client = new HttpClient();
```

This works,

but it is NOT recommended for enterprise applications.

---

# Problems with new HttpClient()

Suppose your application receives

```
10,000 Requests
```

Every request creates

```
new HttpClient()
```

Soon

```
Thousands of TCP Connections
```

Eventually

```
Socket Exhaustion
```

The application becomes slow.

Other issues include

- Poor DNS refresh
- Difficult configuration
- Harder testing
- No centralized management

---

# Solution

ASP.NET Core provides

```
IHttpClientFactory
```

Benefits

- Connection Pooling
- DNS Refresh
- Dependency Injection
- Logging
- Polly Integration
- Better Performance

---

# Register HttpClient

Inside

```
Program.cs
```

```csharp
builder.Services.AddHttpClient();
```

ASP.NET Core now manages HttpClient instances.

---

# Dependency Injection

Instead of creating HttpClient manually,

inject it.

```csharp
public class PaymentService
{
    private readonly HttpClient _client;

    public PaymentService(HttpClient client)
    {
        _client = client;
    }
}
```

Advantages

- Better Performance
- Easier Unit Testing
- Managed by Dependency Injection

---

# Making HTTP Calls

```csharp
var response =
await _client.GetAsync(
"https://payment/api/pay");
```

This performs a normal HTTP request.

Without Polly,

there is no resilience.

---

# Installing Polly

Install packages

```bash
dotnet add package Polly

dotnet add package Microsoft.Extensions.Http.Polly
```

These packages integrate Polly with IHttpClientFactory.

---

# Retry Policy

Retry handles temporary failures.

Example

```
Call Service

↓

Failure

↓

Retry

↓

Success
```

Configuration

```csharp
builder.Services
.AddHttpClient<PaymentService>()
.AddTransientHttpErrorPolicy(policy =>
    policy.RetryAsync(3));
```

Meaning

```
Maximum Retry Count

↓

3
```

---

# When Retry Works

Retry is useful for

- Temporary Network Failure
- DNS Delay
- Small Packet Loss
- Temporary Database Connection Issues

Retry assumes

```
Failure is temporary.
```

---

# When Retry Does NOT Work

Suppose Payment Service is completely down.

Retry

```
Attempt 1

↓

Failed

↓

Attempt 2

↓

Failed

↓

Attempt 3

↓

Failed
```

Retry cannot recover a permanently unavailable service.

This is where Circuit Breaker helps.

---

# Circuit Breaker Policy

Configuration

```csharp
builder.Services
.AddHttpClient<PaymentService>()
.AddTransientHttpErrorPolicy(policy =>
    policy.CircuitBreakerAsync(
        handledEventsAllowedBeforeBreaking:5,
        durationOfBreak:TimeSpan.FromSeconds(30)));
```

---

# handledEventsAllowedBeforeBreaking

```
5
```

Meaning

```
5 Consecutive Failures

↓

Open Circuit
```

---

# durationOfBreak

```
30 Seconds
```

Meaning

```
Open Circuit

↓

Wait 30 Seconds

↓

Half Open
```

---

# Request Flow

Initially

```
Request

↓

Polly

↓

Payment Service

↓

Success
```

Everything works.

---

Failures begin

```
Failure

↓

Failure

↓

Failure

↓

Failure

↓

Failure
```

Circuit opens.

---

New Requests

```
Customer

↓

Order Service

↓

Polly

↓

Circuit Open

↓

Reject Request
```

No HTTP request reaches Payment Service.

---

# Timeout Policy

Suppose Payment Service hangs.

Without Timeout

```
Waiting...

Waiting...

Waiting...
```

Eventually

```
Timeout
```

This wastes resources.

Configuration

```csharp
policy.TimeoutAsync(
TimeSpan.FromSeconds(5));
```

Meaning

```
Wait Maximum

↓

5 Seconds
```

If no response,

cancel the request.

---

# Fallback Policy

Suppose

Retry failed.

Circuit is Open.

Instead of

```
500 Internal Server Error
```

Return

```
Payment Service

Temporarily Unavailable
```

Example

```csharp
policy.FallbackAsync(
new HttpResponseMessage(
HttpStatusCode.ServiceUnavailable));
```

Fallback provides a graceful response.

---

# Combining Policies

Enterprise applications combine multiple resilience policies.

```
Incoming Request

↓

Timeout

↓

Retry

↓

Circuit Breaker

↓

Fallback

↓

Payment Service
```

Each policy has a different responsibility.

---

# Example

Customer places an order.

```
Order Service

↓

Retry

↓

Retry

↓

Retry

↓

Still Failed

↓

Circuit Opens

↓

Fallback Response
```

The application remains stable.

---

# Enterprise Architecture

```
                Order Service

                       │

             IHttpClientFactory

                       │

                    Polly

      ┌──────────┬──────────┬──────────┬──────────┐

      │          │          │          │          │

   Timeout     Retry    Circuit    Fallback

                         Breaker

      └──────────┴──────────┴──────────┴──────────┘

                       │

                Payment Service

                       │

                   Bank Service
```

---

# Best Practices

✔ Always use IHttpClientFactory

✔ Never create new HttpClient for every request

✔ Combine Retry with Circuit Breaker

✔ Configure Timeout

✔ Return meaningful fallback responses

✔ Log Circuit Breaker state changes

✔ Monitor failures using Application Insights, Prometheus or similar monitoring tools

✔ Keep retry counts and timeout values configurable

---

# Interview Questions

## What is Polly?

Polly is a .NET resilience library that provides policies such as Retry, Circuit Breaker, Timeout, and Fallback for handling transient and persistent failures.

---

## Why use IHttpClientFactory?

To efficiently manage HttpClient instances, avoid socket exhaustion, improve DNS handling, and integrate resilience policies.

---

## Why not use new HttpClient()?

Creating a new HttpClient for every request can lead to socket exhaustion, poor resource utilization, and difficult-to-maintain code.

---

## What is Retry Policy?

Retry automatically retries failed requests when failures are expected to be temporary.

---

## What is Circuit Breaker Policy?

Circuit Breaker stops sending requests to an unhealthy service after a configured number of failures.

---

## What is Timeout Policy?

Timeout cancels requests that exceed a configured execution time.

---

## What is Fallback Policy?

Fallback returns an alternative response when all other resilience strategies fail.

---

## Why combine Retry and Circuit Breaker?

Retry handles temporary failures.

Circuit Breaker handles persistent failures.

Together they provide better resiliency.

---

# Summary

Polly is the standard resilience library for ASP.NET Core applications.

It integrates with IHttpClientFactory and provides powerful policies such as Retry, Circuit Breaker, Timeout, and Fallback.

These policies help applications remain responsive even when dependent services experience temporary or long-running failures.

Most enterprise microservices combine these policies to build highly reliable distributed systems.

---

# Key Takeaways

✔ Polly adds resilience to HTTP communication.

✔ IHttpClientFactory should always be used instead of creating HttpClient manually.

✔ Retry handles transient failures.

✔ Circuit Breaker handles persistent failures.

✔ Timeout prevents long-running requests.

✔ Fallback returns graceful responses.

✔ Enterprise applications combine multiple Polly policies for maximum reliability.

---

# Next Module

## Module 9.4 – Complete Circuit Breaker Implementation in ASP.NET Core

In the next module, you will build a complete enterprise application demonstrating

- Order Service
- Payment Service
- Bank Service
- Polly Configuration
- Retry
- Circuit Breaker
- Timeout
- Fallback
- Live failure simulation
- State transitions (Closed → Open → Half-Open → Closed)
- Production-ready code with detailed explanations