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

## 5.9.1 – Clean up Amplify app and frontend hosting

1. Open the **Amplify** console in the same Region used for the workshop.
2. Select the **Amplify app** that hosts the English Journey frontend.
3. Choose **Actions → Delete app** (or **Delete** from the app details page).
4. Confirm the deletion.

Deleting the Amplify app will automatically remove the connected **frontend hosting**, and the **backend stacks** created by Amplify (Cognito, Lambda, DynamoDB, S3) – unless you chose to keep them during deletion.  
Check the confirmation dialog carefully and follow the prompts.

---

## 5.9.2 – Clean up backend resources that remain

Depending on how you created the backend, some resources may still exist after deleting the Amplify app.  
In the AWS console, verify the following services in the workshop Region:

- **Cognito**  
  - Remove any **User Pools** or **Identity Pools** created only for the workshop.

- **Lambda**  
  - Delete Lambda functions used exclusively for English Journey (for example: test-level function, daily reminders, vocabulary utilities).

- **DynamoDB**  
  - Delete DynamoDB tables that were created for workshop data (users progress, questions, vocabulary, etc.) if you no longer need them.

- **S3**  
  - Delete S3 buckets that were used only for this workshop (for example: lesson content, media assets, temporary exports).
  - Be careful not to delete shared or production buckets.

- **MediaConvert**  
  - Remove **job templates**, **queues** or **endpoints** that are dedicated to this workshop, if any.

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
2. Under **Log groups**, delete log groups that belong only to:
   - the English Journey Lambda functions,
   - MediaConvert jobs used for this workshop.
3. Under **Alarms**, delete:
   - alarms that monitor workshop-only resources (Lambda, DynamoDB, SES),  
   - any test alarms you created during the exercises.

### AWS WAF (if used)

If you deployed a dedicated **WAF Web ACL** for the English Journey frontend:

1. Open the **AWS WAF & Shield** console.
2. Identify the **Web ACL** associated with the workshop CloudFront distribution or Amplify app.
3. If the Web ACL is used exclusively for this workshop, delete it.

---

## 5.9.4 – Clean up IAM roles and policies

Finally, review **IAM** to ensure there are no unused roles or policies left behind:

1. In the **IAM** console, go to **Roles**:
   - Look for roles created only for this workshop (for example: custom Lambda execution roles, MediaConvert service roles, or roles with names that clearly reference English Journey or the workshop).
   - Before deleting a role, confirm that no Lambda function, service or user still depends on it.

2. In **Policies**:
   - Remove **customer-managed policies** that were created solely for the workshop, especially:
     - policies that grant `ses:SendEmail` / `ses:SendRawEmail` to Lambda,
     - policies used only by temporary roles.

> Do **not** delete shared or production IAM roles / policies that might be reused by other applications.

After these steps, the AWS environment should no longer contain resources that were created specifically for the English Journey workshop.


