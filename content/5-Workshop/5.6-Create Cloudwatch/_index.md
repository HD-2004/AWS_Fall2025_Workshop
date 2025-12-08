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
- triggering alarms that send notifications through SNS.

---

## 5.6.1 CloudWatch Logs for Lambda and API

By default, every Lambda function created by Amplify writes logs to **CloudWatch Logs**.  
In the context of this project we use these logs to:

- trace requests for:
  - placement tests,
  - quiz submissions,
  - vocabulary and dictionary calls,
- debug issues such as invalid input, permission errors, or timeouts,
- record important events (for example when MediaConvert jobs are created).

In addition to the default logging, some functions write structured JSON logs, which makes it easier to search by `userId`, `requestId` or `feature`.

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

## 5.6.3 CloudWatch Dashboards (optional)

To have a quick overview of the system we created a small **CloudWatch Dashboard** that shows:

- a graph of Lambda error rate over time,
- duration of the LevelTest and Dictionary functions,
- and, optionally, blocked requests reported by AWS WAF.

This is not strictly required for the workshop, but it helps visualize the impact when students actively use the application.

---

## 5.6.4 CloudWatch Alarms integrated with SNS

The most important part of the monitoring setup is **CloudWatch Alarms**.

Examples of alarms configured in this project:

1. **Lambda error alarm**

   - Monitors the `Errors` metric of critical functions (LevelTest, MyLearning, Dictionary).
   - Triggers when the error count is above a small threshold for several consecutive periods.
   - Action: publish to the `english-journey-system-alerts` SNS topic.

2. **DynamoDB throttling alarm**

   - Monitors `ThrottledRequests` for the main tables.
   - Indicates that provisioned capacity or on-demand limits are being exceeded.
   - Also sends notifications via SNS.

3. **MediaConvert failure alarm** (optional)

   - Looks at error metrics or dead-letter queues related to MediaConvert job processing.

Thanks to these alarms, any serious issue (for example a broken Lambda deployment) results in an email notification via SNS, allowing the team to investigate quickly.

---

## Summary

CloudWatch completes the observability story for English Journey by providing:

- **logs** for troubleshooting,
- **metrics and dashboards** for understanding behavior over time,
- **alarms** that connect directly to SNS topics for proactive alerts.

Together with Amplify, SNS and WAF, this gives a basic but robust operational setup for the workshop project.
