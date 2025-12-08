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
      "Sid": "MediaConvertPermissions",
      "Effect": "Allow",
      "Action": [
        "mediaconvert:CreateJob",
        "mediaconvert:DescribeJob",
        "mediaconvert:ListJobs",
        "mediaconvert:GetJob"
      ],
      "Resource": "*"
    },
    {
      "Sid": "SNSPermissions",
      "Effect": "Allow",
      "Action": [
        "sns:Publish",
        "sns:CreateTopic",
        "sns:Subscribe"
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
  - khái niệm region, account,
  - hiểu sơ lược về dịch vụ managed (Cognito, Lambda, DynamoDB, S3,…),
  - khái niệm cơ bản về IAM (identity, role, policy, least privilege).

Báo cáo được viết sao cho vẫn có thể hiểu được **dù không trực tiếp deploy lên AWS**, nhưng những kiến thức trên sẽ giúp theo dõi kiến trúc dễ dàng hơn.

---

## Công cụ và dịch vụ (nếu triển khai thực tế)

Nếu muốn tái hiện workshop trên tài khoản AWS thật, cần có:

- Một **tài khoản AWS** với quyền tạo:
  - Amplify App,
  - Cognito User Pool,
  - Lambda Function,
  - DynamoDB Table,
  - S3 Bucket,
  - SNS Topic,
  - CloudWatch Alarm,
  - IAM Role và Policy.

- **Node.js và npm** cài trên máy local  
  (để chạy và build frontend React).

- **AWS CLI** đã được cấu hình profile/credential phù hợp.

- Tuỳ chọn, công cụ **Amplify CLI / Gen 2**  
  để định nghĩa hạ tầng bằng code và kết nối project với Amplify.

---

## Mã nguồn và cấu trúc dự án

Dự án English Journey được tổ chức gồm:

- **Frontend React** – các trang Level Test, Dictionary, Vocabulary, My Learning, v.v.
- Backend được định nghĩa thông qua **Amplify** – bao gồm Cognito, Lambda, DynamoDB, S3.
- Các thành phần bổ sung: **MediaConvert, SNS, CloudWatch, WAF** hỗ trợ media, thông báo, giám sát và bảo mật.

Trong website Hugo của workshop này, nhóm chỉ trình bày:

- sơ đồ kiến trúc,
- giải thích thiết kế,
- và một số đoạn mã minh hoạ.

Người đọc **không bắt buộc** phải tạo tài nguyên thật trên AWS để hiểu được nội dung.  
Các mục tiếp theo (từ 5.3 trở đi) sẽ lần lượt đi sâu vào từng nhóm dịch vụ AWS dựa trên các điều kiện tiên quyết ở trên.



