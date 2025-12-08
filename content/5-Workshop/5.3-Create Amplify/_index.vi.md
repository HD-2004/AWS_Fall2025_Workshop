---
title : "Tạo backend với Amplify"
date: 2025-09-10
weight : 3 
chapter : false
pre : " <b> 5.3. </b> "
---

## Mục tiêu

Phần này mô tả **quy trình tạo backend bằng AWS Amplify** cho ứng dụng web *English Journey*.

Thay vì tự tay cấu hình từng dịch vụ như Amazon Cognito, AWS Lambda, Amazon S3, AWS WAF hay Amazon DynamoDB trên console, nhóm sử dụng **AWS Amplify (Gen 2)** như một lớp *nền tảng backend*. Amplify đọc cấu hình từ project và sinh ra các resource cần thiết cho xác thực, API, lưu trữ dữ liệu và hosting.

---

## 5.3.1 Vì sao dùng Amplify?

English Journey là ứng dụng **React single-page**. Nhóm chọn Amplify vì:

- Cung cấp **một điểm trung tâm** để cấu hình backend cho web/mobile,
- Tự động tạo **Cognito, Lambda, DynamoDB, S3, CloudFront** dựa trên cấu hình đơn giản,
- Dễ tích hợp với **thư viện Amplify cho JavaScript** mà frontend đang sử dụng (đăng nhập, gọi API, upload file,…).

Nói cách khác, Amplify đóng vai trò “nền tảng backend”, giúp sinh viên không phải thao tác nhiều với từng dịch vụ AWS ở mức thấp.

---

## 5.3.2 Tạo Amplify App và hosting frontend

1. Nhóm tạo một **Amplify App** mới từ AWS Management Console và kết nối với repository chứa mã nguồn React của English Journey.
2. Trong wizard cấu hình:
   - Bước **build** (cài dependency và chạy `npm run build`),
   - Các **biến môi trường** cần cho ứng dụng.
3. Amplify tự động tạo:
   - Một **bucket S3** để lưu các file build của frontend,
   - Một **CloudFront distribution** đứng trước bucket để phân phối website với độ trễ thấp.
4. Kết quả là có một URL public nơi frontend của English Journey được host. Các tài nguyên backend (Cognito, Lambda, DynamoDB, …) về sau đều gắn với Amplify App này.

---

## 5.3.3 Xác thực người dùng với Cognito (Amplify Auth)

Để hỗ trợ người học đăng ký, đăng nhập và quên mật khẩu, nhóm sử dụng **Auth** trong Amplify.

Về mặt cấu hình:

1. Khai báo nhu cầu xác thực trong Amplify:
   - Đăng nhập bằng **email + mật khẩu**,
   - Cho phép **tự đăng ký** tài khoản mới,
   - Cấu hình chính sách mật khẩu và email xác nhận.
2. Amplify sinh ra một **Amazon Cognito User Pool** với cấu hình tương ứng.
3. Frontend React dùng **Amplify Auth library** để:
   - Đăng ký tài khoản (sign up),
   - Đăng nhập (sign in),
   - Đọc thông tin user (name, email) và hiển thị lời chào “Chào mừng trở lại, **tên User**!”.

Token do Cognito cấp (ID token, access token) được dùng để bảo vệ các API và Lambda phía sau.

---

## 5.3.4 Xử lý nghiệp vụ bằng Lambda (Amplify Functions)

Phần nghiệp vụ chính của English Journey chạy trong **AWS Lambda**.  
Nhóm khai báo nhiều function trong Amplify, tiêu biểu như:

- **MyLearning / DailyCheckIn** – cập nhật chuỗi ngày học và tiến độ học cho từng user.
- **LevelTest** – nhận đáp án bài test, tính toán mức CEFR (A1–C1) và lưu kết quả.
- **Dictionary / Vocabulary** – cung cấp API tra từ, lưu “từ đã lưu”.

Trong cấu hình Amplify, các function này được định nghĩa dưới dạng backend handler. Khi deploy, Amplify tạo từng **Lambda function** riêng kèm:

- IAM Role phù hợp (quyền truy cập DynamoDB, SES,…),
- Biến môi trường (tên bảng, ARN của các dịch vụ liên quan).

---

## 5.3.5 Tầng dữ liệu với DynamoDB

Để lưu dữ liệu của ứng dụng, nhóm định nghĩa các model trong backend và để Amplify map sang **bảng Amazon DynamoDB**, ví dụ:

- **UserProfiles** – thông tin cơ bản và tùy chọn học tập của người dùng.
- **PlacementTestResults** – điểm và level phát hiện được cho từng lần làm bài test.
- **Vocabulary / Dictionary** – danh sách từ vựng, nghĩa tiếng Việt, ví dụ, level CEFR.
- **UserProgress** – từ đã lưu, từ đã đánh dấu “mastered”, lịch sử quiz, daily streak.

Các Lambda function nhận tên bảng qua biến môi trường do Amplify sinh ra và truy cập DynamoDB bằng AWS SDK.  
Việc mô tả bảng trong code giúp quá trình deploy có thể lặp lại và dễ quản lý phiên bản.

---


## 5.3.6 Bảo vệ lớp frontend với AWS WAF

Điểm truy cập public của ứng dụng là **CloudFront distribution** do Amplify tạo ra.  
Để bảo vệ endpoint này, nhóm gắn một **AWS WAF Web ACL** vào CloudFront và bật:

- Các **AWS managed rule group** chặn tấn công phổ biến (SQL injection, XSS, bot,…),
- Một rule giới hạn tần suất request cơ bản nhằm giảm nguy cơ bị spam/DoS đơn giản.

Trên sơ đồ kiến trúc, phần này được thể hiện bởi khối **AWS WAF** đứng trước Amplify.

---

## Tóm tắt

Trong dự án English Journey, **Amplify** là dịch vụ trung tâm dùng để:

- Tạo và kết nối **Cognito** (xác thực),
- Triển khai **Lambda** (xử lý nghiệp vụ),
- Quản lý **DynamoDB** (dữ liệu ứng dụng),
- Cấu hình **S3 + CloudFront** (hosting và nội dung tĩnh),
- Kết hợp với **AWS WAF** để tăng cường bảo mật.

