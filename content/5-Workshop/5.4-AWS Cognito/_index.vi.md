---
title: "Cấu hình AWS Cognito"
date: 2025-09-10
weight: 4
chapter: false
pre: "  5.4.  "
---

## Mục tiêu

Trong bước này, chúng tôi trình bày cách sử dụng **Amazon Cognito** trong ứng dụng web *English Journey* để quản lý việc xác thực người dùng.  

Người dùng (học viên) có thể đăng nhập bằng:

- **Email & mật khẩu**
- **Tài khoản Google (Gmail)**

Cognito đóng vai trò là **dịch vụ quản lý danh tính trung tâm**, cấp token để frontend và backend của English Journey sử dụng.

---

## 5.4.1 Vai trò của Amazon Cognito trong kiến trúc

Trong kiến trúc của English Journey:

- **Cognito User Pool** lưu trữ thông tin định danh người dùng (email, tên, …).
- Xử lý các chức năng:
  - **Đăng ký (sign up)**
  - **Đăng nhập (sign in)**
  - **Quên mật khẩu / đổi mật khẩu**
  - **Xác thực email**
  - **Lưu trữ người dùng**
  - ...
- Tích hợp với **AWS Amplify**, giúp frontend React gọi các hàm `signIn`, `signUp`, v.v. dễ dàng.
- Kết nối với **các nhà cung cấp đăng nhập xã hội (Social IdP)**:
  - **Google** → cho người dùng đăng nhập bằng Gmail  

Sau khi đăng nhập thành công (bằng email, Google), Cognito trả về **token**, và các token này được dùng để bảo vệ API, Lambda và các tài nguyên phía backend.

---

## 5.4.2 Tạo Cognito User Pool

Các bước cấu hình chính:

1. **Tạo User Pool**
   - Đăng nhập AWS Management Console → **Amazon Cognito** → **User pools** → **Create user pool**.
   - Chọn **Email** là thông tin đăng nhập chính (sign-in identifier).
   - Cho phép **tự đăng ký** (self-registration) để học viên tự tạo tài khoản.

2. **Cấu hình chính sách mật khẩu & xác thực**
   - Thiết lập chính sách mật khẩu cơ bản (độ dài tối thiểu, ký tự đặc biệt, …).
   - Bật **xác thực email** để yêu cầu người dùng xác nhận email trước khi sử dụng đầy đủ chức năng.
   - Có thể tùy chỉnh nội dung email xác thực và email quên mật khẩu (nếu cần).

3. **Tạo app client**
   - Tạo một **public app client** dành cho frontend web.
   - Bật **Cognito User Pool** và **social identity providers (Google)** trong phần allowed identity providers.
   - Cấu hình **callback URLs** và **sign-out URLs** (ví dụ: domain của frontend Amplify của English Journey).

User Pool này sau đó sẽ được tham chiếu trong cấu hình Amplify và sử dụng ở frontend.

---

## 5.4.3 Bật đăng nhập bằng Google (Gmail)

Để hỗ trợ đăng nhập xã hội, chúng tôi thêm **Google** làm identity provider trong Cognito.

### Đăng nhập bằng Google (Gmail)

1. Vào **Google Cloud Console**, tạo **OAuth 2.0 Client ID** cho ứng dụng web.
2. Thiết lập **Authorized redirect URI** trỏ về URL callback của Cognito (được tạo từ User Pool).
3. Lấy **Client ID** và **Client Secret** từ Google.
4. Trong **Cognito → User pool → Identity providers → Google**:
   - Dán Client ID và Client Secret.
   - Map các thuộc tính như email, name của Google sang các thuộc tính chuẩn của Cognito.

Sau khi cấu hình, người dùng có thể bấm **"Sign in with Google"** để đăng nhập bằng tài khoản Gmail của mình.

---

## 5.4.4 Tích hợp Cognito với frontend Amplify

Frontend **React** của English Journey sử dụng **AWS Amplify Auth** để kết nối với Cognito.

Về mặt luồng xử lý:

- Với **email/mật khẩu**:
  - `Auth.signUp()` dùng để đăng ký tài khoản mới.
  - `Auth.signIn()` dùng để đăng nhập thông thường.
- Với **Gmail** (đăng nhập xã hội):
  - Gọi `Auth.federatedSignIn({ provider: 'Google' })`.
  - Amplify sẽ:
    1. Chuyển hướng người dùng sang trang đăng nhập Google.
    2. Người dùng xác thực thành công.
    3. Google trả về Cognito.
    4. Cognito trả về token và chuyển hướng lại frontend của English Journey.


---
**Trên giao diện trang đăng nhập, chúng tôi hiển thị hai lựa chọn chính:**

    Đăng nhập bằng email & mật khẩu

    Đăng nhập bằng Google

    Dù dùng cách nào thì cuối cùng người dùng vẫn nằm trong cùng một Cognito User Pool.

---
## 5.4.5 Bảo mật và quản lý người dùng

**Với Cognito, chúng tôi có thể:**

- Bắt buộc xác thực email trước khi cho phép sử dụng đầy đủ chức năng.

- Kiểm soát domain hợp lệ được phép gọi đăng nhập (qua phần callback URL).

- Quản lý người dùng tập trung:

    - Khóa tài khoản

    - Reset mật khẩu

    - Xóa tài khoản

- Mở rộng sau này với:

    - MFA (Multi-Factor Authentication)
    - Đăng nhập đa dạng nền tảng (Github, Facebook,...)


**Trong phạm vi workshop này, chúng tôi tập trung vào:**

- Đăng nhập bằng email & mật khẩu

- Đăng nhập ứng dụng qua Gmail

- Xác thực email (OTP)

---
## Tóm tắt

**Trong bước này, chúng tôi cấu hình Amazon Cognito làm dịch vụ quản lý danh tính cho English Journey:**

- Tạo User Pool để quản lý tài khoản và xác thực người dùng.

- Cho phép người dùng đăng nhập bằng email, Google (Gmail).

- Frontend React + Amplify sử dụng token từ Cognito để truy cập an toàn đến các API và dịch vụ backend.