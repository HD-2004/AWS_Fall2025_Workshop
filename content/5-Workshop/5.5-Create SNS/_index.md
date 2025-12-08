---
title : "Configure Amazon SNS for notifications"
date: 2025-09-10
weight : 5 
chapter : false
pre : " <b> 5.5. </b> "
---


## Goal

English Journey needs a lightweight mechanism to send **notifications** to both students and operators.  
For example:

- reminders about learning activities,
- alerts when something goes wrong in the backend,
- or new lesson announcements.

For this purpose we use **Amazon Simple Notification Service (SNS)**.

---

## 5.5.1 SNS in the architecture

SNS is used in two main ways:

1. **User-facing notifications** – Lambda functions publish messages when:
   - a new lesson or quiz is available,
   - the user completes a study milestone (for example, a certain number of days of streak),
   - daily check-in or level test results are ready.

   These notifications can later be consumed by email, SMS, or by another Lambda that stores them in DynamoDB and displays them in the web UI.

2. **System alerts** – CloudWatch alarms publish to SNS topics when:
   - a Lambda function repeatedly fails,
   - error rates exceed a threshold,
   - or other important metrics go out of range.

   The team subscribes email addresses to this topic to be notified quickly.

---

## 5.5
.2 Creating SNS topics

We created at least two SNS topics:

- `english-journey-user-notifications` – for **application notifications** related to students.
- `english-journey-system-alerts` – for **operational alerts** from CloudWatch.

For each topic we added one or more **email subscriptions** during development, so the team could easily observe how notifications are sent.

---

## 5.5
.3 Publishing notifications from Lambda

The backend Lambda functions use the AWS SDK to publish messages to SNS.  
Typical flow:

1. After finishing some business logic (for example processing a daily check-in), the Lambda builds a small JSON payload containing:
   - user ID,
   - notification type (NEW_LESSON, LEVEL_UP, REMINDER, …),
   - a localized title and message body.
2. The Lambda publishes this payload to the **user-notifications topic**.
3. Another consumer (or the same Lambda) can:
   - store the notification in DynamoDB,
   - or let SNS deliver it directly as an email/SMS in simple scenarios.

This pattern decouples **business logic** from **delivery channel**. Later we can add new subscribers (mobile push, other microservices, …) without changing the original Lambda.

---

## 5.5
.4 SNS for system alerts

For the **system-alerts topic** we mainly use email subscriptions:

- CloudWatch Alarms (defined in section 5.9) are configured to use this topic as their action.
- When an alarm enters the `ALARM` state (e.g. high error rate of a Lambda), SNS immediately sends an email to the operations team.

This provides a simple but effective monitoring loop for the workshop environment.
