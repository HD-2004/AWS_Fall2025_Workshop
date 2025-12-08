---
title : "Clean up"
date: 2025-09-10
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---

## Goal

This section explains how to **remove the AWS resources** that were created or used during the English Journey workshop, so that you do not incur unexpected costs.

You should only delete these resources when you have finished experimenting with the architecture.

---

## 5.9.1 – Delete the Amplify app and front-end hosting

1. Open the **Amplify** console in the same Region used for the workshop.
2. Select the **Amplify app** that is hosting the English Journey front end.
3. Choose **Actions → Delete app** (or the **Delete** button in the app details page).
4. Confirm the deletion as instructed.

When you delete the Amplify app:

- Amplify automatically removes the **front-end hosting**,
- And usually deletes the **backend stacks** it created (Cognito, Lambda, DynamoDB),  
  unless you explicitly choose to keep them during the deletion process.

Carefully read the confirmation dialog to avoid deleting important resources by mistake.

---

## 5.9.2 – Delete any remaining back-end resources

Depending on how you created the back end, there may still be some resources left after deleting the Amplify app.  
In the AWS console, in the workshop Region, check the following services:

- **Cognito**  
  - Delete any **User Pools** or **Identity Pools** that were created specifically for the workshop.

- **Lambda**  
  - Delete Lambda functions that only serve English Journey (for example: level test handlers, daily reminders, vocabulary processing).

- **DynamoDB**  
  - Delete DynamoDB tables that were used only for workshop data (learning progress, questions, vocabulary, …) if you no longer need them.


---

## 5.9.3 – Clean up SES, CloudWatch and WAF

In addition to the core backend, this workshop uses **Amazon SES**, **CloudWatch** and optionally **AWS WAF**.

### Amazon SES

1. Open the **Amazon SES** console.
2. In **Verified identities**:
   - Delete email identities that were created only for the workshop (for example: test sender or test recipient addresses).
3. In **Configuration sets**:
   - Delete the configuration set used by the English Journey application (for example: `english-journey-config`), if you will not reuse it.

If your account was moved **out of SES sandbox** only for the workshop, you may want to review your SES sending quotas and usage, but there is nothing extra to delete for that.

### CloudWatch

1. Open the **CloudWatch** console.
2. In **Log groups**, delete:
   - Log groups for Lambda functions that belong to English Journey.

### AWS WAF

If you deployed a dedicated **WAF Web ACL** for the English Journey frontend:

1. Open the **AWS WAF** console.
2. Identify the **Web ACL** associated with the workshop CloudFront distribution or Amplify app.
3. If the Web ACL is used exclusively for this workshop, delete it.

---

## 5.9.4 – Clean up IAM roles and policies

Finally, review **IAM** to ensure there are no unused roles or policies left behind:

1. In the **IAM** console, go to **Roles**:
   - Look for roles created only for this workshop (for example: custom Lambda execution roles, or roles with names that clearly reference English Journey or the workshop).
   - Before deleting a role, confirm that no Lambda function, service or user still depends on it.

2. In **Policies**:
   - Remove **customer-managed policies** that were created solely for the workshop, especially:
     - policies that grant `ses:SendEmail` / `ses:SendRawEmail` to Lambda,
     - policies used only by temporary roles.

> Do **not** delete shared or production IAM roles / policies that might be reused by other applications.

After these steps, the AWS environment should no longer contain resources that were created specifically for the English Journey workshop.


