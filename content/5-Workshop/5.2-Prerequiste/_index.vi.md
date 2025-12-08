---
title : "Các bước chuẩn bị"
date: 2025-09-10
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### IAM permissions
Gắn IAM permission policy sau vào tài khoản aws user của bạn để triển khai và dọn dẹp tài nguyên trong workshop này.
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
      "Sid": "S3Permissions",
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:ListBucket",
        "s3:DeleteObject"
      ],
      "Resource": [
        "arn:aws:s3:::your-bucket-name/*",
        "arn:aws:s3:::your-bucket-name"
      ]
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
    },
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

## Kiến thức nền tảng

Workshop giả định người đọc có một số kiến thức cơ bản:

- **Lập trình web**
  - HTML, CSS, JavaScript/TypeScript,
  - React hoặc một framework SPA tương tự.

- **Kiến thức nền về Cloud và AWS**
  - Khái niệm region, account,
  - Hiểu sơ lược về dịch vụ managed (Cognito, Lambda, DynamoDB,…),
  - Khái niệm cơ bản về IAM (identity, role, policy, least privilege).


---

## Công cụ và dịch vụ

Để có thể tái hiện workshop trong một môi trường thực tế, bạn sẽ cần các công cụ và dịch vụ sau:

- Một **tài khoản AWS** với quyền tạo:
  - AWS Amplify,
  - Cognito User Pools,
  - AWS Lambda,
  - AWS DynamoDB,
  - Các identity và configuration set của SES,
  - CloudWatch,
  - IAM roles và policies.

- **Node.js và npm** cài đặt trên máy local  
  (để chạy và build frontend React).

- **AWS CLI** đã được cấu hình với IAM user hoặc role có đủ quyền.

- (Tuỳ chọn) **Amplify CLI / Gen 2 tooling**  
  để định nghĩa hạ tầng bằng code và kết nối dự án với Amplify.

---

## Mã nguồn và cấu trúc dự án

Dự án English Journey được tổ chức như sau:

- Một **frontend React** (các trang như Level Test, Dictionary, Vocabulary, Quiz, Reading),
- Backend được định nghĩa thông qua **Amplify** (Cognito, Lambda, DynamoDB),
- Hạ tầng bổ sung cho **SES (email), CloudWatch và WAF**.


Các mục tiếp theo (từ 5.3 trở đi) sẽ dựa trên các điều kiện tiên quyết này và giải thích chi tiết hơn từng nhóm dịch vụ AWS.




