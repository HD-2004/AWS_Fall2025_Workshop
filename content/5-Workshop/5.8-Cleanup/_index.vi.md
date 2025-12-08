---
title : "Dọn dẹp tài nguyên"
date: 2025-09-10
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---

## Mục tiêu

Phần này mô tả cách **dọn dẹp (cleanup) các tài nguyên AWS** liên quan đến workshop English Journey  
sau khi đã hoàn thành việc demo hoặc kiểm thử.

Trong báo cáo, chúng ta chủ yếu mô tả thiết kế và cách triển khai. Tuy nhiên,  
nếu triển khai thật trên tài khoản AWS, việc xoá bỏ tài nguyên không còn dùng là cần thiết để:

- tránh phát sinh chi phí không cần thiết,
- giữ môi trường AWS gọn gàng, dễ quản lý.

> ⚠️ Lưu ý: chỉ xoá các tài nguyên **dành riêng cho dự án này**.  
> Không xoá nhầm bucket, role hoặc alarm đang được dùng chung bởi hệ thống khác.

---

## 5.8.1 Cách tiếp cận tổng quát

Ở mức tổng quan, quy trình cleanup gồm:

1. Xoá **Amplify App và phần hosting** (CloudFront + S3).
2. Xoá dữ liệu ứng dụng trong **DynamoDB** và các **bucket S3** dùng cho nội dung / media.
3. Xoá các thành phần xử lý media (**MediaConvert queue / job template**).
4. Xoá **SNS topic** và **CloudWatch** dashboard / alarm được tạo cho workshop.
5. Xoá **WAF Web ACL** và các **IAM Role / Policy** chỉ dùng cho English Journey.

Thứ tự không bắt buộc tuyệt đối, nhưng thường nên bắt đầu từ dịch vụ “tầng trên” (Amplify) rồi dần xuống các thành phần bên dưới.

---

## 5.8.2 Bước 1 – Xoá Amplify App và hosting

**Tài nguyên liên quan**

- AWS Amplify App cho English Journey  
- S3 bucket dùng để hosting frontend (Amplify hosting)  
- CloudFront distribution phục vụ website frontend

**Các bước thực hiện**

1. Mở **AWS Amplify console**.
2. Tìm **Amplify App** tương ứng với dự án English Journey.
3. Chọn **Delete app** và xác nhận.
4. Thao tác này sẽ tự động xoá:
   - pipeline build & deploy của Amplify,
   - **S3 hosting bucket** chứa bản build React,
   - **CloudFront distribution** phục vụ website.

> Nếu hạ tầng được tạo bằng Amplify CLI (`amplify init`, `amplify push`),  
> có thể dùng lệnh `amplify delete` tại thư mục root của project  
> để xoá toàn bộ stack backend do Amplify quản lý.

---

## 5.8.3 Bước 2 – Xoá DynamoDB tables và nội dung trên S3

### 5.8.3.1 DynamoDB

**Tài nguyên liên quan**

- `EnglishJourney-Users`  
- `EnglishJourney-PlacementTestResults`  
- `EnglishJourney-Vocabulary`  
- `EnglishJourney-UserProgress`  
  (và các bảng khác nếu được tạo riêng cho English Journey)

**Các bước thực hiện**

1. Mở **Amazon DynamoDB console**.
2. Lọc / tìm các bảng có tên bắt đầu bằng `EnglishJourney-` (hoặc prefix mà nhóm sử dụng).
3. (Tuỳ chọn) **Export** dữ liệu cần giữ lại để làm tài liệu hoặc backup.
4. Với mỗi bảng:
   - kiểm tra kỹ đảm bảo chỉ dùng cho workshop này,
   - chọn **Delete table** và xác nhận.

### 5.8.3.2 Các bucket S3 dùng cho media / nội dung

**Tài nguyên liên quan**

- `english-journey-media-source` – nơi lưu file media gốc (audio / video, …)  
- `english-journey-media-output` – nơi lưu kết quả encode từ MediaConvert  
- Các S3 bucket khác chỉ dùng cho nội dung English Journey (reading, asset tĩnh, …)

**Các bước thực hiện**

1. Mở **Amazon S3 console**.
2. Với từng bucket liên quan đến English Journey:
   - trước tiên **xoá toàn bộ object** bên trong (empty bucket),
   - sau đó mới xoá được bucket.
3. Các bucket thường cần xoá:
   - `english-journey-media-source`
   - `english-journey-media-output`
   - các bucket nội dung khác dành riêng cho English Journey (nếu có).

> Bucket S3 dùng cho Amplify hosting thường đã được xoá cùng với Amplify App ở bước 5.8.2.

---

## 5.8.4 Bước 3 – Xoá MediaConvert queues và job templates

**Tài nguyên liên quan**

- Các **MediaConvert queue** dùng cho job transcode của English Journey  
- Các **MediaConvert job template** trỏ đến bucket S3 của English Journey

**Các bước thực hiện**

1. Mở **AWS Elemental MediaConvert console**.
2. Vào phần **Queues**:
   - xác định các queue chỉ dùng cho việc xử lý media / bài học của English Journey,
   - xoá các queue này.
3. Vào phần **Job templates**:
   - tìm các template có tham chiếu tới `english-journey-media-source` hoặc `english-journey-media-output`,
   - xoá các template đó.

Bản thân MediaConvert không tốn nhiều chi phí khi không có job,  
nhưng dọn dẹp queue / template giúp cấu hình tài khoản gọn gàng, dễ quản lý.

---

## 5.8.5 Bước 4 – Dọn dẹp SNS topics và CloudWatch (logs / alarms)

### 5.8.5.1 SNS topics

**Tài nguyên liên quan**

- `english-journey-user-notifications` – topic cho thông báo gửi đến người học  
- `english-journey-system-alerts` – topic cho cảnh báo vận hành từ CloudWatch

**Các bước thực hiện**

1. Mở **Amazon SNS console**.
2. Trong mục **Topics**, tìm:
   - `english-journey-user-notifications`,
   - `english-journey-system-alerts`.
3. Với mỗi topic:
   - xem các **subscription** hiện có (email, SMS, …),
   - nếu cần, xoá subscription,
   - sau đó xoá SNS topic.

### 5.8.5.2 CloudWatch logs, dashboards và alarms

**Tài nguyên liên quan**

- **Log group** CloudWatch của các Lambda function trong English Journey  
  - ví dụ: `/aws/lambda/EnglishJourney-LevelTest`,  
    `/aws/lambda/EnglishJourney-MyLearning`, ...
- **Dashboard** dùng để giám sát hệ thống English Journey  
- **Alarm** CloudWatch theo dõi Lambda / DynamoDB / MediaConvert của dự án

**Các bước thực hiện**

1. Mở **Amazon CloudWatch console**.
2. Trong mục **Logs → Log groups**:
   - tìm các log group thuộc về Lambda của English Journey,
   - xoá những log group này nếu không còn cần thiết.
3. Trong mục **Dashboards**:
   - xác định các dashboard được tạo riêng cho English Journey,
   - xoá các dashboard đó.
4. Trong mục **Alarms**:
   - tìm các alarm theo dõi Lambda, DynamoDB hoặc MediaConvert của English Journey,
   - đặc biệt là những alarm publish lên topic `english-journey-system-alerts`,
   - xoá các alarm này.

---

## 5.8.6 Bước 5 – Xoá WAF Web ACL và IAM Roles / Policies

### 5.8.6.1 AWS WAF Web ACL

**Tài nguyên liên quan**

- **AWS WAF Web ACL** gắn với CloudFront distribution phục vụ English Journey

**Các bước thực hiện**

1. Mở **AWS WAF console**.
2. Tìm Web ACL được gắn với CloudFront distribution của English Journey.
3. Nếu CloudFront distribution vẫn còn tồn tại:
   - gỡ Web ACL khỏi distribution.
4. Xoá Web ACL nếu nó được tạo riêng cho English Journey.

### 5.8.6.2 IAM Roles và inline Policies

**Tài nguyên liên quan**

- Các **Lambda execution role** chỉ dùng cho Lambda của English Journey  
- **Service role của MediaConvert** cho bucket media English Journey  
- Các **IAM policy** tuỳ chỉnh có Resource trỏ tới:
  - `english-journey-media-source`,
  - `english-journey-media-output`,
  - các bảng DynamoDB English Journey,
  - các SNS topic của English Journey.

**Các bước thực hiện**

1. Mở **IAM console**.
2. Trong mục **Roles**:
   - xác định các Lambda execution role có tên gắn với English Journey,
   - kiểm tra chắc chắn không còn Lambda / service nào khác sử dụng các role này,
   - xoá những role chỉ phục vụ dự án này.
3. Tìm **MediaConvert service role** dùng cho English Journey (thường trong tên có `mediaconvert` và `english-journey`):
   - xác nhận role không dùng cho project khác,
   - xoá role đó.
4. Trong mục **Policies**:
   - tìm các custom policy có Resource trỏ tới S3, DynamoDB, SNS của English Journey,
   - sau khi đã xoá tài nguyên tương ứng, xoá các policy này.
5. **Không** xoá các role dùng chung hoặc **Amplify deployment role** nếu chúng còn được dùng bởi project khác.

---

## Tóm tắt

Quy trình cleanup sau workshop bao gồm:

- xoá Amplify App và lớp hosting frontend,
- xoá bảng DynamoDB và các bucket S3 dành riêng cho dữ liệu / media,
- xoá MediaConvert queue và job template,
- dọn dẹp SNS topic, CloudWatch log, dashboard và alarm,
- cuối cùng là xoá Web ACL và IAM Role / Policy chỉ phục vụ English Journey.

