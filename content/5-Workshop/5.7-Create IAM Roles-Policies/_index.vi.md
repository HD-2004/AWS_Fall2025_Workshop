---
title : "IAM Roles & Policies"
date: 2025-09-10
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

## Mục tiêu

Phần này giải thích cách thiết kế **IAM Role và Policy** cho ứng dụng English Journey.

Đa số role được sinh tự động bởi **AWS Amplify**, nhưng nhóm vẫn cần hiểu:

- có những loại role nào,
- mỗi role được phép làm gì với S3, DynamoDB, SNS, MediaConvert, …,
- và cách áp dụng nguyên tắc **least-privilege** (ít quyền nhất có thể).

---

## 5.7.1 Tổng quan IAM trong kiến trúc

Trong kiến trúc mô tả ở các mục 5.3–5.6, IAM đóng vai trò “chất keo” kết nối dịch vụ:

- **Amplify** dùng role để deploy CloudFormation và host frontend.
- **Lambda** dùng execution role để truy cập DynamoDB, S3, SNS.
- **MediaConvert** assume một service role để đọc/ghi file media trên S3.
- **CloudWatch** và **SNS** dựa vào IAM để gửi log, alarm và thông báo.

Mục tiêu thiết kế là: *mỗi thành phần chỉ có đúng lượng quyền cần thiết để làm việc*.

---

## 5.7.2 Lambda execution role

Khi khai báo các backend function trong Amplify (Level Test, My Learning, Dictionary, Vocabulary,…), Amplify sẽ tự tạo **Lambda execution role** tương ứng.

Mỗi role gồm:

- **Trust policy** – cho phép dịch vụ Lambda assume role:

  ```json
  {
    "Effect": "Allow",
    "Principal": { "Service": "lambda.amazonaws.com" },
    "Action": "sts:AssumeRole"
  }
