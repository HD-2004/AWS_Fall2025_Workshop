---
title : "Dọn dẹp tài nguyên"
date: 2025-09-10
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---

## Mục tiêu

Phần này hướng dẫn bạn **xoá các tài nguyên AWS** đã được tạo hoặc sử dụng trong workshop English Journey, để tránh phát sinh chi phí không cần thiết.

Chỉ nên thực hiện bước này khi bạn đã hoàn tất việc thử nghiệm kiến trúc.

---

## 5.9.1 – Xoá ứng dụng Amplify và frontend hosting

1. Mở console **Amplify** ở đúng Region đã dùng cho workshop.
2. Chọn **Amplify app** đang host frontend của English Journey.
3. Chọn **Actions → Delete app** (hoặc nút **Delete** trong trang chi tiết app).
4. Xác nhận xoá theo hướng dẫn.

Khi xoá Amplify app:

- Amplify sẽ tự động xoá phần **frontend hosting**,
- và thường xoá luôn các **backend stack** mà Amplify đã tạo (Cognito, Lambda, DynamoDB, S3),  
  trừ khi bạn chọn giữ lại chúng trong quá trình xoá.

Hãy đọc kỹ nội dung trong hộp thoại xác nhận để tránh xoá nhầm tài nguyên quan trọng.

---

## 5.9.2 – Xoá các backend resource còn sót lại

Tuỳ cách bạn tạo backend, sau khi xoá Amplify app vẫn có thể còn một số tài nguyên tồn tại.  
Trong AWS console, tại đúng Region workshop, hãy kiểm tra các dịch vụ sau:

- **Cognito**  
  - Xoá các **User Pool** hoặc **Identity Pool** được tạo riêng cho workshop.

- **Lambda**  
  - Xoá các Lambda function chỉ phục vụ English Journey (ví dụ: function kiểm tra trình độ, daily reminder, xử lý từ vựng).

- **DynamoDB**  
  - Xoá các bảng DynamoDB chỉ dùng cho dữ liệu workshop (tiến độ học, câu hỏi, từ vựng, …) nếu bạn không còn cần.

- **S3**  
  - Xoá các S3 bucket dùng riêng cho workshop (chứa nội dung bài học, media, file tạm, …).  
  - Cẩn thận không xoá các bucket dùng chung hoặc môi trường production.

- **MediaConvert**  
  - Xoá các **job template**, **queue** hoặc **endpoint** được tạo cho workshop (nếu có).

---

## 5.9.3 – Dọn dẹp SES, CloudWatch và WAF

Ngoài backend chính, workshop còn sử dụng **Amazon SES**, **CloudWatch** và (tuỳ chọn) **AWS WAF**.

### Amazon SES

1. Mở console **Amazon SES**.
2. Trong mục **Verified identities**:
   - Xoá các địa chỉ email (identity) được tạo chỉ để phục vụ workshop (ví dụ: email gửi thử hoặc nhận thử).
3. Trong mục **Configuration sets**:
   - Xoá configuration set dùng cho ứng dụng English Journey (ví dụ: `english-journey-config`), nếu bạn không có ý định tái sử dụng.

Nếu bạn đã yêu cầu **thoát khỏi chế độ sandbox của SES** chỉ để phục vụ workshop, hãy xem lại cách sử dụng và hạn mức gửi email; tuy nhiên không có tài nguyên riêng nào cần xoá cho việc đó.

### CloudWatch

1. Mở console **CloudWatch**.
2. Ở mục **Log groups**, xoá:
   - các log group của Lambda function thuộc English Journey,
   - log group của MediaConvert jobs dùng trong workshop.
3. Ở mục **Alarms**, xoá:
   - các alarm giám sát tài nguyên chỉ dùng cho workshop (Lambda, DynamoDB, SES),
   - các alarm test bạn đã tạo trong quá trình thực hành.

### AWS WAF (nếu có dùng)

Nếu bạn đã cấu hình một **WAF Web ACL** riêng cho frontend của English Journey:

1. Mở console **AWS WAF & Shield**.
2. Tìm **Web ACL** gắn với CloudFront distribution hoặc Amplify app của workshop.
3. Nếu Web ACL này chỉ phục vụ riêng workshop, bạn có thể xoá nó.

---

## 5.9.4 – Dọn dẹp IAM roles và policies

Cuối cùng, hãy rà soát **IAM** để đảm bảo không còn role/policy nào bị “mồ côi”:

1. Trong console **IAM**, vào mục **Roles**:
   - Tìm các role được tạo chỉ cho workshop (ví dụ: các Lambda execution role tuỳ chỉnh, MediaConvert service role, hoặc role có tên chứa English Journey / workshop).
   - Trước khi xoá, hãy kiểm tra chắc chắn không còn Lambda, dịch vụ hay người dùng nào đang dùng role đó.

2. Trong mục **Policies**:
   - Xoá các **customer-managed policy** chỉ phục vụ workshop, đặc biệt là:
     - policy cấp quyền `ses:SendEmail` / `ses:SendRawEmail` cho Lambda,
     - các policy chỉ gắn với những role tạm thời.

> **Không** xoá các IAM role / policy dùng chung hoặc đang phục vụ hệ thống production.

Sau khi hoàn thành các bước trên, môi trường AWS của bạn sẽ không còn các tài nguyên được tạo riêng cho workshop English Journey.


