---
title : "Cấu hình Amazon SES để gửi email thông báo"
date: 2025-09-10
weight : 5 
chapter : false
pre : " <b> 5.5. </b> "
---


Trong bước này, chúng ta sẽ thay kênh thông báo của ứng dụng **English Journey** từ Amazon SNS sang **Amazon Simple Email Service (SES)**.

SES sẽ được dùng để gửi các email giao dịch cho học viên, ví dụ:

- Email chào mừng khi đăng nhập lần đầu  
- Nhắc học hàng ngày / hàng tuần  
- Kết quả bài kiểm tra trình độ, hoặc thông báo có bài học mới  

> **Lưu ý:** SES là dịch vụ theo vùng (Region). Hãy đảm bảo bạn đang thao tác đúng Region đã dùng cho Amplify, Lambda và DynamoDB.

---

## 5.5.1 – Mở Amazon SES Console

1. Từ AWS Management Console, gõ **“SES”** trong ô tìm kiếm.  
2. Chọn **Amazon Simple Email Service**.  
3. Kiểm tra **Region** ở góc trên bên phải (ví dụ: `ap-southeast-1`).  
4. Nếu SES đang ở Region khác, hãy chuyển về Region của môi trường workshop.

---

## 5.5.2 – Xác thực địa chỉ email (Verified identity)

Trong workshop này, chúng ta sẽ xác thực **một địa chỉ email** và dùng nó làm địa chỉ gửi (sender), ví dụ: email cá nhân của bạn.

1. Ở menu bên trái của SES, chọn **Verified identities**.  
2. Nhấn **Create identity**.  
3. Chọn **Email address**.  
4. Trong ô **Email address**, nhập địa chỉ email bạn muốn dùng, ví dụ:  
    `your-name+english-journey@gmail.com`.
5. Giữ các tùy chọn khác mặc định và nhấn **Create identity**.  
6. SES sẽ gửi một email xác thực tới địa chỉ này.  
7. Vào hộp thư, tìm email từ **Amazon Web Services** và bấm vào **link xác thực**.  
8. Quay lại SES console và bấm **Refresh**. Trạng thái của identity phải chuyển sang **Verified**.

Từ thời điểm này, vì tài khoản vẫn đang ở **SES Sandbox**, SES chỉ cho phép gửi email **từ** và **đến** các địa chỉ đã được xác thực. Điều này là đủ cho môi trường học / workshop.

---

## 5.5.3 – (Tùy chọn) Yêu cầu chuyển khỏi chế độ Sandbox

Nếu sau này bạn muốn gửi email tới người học thật (địa chỉ chưa verified), bạn cần đưa tài khoản SES ra khỏi chế độ sandbox:

1. Trong trang SES, chọn **Account dashboard**.  
2. Tại **Your account details**, kiểm tra **Account status**.  
3. Nếu vẫn là **Sandbox**, nhấn **Request production access** và làm theo hướng dẫn.

> Trong phạm vi workshop, bạn có thể ở chế độ sandbox miễn là chỉ gửi email giữa các địa chỉ đã xác thực.

---

## 5.5.4 – Tạo Configuration Set (không bắt buộc, nhưng nên làm)

Configuration set giúp nhóm toàn bộ email của ứng dụng English Journey lại với nhau và dễ dàng bật các tính năng theo dõi (CloudWatch metrics, event publishing, …) sau này.

1. Trong menu SES, chọn **Configuration sets**.  
2. Nhấn **Create configuration set**.  
3. Đặt tên, ví dụ: `english-journey-config`.  
4. Giữ nguyên các tùy chọn mặc định và nhấn **Create configuration set**.

Chúng ta sẽ sử dụng configuration set này khi gửi email từ các hàm Lambda.

---

## 5.5.5 – Cấp quyền cho Lambda gửi email bằng SES

Các Lambda như **Daily Check-in** hoặc **Test Level result** sẽ gửi email thông qua SES.  
Vì vậy IAM role của Lambda phải được cấp quyền gọi API SES.

Quyền này sẽ được cấu hình chi tiết ở **Mục 5.7 – Tạo IAM Roles & Policies**, nhưng bạn có thể tham khảo mẫu policy dưới đây:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ses:SendEmail",
        "ses:SendRawEmail"
      ],
      "Resource": "*"
    }
  ]
}
