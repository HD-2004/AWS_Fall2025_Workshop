---
title : "Cấu hình Amazon Route 53"
date: 2025-09-10
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---


## 5.8 Cấu hình Amazon Route 53 (Tên miền riêng)

Bước này sẽ kết nối frontend **English Journey** chạy trên **AWS Amplify**
với một tên miền riêng được quản lý bởi **Amazon Route 53**.

> 🔗 **Tên miền dùng trong workshop**
>
> Trong môi trường demo, ứng dụng sử dụng tên miền  
> **englishjourney.xyz** – trang web cuối cùng truy cập tại:  
> `https://www.englishjourney.xyz/`


---

### 5.8.1 Tạo / kiểm tra hosted zone

1. Mở console **Route 53** → *Hosted zones* → **Create hosted zone**.
2. Nhập tên miền của bạn, ví dụ **englishjourney.xyz**, chọn loại *Public hosted zone*.
3. Route 53 sẽ tạo sẵn các bản ghi **NS** và **SOA** cho zone.
4. Nếu domain đăng ký ở nơi khác, copy các bản ghi **NS** này sang nhà đăng ký domain để trỏ DNS về Route 53.

---

### 5.8.2 Kết nối tên miền trong AWS Amplify

1. Vào console **AWS Amplify** → chọn ứng dụng **English Journey**.
2. Menu bên trái chọn **Domain management** → **Add domain**.
3. Chọn hosted zone **englishjourney.xyz**.
4. Map các đường dẫn:

   - `englishjourney.xyz` → nhánh chính (production)
   - `www.englishjourney.xyz` → redirect về domain gốc

5. Amplify sẽ tự động tạo các bản ghi **A / AAAA** và **CNAME** tương ứng trong Route 53.

---

### 5.8.3 Kiểm tra hoạt động

1. Chờ vài phút để DNS và chứng chỉ SSL được cấp.
2. Mở trình duyệt và truy cập:

   - `https://www.englishjourney.xyz/`

3. Xác nhận trang chủ English Journey hiển thị đúng và chạy bằng HTTPS.
4. Ghi lại URL này trong phần báo cáo / slide như **điểm truy cập chính** của ứng dụng trong workshop.

