---
title : "Cấu hình Amazon SNS cho thông báo"
date: 2025-09-10
weight : 5 
chapter : false
pre : " <b> 5.5. </b> "
---



## Mục tiêu

English Journey cần một cơ chế nhẹ để gửi **thông báo** cho cả người học và người vận hành hệ thống, ví dụ:

- nhắc nhở hoạt động học tập hằng ngày,
- thông báo có bài học / quiz mới,
- cảnh báo khi backend gặp lỗi.

Để làm việc này, nhóm sử dụng **Amazon Simple Notification Service (SNS)**.

---

## 5.5.1 Vai trò của SNS trong kiến trúc

SNS được dùng theo hai hướng chính:

1. **Thông báo cho người dùng** – các Lambda publish message khi:
   - có bài học hoặc quiz mới,
   - người dùng đạt một mốc tiến độ (ví dụ số ngày học liên tiếp),
   - có kết quả kiểm tra trình độ hoặc daily check-in.

   Những thông báo này có thể:
   - gửi trực tiếp qua email / SMS, hoặc
   - được một Lambda khác nhận và lưu vào DynamoDB để hiển thị trong giao diện web.

2. **Cảnh báo hệ thống** – CloudWatch Alarm publish lên SNS khi:
   - Lambda lỗi nhiều lần,
   - tỷ lệ lỗi vượt ngưỡng,
   - hoặc metric quan trọng nằm ngoài khoảng an toàn.

   Nhóm đăng ký email vào topic này để nhận cảnh báo kịp thời.

---

## 5.5.2 Tạo các SNS topic

Nhóm tạo tối thiểu hai topic:

- `english-journey-user-notifications` – dùng cho **thông báo ứng dụng** gửi đến học viên.
- `english-journey-system-alerts` – dùng cho **cảnh báo vận hành** từ CloudWatch.

Trong giai đoạn phát triển, cả hai topic đều có **subscription email**, giúp quan sát nhanh luồng thông báo.

---

## 5.5.3 Lambda publish thông báo lên SNS

Các Lambda backend sử dụng AWS SDK để publish message tới SNS.  
Luồng cơ bản:

1. Sau khi xử lý xong nghiệp vụ (ví dụ daily check-in), Lambda xây dựng payload JSON gồm:
   - `userId`,
   - `type` (NEW_LESSON, LEVEL_UP, REMINDER, …),
   - tiêu đề và nội dung thông báo (đã dịch).
2. Lambda publish payload đó lên **user-notifications topic**.
3. Một consumer khác (hoặc chính Lambda) có thể:
   - lưu thông báo vào DynamoDB,
   - hoặc để SNS gửi trực tiếp email/SMS trong các trường hợp đơn giản.

Cách tiếp cận này tách riêng **logic nghiệp vụ** và **kênh gửi thông báo**, nên sau này có thể bổ sung thêm subscriber (mobile push, dịch vụ khác, …) mà không phải sửa Lambda ban đầu.

---

## 5.5.4 SNS cho cảnh báo hệ thống

Với topic **system-alerts**, nhóm chủ yếu dùng subscription qua email:

- Các CloudWatch Alarm (trình bày ở phần 5.9) được cấu hình action là publish lên topic này.
- Khi alarm chuyển sang trạng thái `ALARM` (ví dụ tỷ lệ lỗi Lambda cao), SNS sẽ gửi email lập tức tới nhóm vận hành.

Cách làm này đơn giản nhưng đủ hiệu quả cho môi trường workshop.
