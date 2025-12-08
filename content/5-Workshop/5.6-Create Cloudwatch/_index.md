---
title : "Configure Amazon CloudWatch"
date: 2025-09-10
weight : 6 
chapter : false
pre : " <b> 5.6. </b> "
---


## Goal

To understand how the application behaves in production and to be able to react quickly to errors, we use **Amazon CloudWatch** for:

- collecting logs from Lambda functions,
- monitoring metrics (invocations, errors, throttles, latency),

---

## 5.6.1 CloudWatch Logs for Lambda and API

By default, every Lambda function created by Amplify writes logs to **CloudWatch Logs**.  
In this project, logs are used to:

- Track requests for:
  - Level test submissions,
  - Quiz submissions,
  - Dictionary / vocabulary lookups,
- Debug input errors, permission (IAM) errors or timeouts,
- Record important application events.

In addition to the default logs, some functions write structured JSON logs, which makes it easier to search by `userId`, `requestId` or `feature`.

---

## 5.6.2 Metrics for key services

CloudWatch automatically provides metrics for:

- **Lambda** – invocations, errors, duration, concurrent executions,
- **DynamoDB** – read/write capacity, throttled requests,
- **S3 / CloudFront** – data transfer and request counts,
- **WAF** – number of allowed/blocked requests.

For the workshop we focus on a few **key metrics**:

- Lambda **Error count** and **Error rate** for the main backend functions,
- Lambda **Duration** to detect performance issues in level test & quiz processing,
- DynamoDB **ThrottledRequests** to see if the provisioned capacity is sufficient.

---

## 5.6.3 CloudWatch Dashboard (optional)

To quickly observe the system health, the team creates a small **CloudWatch Dashboard** showing:

- A chart of Lambda error rate over time,
- Execution duration of the LevelTest and Dictionary functions,
- (Optional) Number of requests blocked by AWS WAF.

The dashboard is not mandatory for the workshop, but it helps illustrate the system behavior when many students are using the application.

---


## Summary

CloudWatch completes the monitoring story for English Journey by providing:

- **Logs** for analysis and debugging,
- **Metrics & dashboards** to observe trends,

Combined with Amplify, SES and WAF, this gives a reasonably robust operational setup for this workshop project.
