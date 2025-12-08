---
title : "Cấu hình Amazon Route 53"
date: 2025-09-10
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---

## Mục tiêu

Phần này giải thích cách sử dụng **Amazon Route 53** trong kiến trúc English Journey
để cung cấp **tên miền riêng (custom domain)** cho ứng dụng web và định tuyến lưu lượng
tới frontend được host bởi Amplify và bảo vệ bởi AWS WAF.

---

## 5.8.1 Vai trò của Route 53 trong kiến trúc

Trong sơ đồ tổng thể, Route 53 nằm giữa **người dùng cuối** và **frontend**:

- Ứng dụng được host bằng **AWS Amplify** (bên dưới là S3 + CloudFront).
- Ta sử dụng hoặc đăng ký một **tên miền** như `englishjourney.example.com`.
- **Route 53** quản lý DNS cho tên miền này và tạo **bản ghi Alias**
  trỏ tới ứng dụng Amplify (hoặc CloudFront distribution phía sau).
- Tên miền này cũng có thể gắn với **AWS WAF** để lọc request độc hại
  trước khi vào ứng dụng.

Nhờ Route 53, người dùng truy cập qua URL thân thiện thay vì URL mặc định của Amplify.

---

## 5.8.2 Hosted zone và tên miền

Để dùng Route 53, cần có một **public hosted zone** cho domain.

Có hai trường hợp:

1. **Đăng ký domain mới trên Route 53**
   - Ví dụ: `englishjourney.workshop.com`.
   - Route 53 tự tạo hosted zone với record NS và SOA.

2. **Dùng domain đã có ở registrar khác**
   - Tạo một **public hosted zone** trong Route 53 với đúng tên domain.
   - Vào trang quản lý domain ở registrar và chỉnh name server
     sang 4 record NS mà Route 53 cung cấp.

Dù theo cách nào, English Journey cũng dùng hosted zone này để tạo record DNS cho ứng dụng.

---

## 5.8.3 Gắn Amplify App với custom domain

Khi đã có domain / hosted zone:

1. Mở **AWS Amplify console** của ứng dụng English Journey.
2. Chọn **Domain management → Add domain**.
3. Nhập domain do Route 53 quản lý  
   (ví dụ `englishjourney.example.com`).
4. Amplify sẽ đề xuất các **subdomain**:
   - `englishjourney.example.com` → branch production,
   - `dev.englishjourney.example.com` → branch development (tuỳ chọn).
5. Chọn branch muốn map và xác nhận.

Amplify sẽ tự động: 

- tạo các bản ghi **A/AAAA Alias** tương ứng trong Route 53,
- yêu cầu hoặc gắn **chứng chỉ HTTPS** từ AWS Certificate Manager,
- liên kết domain với CloudFront distribution phía sau.

Từ thời điểm này, người dùng có thể truy cập bằng domain riêng
thay vì đường dẫn mặc định của Amplify.

---

## 5.8.4 Ví dụ bản ghi DNS trong Route 53

Trong hosted zone, thường sẽ có các record:

- `A` (Alias) – `englishjourney.example.com` → CloudFront / Amplify domain
- `AAAA` (Alias) – bản IPv6 tương đương (tuỳ chọn)
- `CNAME` – `www.englishjourney.example.com` → `englishjourney.example.com` (tuỳ chọn)
- Các record `NS` và `SOA` mặc định của hosted zone.

Trong workshop, phần lớn các record này do Amplify tạo tự động,
nhưng chúng xuất hiện trong Route 53 để toàn bộ DNS được quản lý ngay trong tài khoản AWS của dự án.

---

## 5.8.5 Tích hợp với AWS WAF

Nếu kiến trúc có dùng **AWS WAF** (như trên sơ đồ):

- WAF được gắn với **CloudFront distribution** phía sau Amplify.
- Route 53 định tuyến domain về CloudFront qua bản ghi Alias,
  nên toàn bộ lưu lượng tới domain đều đi qua WAF trước.
- WAF có thể chặn các tấn công phổ biến (SQL injection, XSS, bot xấu,…)
  trước khi request vào ứng dụng.

Bản thân Route 53 không kiểm tra nội dung request,  
nhưng là điểm vào giúp kết nối: **Custom domain → CloudFront/WAF → Amplify**.

---

## 5.8.6 Tóm tắt

Route 53 mang lại:

- **URL dễ nhớ** cho website English Journey,
- Quyền **kiểm soát DNS** ngay trong cùng tài khoản AWS với các dịch vụ khác,
- Tích hợp trơn tru với **Amplify, CloudFront, ACM và WAF**.

Kết hợp với Amplify, đây là cách cấu hình gần giống môi trường production
cho một ứng dụng web hiện đại trong workshop của bạn.
