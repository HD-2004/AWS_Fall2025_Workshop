---
title : "Clean up"
date: 2025-09-10
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---
## Goal

This section explains how to **clean up AWS resources** related to the English Journey workshop  
after testing or demonstration is complete.

Although in this report we mainly describe the architecture and implementation,  
anyone who reproduces the deployment in a real AWS account should remove unused resources  
to avoid unnecessary cost and keep the environment tidy.

> ⚠️ Important: only delete resources that are dedicated to this project.  
> Do not remove shared buckets, roles or CloudWatch alarms that are used by other applications.

---

## 5.8.1 Overall approach

At a high level, the cleanup process follows these steps:

1. Remove the **Amplify app and its hosting stack** (CloudFront + S3).
2. Delete application data in **DynamoDB** and additional **S3 buckets** used for content and media.
3. Remove media processing components (**MediaConvert** queues/templates).
4. Delete **SNS topics** and **CloudWatch** dashboards/alarms created for the workshop.
5. Delete **WAF** web ACLs and **IAM roles / inline policies** that are only used by English Journey.

The exact order is not strict, but it is safer to start from the “top-level” service (Amplify) and then clean underlying resources.

---

## 5.8.2 Step 1 – Delete Amplify app and hosting

**Resources involved**

- AWS Amplify App for English Journey  
- Amplify hosting S3 bucket  
- CloudFront distribution serving the frontend

**Actions**

1. Open the **AWS Amplify console**.
2. Locate the Amplify App corresponding to English Journey.
3. Choose **Delete app** and confirm.
4. This automatically removes:
   - the Amplify build & deploy pipeline,
   - the **S3 hosting bucket** containing the built React application,
   - the **CloudFront distribution** serving the website.

> If the infrastructure was created using the Amplify CLI (`amplify init`, `amplify push`),  
> an alternative option is to run `amplify delete` from the project root,  
> which tears down the Amplify-managed backend stack.

---

## 5.8.3 Step 2 – Delete DynamoDB tables and S3 content

### 5.8.3.1 DynamoDB

**Resources involved**

- `EnglishJourney-Users`  
- `EnglishJourney-PlacementTestResults`  
- `EnglishJourney-Vocabulary`  
- `EnglishJourney-UserProgress`  
  (and any other tables created specifically for English Journey)

**Actions**

1. Open the **Amazon DynamoDB console**.
2. Filter or search for tables whose names start with `EnglishJourney-` (or the chosen project prefix).
3. Optionally **export** data you want to keep for documentation or backup.
4. For each table:
   - review that it is only used by this workshop,
   - choose **Delete table** and confirm.

### 5.8.3.2 S3 media and content buckets

**Resources involved**

- `english-journey-media-source` – raw media uploads (audio/video, etc.)  
- `english-journey-media-output` – MediaConvert outputs  
- Any extra S3 buckets used only for English Journey reading materials or static content

**Actions**

1. Open the **Amazon S3 console**.
2. For each bucket related to English Journey:
   - first **empty the bucket** (delete all objects and folders),
   - then delete the bucket itself.
3. Typical buckets to remove:
   - `english-journey-media-source`
   - `english-journey-media-output`
   - additional English Journey–specific content buckets, if they exist.

> The S3 bucket created for Amplify hosting should already have been deleted together with the Amplify app in step 5.8.2.

---

## 5.8.4 Step 3 – Remove MediaConvert queues and job templates

**Resources involved**

- MediaConvert **queues** used for English Journey transcoding jobs  
- MediaConvert **job templates** pointing to English Journey S3 buckets

**Actions**

1. Open the **AWS Elemental MediaConvert** console.
2. Navigate to the **Queues** section:
   - identify queues used only for English Journey lesson or media processing,
   - delete these queues.
3. Navigate to the **Job templates** section:
   - find templates that reference `english-journey-media-source` and `english-journey-media-output`,
   - delete those templates.

MediaConvert itself incurs little or no cost when idle, but removing unused queues and templates keeps the account configuration clean and easier to maintain.

---

## 5.8.5 Step 4 – Cleanup SNS topics and CloudWatch logs/alarms

### 5.8.5.1 SNS topics

**Resources involved**

- `english-journey-user-notifications` – application notifications for students  
- `english-journey-system-alerts` – operational alerts from CloudWatch

**Actions**

1. Open the **Amazon SNS console**.
2. Under **Topics**, locate:
   - `english-journey-user-notifications`,
   - `english-journey-system-alerts`.
3. For each topic:
   - review existing **subscriptions** (email addresses, SMS, etc.),
   - delete subscriptions if necessary,
   - delete the SNS topic.

### 5.8.5.2 CloudWatch logs, dashboards and alarms

**Resources involved**

- CloudWatch **log groups** for English Journey Lambda functions  
  - e.g. `/aws/lambda/EnglishJourney-LevelTest`,  
    `/aws/lambda/EnglishJourney-MyLearning`, etc.
- CloudWatch **dashboards** created for this workshop  
- CloudWatch **alarms** monitoring English Journey resources

**Actions**

1. Open the **CloudWatch console**.
2. Under **Logs → Log groups**:
   - search for log groups belonging to English Journey Lambda functions,
   - delete those log groups if they are no longer needed.
3. Under **Dashboards**:
   - identify dashboards dedicated to English Journey monitoring,
   - delete these dashboards.
4. Under **Alarms**:
   - find alarms that monitor English Journey Lambda functions, DynamoDB tables or MediaConvert jobs,
   - in particular, alarms that publish to `english-journey-system-alerts`,
   - delete these alarms.

---

## 5.8.6 Step 5 – Remove WAF web ACLs and IAM roles/policies

### 5.8.6.1 AWS WAF web ACL

**Resources involved**

- AWS **WAF Web ACL** attached to the CloudFront distribution that served English Journey

**Actions**

1. Open the **AWS WAF console**.
2. Locate the Web ACL associated with the English Journey CloudFront distribution.
3. If the CloudFront distribution still exists:
   - detach the Web ACL from that distribution.
4. Delete the Web ACL if it was created exclusively for English Journey.

### 5.8.6.2 IAM roles and inline policies

**Resources involved**

- Lambda **execution roles** only used by English Journey functions  
- The **MediaConvert service role** for English Journey media buckets  
- Custom IAM **policies** referencing:
  - `english-journey-media-source`,
  - `english-journey-media-output`,
  - English Journey DynamoDB tables,
  - English Journey SNS topics.

**Actions**

1. Open the **IAM console**.
2. Under **Roles**:
   - identify Lambda execution roles whose names clearly belong to English Journey,
   - confirm that no other Lambda functions or services depend on these roles,
   - delete the roles that are dedicated to this project.
3. Locate the **MediaConvert service role** used for English Journey (for example, a role name containing `mediaconvert` and `english-journey`):
   - verify it is not used by other projects,
   - delete the role.
4. Under **Policies**:
   - find custom policies that reference English Journey S3 buckets, DynamoDB tables or SNS topics,
   - delete those policies after the corresponding resources have been removed.
5. Keep any **shared roles** or the general **Amplify deployment role** if they are reused by other projects.

---

## Summary

Cleaning up after the workshop involves:

- deleting the Amplify app and hosting layer,
- removing DynamoDB tables and S3 buckets used for data and media,
- deleting MediaConvert queues and job templates,
- cleaning up SNS topics, CloudWatch logs, dashboards and alarms,
- and finally, removing dedicated WAF web ACLs and IAM roles/policies.

