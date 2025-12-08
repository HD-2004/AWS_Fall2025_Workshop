---
title : "Cấu hình AWS MediaConvert"
date: 2025-09-10
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---


## Mục tiêu

English Journey có các nội dung nghe và video (ví dụ hội thoại ngắn, video giải thích).  
Để đảm bảo các file media này phát mượt trên web, nhóm sử dụng **AWS Elemental MediaConvert** để chuyển đổi (transcode) file gốc sang định dạng tối ưu cho trình duyệt và thiết bị di động, sau đó lưu vào một bucket S3 riêng.

Phần này mô tả cách cấu hình MediaConvert và cách tích hợp vào kiến trúc hệ thống.

---

## 5.4.1 Vai trò của MediaConvert trong kiến trúc

Trên sơ đồ kiến trúc, MediaConvert nằm giữa hai bucket S3:

- **S3 (source)** – nơi upload file media gốc (ví dụ video do giảng viên cung cấp).
- **AWS MediaConvert** – dịch vụ chuyển đổi các file này thành MP4/HLS đã nén, phù hợp cho web.
- **S3 (destination)** – bucket lưu kết quả đã chuyển đổi.  
  Frontend sẽ truy cập các file này thông qua CloudFront / Amplify.

Một function Lambda chịu trách nhiệm tạo job MediaConvert mỗi khi có file mới ở source bucket.

---

## 5.4.2 Tạo IAM Role cho MediaConvert

MediaConvert cần quyền đọc từ **source bucket** và ghi vào **destination bucket**.  
Nhóm tạo một **IAM service role** riêng với:

- **trust policy** cho phép dịch vụ `mediaconvert.amazonaws.com` được assume role,
- **inline policy** cấp quyền:
  - `s3:GetObject` trên bucket nguồn,
  - `s3:PutObject` trên bucket đích,
  - `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents` cho CloudWatch Logs.

Role này được tham chiếu trong tất cả MediaConvert job.

*(Chi tiết JSON policy được mô tả ở phần 5.11 – IAM Roles & Policies.)*

---

## 5.4.3 Tạo MediaConvert queue và job template

Để đơn giản hóa pipeline, nhóm cấu hình:

1. Một **standard queue** trong MediaConvert – tất cả job transcode sẽ được gửi vào queue này.
2. Một **job template** bao gồm:
   - cấu hình input (H.264, AAC, độ phân giải đầu vào),
   - các output group:
     - MP4 cho playback cơ bản,
     - tùy chọn HLS cho adaptive streaming,
   - đường dẫn output mặc định trỏ tới **destination S3 bucket**, với cấu trúc ví dụ:

     ```text
     s3://english-journey-media-output/{lessonId}/
     ```

Nhờ job template, Lambda chỉ cần cung cấp file input và một số tham số như `lessonId` là có thể tạo job mới.

---

## 5.4.4 Tích hợp với Lambda

Một **AWS Lambda** backend (được quản lý qua Amplify) điều phối MediaConvert theo luồng:

1. Khi có file media mới upload vào **source bucket**, **S3 event** sẽ gọi Lambda.
2. Lambda xây dựng request tạo job MediaConvert:
   - input: `s3://source-bucket/.../file.mp4`,
   - output: đường dẫn trong bucket đích dựa trên bài học / unit,
   - tên job template,
   - ARN của service role MediaConvert.
3. Lambda gọi API **CreateJob** của MediaConvert (endpoint theo region).
4. Tuỳ chọn, một Lambda khác (hoặc cùng function) có thể lắng nghe sự kiện job hoàn thành (qua CloudWatch Events hoặc SNS) để cập nhật metadata lên DynamoDB (ví dụ đánh dấu bài học “đã sẵn sàng”).

Nhờ vậy, giảng viên chỉ cần upload video gốc, còn hệ thống tự động chuyển đổi và chuẩn bị phiên bản tối ưu cho người học.

---

## 5.4.5 Lợi ích

Việc dùng MediaConvert trong English Journey mang lại:

- **Khả năng phát ổn định** trên nhiều trình duyệt / thiết bị.
- **Dung lượng nhỏ hơn**, tiết kiệm băng thông cho sinh viên.
- **Quy trình tự động**, không cần convert video thủ công trên máy cá nhân.
