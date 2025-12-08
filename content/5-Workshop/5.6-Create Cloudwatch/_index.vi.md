---
title : "Cấu hình Amazon CloudWatch"
date: 2025-09-10
weight : 6 
chapter : false
pre : " <b> 5.6. </b> "
---

## Mục tiêu

Để hiểu hệ thống hoạt động như thế nào và phản ứng nhanh khi có lỗi, nhóm sử dụng **Amazon CloudWatch** cho:

- thu thập log từ các Lambda function,
- theo dõi metric (số lần gọi, lỗi, throttling, thời gian xử lý),
- tạo alarm và gửi cảnh báo thông qua SNS.

---

## 5.6.1 CloudWatch Logs cho Lambda và API

Mặc định, mọi Lambda được tạo bởi Amplify đều ghi log vào **CloudWatch Logs**.  
Trong dự án này, log được dùng để:

- theo dõi request cho:
  - bài kiểm tra trình độ,
  - nộp bài quiz,
  - tra từ điển / từ vựng,
- debug lỗi đầu vào, lỗi phân quyền (IAM) hoặc timeout,
- ghi lại các sự kiện quan trọng (ví dụ khi tạo job MediaConvert).

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

- biểu đồ tỷ lệ lỗi Lambda theo thời gian,
- thời gian thực thi của các function LevelTest và Dictionary,
- (tuỳ chọn) số request bị chặn bởi AWS WAF.

Dashboard không bắt buộc cho workshop, nhưng giúp minh hoạ rõ hơn khi có nhiều sinh viên truy cập hệ thống.

---

## 5.6.4 CloudWatch Alarm tích hợp với SNS

Phần quan trọng nhất trong cấu hình CloudWatch là **Alarm**.

Một số alarm được thiết lập:

1. **Alarm lỗi Lambda**

   - Theo dõi metric `Errors` của các function quan trọng (LevelTest, MyLearning, Dictionary).
   - Kích hoạt khi số lỗi vượt ngưỡng nhỏ trong một vài chu kỳ liên tiếp.
   - Action: publish lên SNS topic `english-journey-system-alerts`.

2. **Alarm DynamoDB throttling**

   - Theo dõi `ThrottledRequests` trên các bảng chính.
   - Cho biết khi workload vượt quá capacity được cấu hình.
   - Cũng gửi thông báo qua SNS.

3. **Alarm cho MediaConvert** (tuỳ chọn)

   - Theo dõi metric lỗi hoặc dead-letter queue liên quan đến việc xử lý job MediaConvert.

Nhờ các alarm này, khi có sự cố nghiêm trọng (ví dụ deploy Lambda lỗi), nhóm sẽ nhận được email cảnh báo và có thể xử lý kịp thời.

---

## Tóm tắt

CloudWatch giúp hoàn thiện khả năng giám sát cho English Journey bằng cách cung cấp:

- **log** để phân tích và debug,
- **metric & dashboard** để quan sát xu hướng,
- **alarm** gắn trực tiếp với SNS để cảnh báo chủ động.

Kết hợp với Amplify, SNS và WAF, đây là một cấu hình vận hành đủ chắc cho dự án workshop này.
