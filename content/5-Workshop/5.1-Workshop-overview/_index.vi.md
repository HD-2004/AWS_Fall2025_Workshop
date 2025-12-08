---
title : "Giới thiệu"
date: 2025-09-10
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Giới thiệu về dự án

Workshop này trình bày kiến trúc và cách triển khai ứng dụng web **English Journey** –  
một website học tiếng Anh được xây dựng trên **nền tảng AWS**.

Ứng dụng cho phép người học:

- Đăng ký / đăng nhập an toàn,
- Làm **bài kiểm tra trình độ** (A1–C1) trước khi bắt đầu học,
- Luyện từ vựng,bài đọc và làm quiz,
- Theo dõi quá trình học của bản thân.

Về mặt hạ tầng, dự án minh họa cách kết hợp nhiều dịch vụ managed của AWS:
- **AWS Amplify** làm nền tảng trung tâm cho backend và hosting của web app,
- **Amazon Cognito** cho xác thực người dùng,
- **AWS Lambda** cho nghiệp vụ backend (Level Test, Quiz, Vocabulary),
- **Amazon DynamoDB** cho dữ liệu ứng dụng,
- **Amazon SES** để gửi email thông báo ( xác thực tài khoản ),
- **Amazon CloudWatch** cho log, metric,
- **AWS WAF** để bảo vệ web trước một số tấn công phổ biến,
- cùng **IAM Roles & Policies** để kiểm soát quyền truy cập giữa các thành phần.

#### Mục tiêu của workshop

Sau khi đọc phần workshop, người đọc có thể:

1. Hiểu **kiến trúc tổng thể** của ứng dụng English Journey trên AWS.
2. Giải thích vai trò của **Amplify** trong việc điều phối Cognito, Lambda, DynamoDB và Route53.
3. Mô tả được luồng xử lý của tính năng **kiểm tra trình độ**, từ frontend → Lambda → DynamoDB.
4. Hiểu cách **thông báo** được gửi qua email bằng Amazon SES.
5. Nhận thức tầm quan trọng của **CloudWatch**, **IAM** và **WAF** đối với giám sát và bảo mật.

#### Tổng quan về workshop
Dự án này tận dụng các dịch vụ của AWS để xây dựng và triển khai ứng dụng:

    - WS Amplify: Dịch vụ hosting cho phép triển khai ứng dụng nhanh chóng và dễ dàng.

    - AWS Lambda: Xử lý các tác vụ, backend và logic của ứng dụng mà không cần quản lý máy chủ, giúp tiết kiệm chi phí và tài nguyên.

    - Amazon DynamoDB: Cơ sở dữ liệu NoSQL dùng để lưu trữ dữ liệu người dùng, từ vựng và kết quả học tập.

    - Amazon CloudWatch: Giám sát hiệu suất và hoạt động của ứng dụng, cung cấp log và dễ dàng kiểm tra khi có sự cố.

<img src="/images/5-Workshop/5.1-Workshop-overview/architecture2.png" alt="Overview" width="600">
