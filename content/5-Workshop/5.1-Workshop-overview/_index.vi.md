---
title : "Giới thiệu"
date: 2025-09-10
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Giới thiệu về dự án

Workshop này trình bày kiến trúc và cách triển khai ứng dụng web **English Journey** –  
một website học từ vựng tiếng Anh được xây dựng trên **nền tảng AWS**.

Ứng dụng cho phép người học:

- đăng ký / đăng nhập an toàn,
- làm **bài kiểm tra trình độ** (A1–C1) trước khi bắt đầu học,
- luyện từ vựng và làm quiz,
- theo dõi quá trình học của bản thân.

Về mặt hạ tầng, dự án minh họa cách kết hợp nhiều dịch vụ managed của AWS:
- **AWS Amplify** làm nền tảng trung tâm cho backend và hosting của web app,
- **Amazon Cognito** cho xác thực người dùng,
- **AWS Lambda** cho nghiệp vụ backend (Level Test, Quiz, Vocabulary),
- **Amazon DynamoDB** cho dữ liệu ứng dụng,
- **Amazon S3 + CloudFront** cho nội dung tĩnh và file media,
- **AWS Elemental MediaConvert** để xử lý audio/video,
- **Amazon SNS** cho thông báo và cảnh báo,
- **Amazon CloudWatch** cho log, metric và alarm,
- **AWS WAF** để bảo vệ web trước một số tấn công phổ biến,
- cùng **IAM Roles & Policies** để kiểm soát quyền truy cập giữa các thành phần.

#### Mục tiêu của workshop

Sau khi đọc phần workshop, người đọc có thể:

1. Hiểu **kiến trúc tổng thể** của ứng dụng English Journey trên AWS.
2. Giải thích vai trò của **Amplify** trong việc điều phối Cognito, Lambda, DynamoDB và S3.
3. Mô tả được luồng xử lý của tính năng **kiểm tra trình độ**, từ frontend → Lambda → DynamoDB.
4. Nắm được cách hệ thống xử lý **nội dung media** (audio/video) với S3 và MediaConvert.
5. Hiểu cách **gửi thông báo** cho người dùng và **cảnh báo hệ thống** bằng SNS.
6. Nhận thức tầm quan trọng của **CloudWatch** và **IAM** đối với giám sát và bảo mật.

#### Tổng quan về workshop
Dự án này tận dụng các dịch vụ của AWS để xây dựng và triển khai ứng dụng:

    - WS Amplify: Dịch vụ hosting full-stack cho phép triển khai ứng dụng nhanh chóng và dễ dàng.

    - AWS Lambda: Xử lý các tác vụ và logic của ứng dụng mà không cần quản lý máy chủ, giúp tiết kiệm chi phí và tài nguyên.

    - Amazon DynamoDB: Cơ sở dữ liệu NoSQL dùng để lưu trữ dữ liệu người dùng, từ vựng và kết quả học tập.

    - Amazon S3: Lưu trữ tài liệu học (video, âm thanh, hình ảnh) để hỗ trợ quá trình học tập.

    - AWS MediaConvert: Xử lý và chuyển đổi các tập tin media như video hoặc âm thanh để sử dụng trong các bài học.

    - Amazon CloudWatch: Giám sát hiệu suất và hoạt động của ứng dụng, cung cấp log và cảnh báo khi có sự cố.

<img src="/images/5-Workshop/5.1-Workshop-overview/diagram1.png" alt="Overview" width="600">
