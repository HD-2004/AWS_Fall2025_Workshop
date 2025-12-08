---
title : "IAM Roles & Policies"
date: 2025-09-10
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

## Mục tiêu

Phần này giải thích cách **IAM roles và policies** được thiết kế cho ứng dụng English Journey.

Phần lớn các role được sinh tự động bởi **AWS Amplify**, nhưng chúng ta vẫn cần hiểu:

- có những role nào tồn tại,
- mỗi role được phép làm gì (S3, DynamoDB, SES, MediaConvert, …),
- và cách chúng ta áp dụng nguyên tắc **ít quyền nhất (least privilege)**.

---

## 5.7.1 Tổng quan IAM trong kiến trúc

Trong kiến trúc từ các mục 5.3–5.6, IAM là “chất keo” kết nối các dịch vụ với nhau:

- **Amplify** sử dụng IAM roles để triển khai các CloudFormation stack và host frontend.
- Các hàm **Lambda** dùng execution role để truy cập DynamoDB, S3 và SES (để gửi email).
- **MediaConvert** dùng service role để đọc/ghi file media trong S3.
- **CloudWatch** và **SES** dựa vào IAM để có thể gửi cảnh báo và email thông báo một cách chính xác.

Mục tiêu thiết kế là *mỗi thành phần chỉ nhận đúng số quyền tối thiểu mà nó cần*.

---

## 5.7.2 Lambda execution roles

Khi chúng ta định nghĩa các backend function trong Amplify (Level Test, My Learning, Dictionary, Vocabulary, …), Amplify sẽ tự động tạo một **Lambda execution role** cho mỗi function.

Mỗi role sẽ có:

- **Trust policy** – cho phép dịch vụ Lambda được assume (nhận) role này:

  ```json
  {
    "Effect": "Allow",
    "Principal": { "Service": "lambda.amazonaws.com" },
    "Action": "sts:AssumeRole"
  }
