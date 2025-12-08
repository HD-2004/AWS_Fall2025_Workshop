---
title : "Cấu hình Amazon CloudWatch"
date: 2025-09-10
weight : 6 
chapter : false
pre : " <b> 5.6. </b> "
---

## Mục tiêu

Để hiểu hệ thống hoạt động như thế nào và phản ứng nhanh khi có lỗi, nhóm sử dụng **Amazon CloudWatch** cho:

- Thu thập log từ các Lambda function,
- Theo dõi metric (số lần gọi, lỗi, throttling, thời gian xử lý),

---

## 5.6.1 CloudWatch Logs cho Lambda và API

Mặc định, mọi Lambda được tạo bởi Amplify đều ghi log vào **CloudWatch Logs**.  
Trong dự án này, log được dùng để:

- Theo dõi request cho:
  - Bài kiểm tra trình độ,
  - Nộp bài quiz,
  - Tra từ điển / từ vựng,
- Debug lỗi đầu vào, lỗi phân quyền (IAM) hoặc timeout,
- Ghi lại các sự kiện quan trọng.

Ngoài log mặc định, một số function còn ghi log dạng JSON có cấu trúc, giúp dễ tìm kiếm theo `userId`, `requestId` hoặc `feature`.

---

## 5.6.2 Metric cho các dịch vụ chính

CloudWatch tự động cung cấp metric cho:

- **Lambda** – số lần gọi, số lỗi, thời gian thực thi, số concurrent execution,
- **DynamoDB** – dung lượng đọc/ghi, số request bị throttled,
- **S3 / CloudFront** – băng thông, số request,
- **WAF** – số request được cho phép / bị chặn.

Trong workshop, nhóm tập trung vào một số **metric quan trọng**:

- **Error count / Error rate** của các Lambda chính,
- **Duration** của function xử lý Level Test và Quiz để phát hiện vấn đề hiệu năng,
- **ThrottledRequests** của DynamoDB để xem cấu hình capacity có đủ hay không.

---

## 5.6.3 CloudWatch Dashboard (tuỳ chọn)

Để quan sát nhanh tình trạng hệ thống, nhóm tạo một **CloudWatch Dashboard** nhỏ hiển thị:

- Biểu đồ tỷ lệ lỗi Lambda theo thời gian,
- Thời gian thực thi của các function LevelTest và Dictionary,
- (Tuỳ chọn) số request bị chặn bởi AWS WAF.

Dashboard không bắt buộc cho workshop, nhưng giúp minh hoạ rõ hơn khi có nhiều sinh viên truy cập hệ thống.

---


## Tóm tắt

CloudWatch giúp hoàn thiện khả năng giám sát cho English Journey bằng cách cung cấp:

- **Log** để phân tích và debug,
- **Metric & dashboard** để quan sát xu hướng,

Kết hợp với Amplify, SES và WAF, đây là một cấu hình vận hành đủ chắc cho dự án workshop này.
