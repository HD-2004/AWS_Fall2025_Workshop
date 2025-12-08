---
title : "Prerequiste"
date: 2025-09-10
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

#### IAM permissions
Add the following IAM permission policy to your user account to deploy and cleanup this workshop.
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "CognitoPermissions",
      "Effect": "Allow",
      "Action": [
        "cognito-idp:AdminCreateUser",
        "cognito-idp:AdminUpdateUserAttributes",
        "cognito-idp:ListUsers",
        "cognito-idp:SignUp",
        "cognito-idp:InitiateAuth"
      ],
      "Resource": "*"
    },
    {
      "Sid": "LambdaPermissions",
      "Effect": "Allow",
      "Action": [
        "lambda:InvokeFunction",
        "lambda:ListFunctions",
        "lambda:CreateFunction",
        "lambda:UpdateFunctionCode"
      ],
      "Resource": "*"
    },
    {
      "Sid": "DynamoDBPermissions",
      "Effect": "Allow",
      "Action": [
        "dynamodb:PutItem",
        "dynamodb:GetItem",
        "dynamodb:UpdateItem",
        "dynamodb:Query",
        "dynamodb:Scan"
      ],
      "Resource": "arn:aws:dynamodb:region:account-id:table/your-table-name"
    },
    {
      "Sid": "SESPermissions",
      "Effect": "Allow",
      "Action": [
        "ses:SendEmail",
        "ses:SendRawEmail"
      ],
      "Resource": "*"
    }

    {
      "Sid": "CloudWatchPermissions",
      "Effect": "Allow",
      "Action": [
        "cloudwatch:PutMetricData",
        "cloudwatch:GetMetricData",
        "cloudwatch:DescribeAlarms",
        "cloudwatch:SetAlarmState"
      ],
      "Resource": "*"
    },
    {
      "Sid": "WAFPermissions",
      "Effect": "Allow",
      "Action": [
        "wafv2:CreateWebACL",
        "wafv2:UpdateWebACL",
        "wafv2:GetWebACL",
        "wafv2:ListWebACLs"
      ],
      "Resource": "*"
    },
    {
      "Sid": "IAMPermissions",
      "Effect": "Allow",
      "Action": [
        "iam:CreateRole",
        "iam:AttachRolePolicy",
        "iam:ListPolicies",
        "iam:GetRole"
      ],
      "Resource": "*"
    }
  ]
}

```

## Technical background

This workshop assumes that the reader has basic knowledge of:

- **Web development**  
  - HTML, CSS and JavaScript/TypeScript  
  - React or another single-page application framework

- **Cloud and AWS fundamentals**  
  - what an AWS region and account are,  
  - the idea of managed services (Cognito, Lambda, DynamoDB,…),  
  - basic concepts of IAM (identity, role, policy, least privilege).

The report is written so that it can be understood even if the reader does not actually deploy the system, but this background helps to follow the architecture.

---

## Tools and services

To reproduce the workshop in a real environment, the following tools and services would be required:

- An **AWS account** with permission to create:
  - Amplify apps,
  - Cognito User Pools,
  - Lambda functions,
  - DynamoDB tables,
  - SES identities and configuration sets,
  - IAM roles and policies.

- **Node.js and npm** installed locally  
  (for running and building the React frontend).

- The **AWS CLI** configured with an IAM user or role that has sufficient permissions.

- Optionally, the **Amplify CLI / Gen 2 tooling**  
  to define infrastructure in code and connect the project to Amplify.

---

## Source code and project structure

The English Journey project is organised as:

- a **React frontend** (pages such as Level Test, Dictionary, Vocabulary, My Learning),
- a backend defined via **Amplify** (Cognito, Lambda, DynamoDB),
- additional infrastructure for **SES (email), CloudWatch and WAF**.

In this Hugo workshop site, we only present the **architecture diagrams, explanations and example code**. The actual AWS resources do not need to be created to understand the design decisions.

The following sections (5.3 and onwards) build on these prerequisites and explain each group of AWS services in more detail.
