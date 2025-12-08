---
title: "Workshop"
date: 2025-09-10
weight: 5
chapter: false
pre: " <b> 5. </b> "
---



# English Journey

#### Tổng quan

**English Journey** là một ứng dụng web sáng tạo, được thiết kế để giúp người dùng học từ vựng tiếng Anh một cách có cấu trúc và tương tác. Nền tảng này sử dụng các dịch vụ của AWS để cung cấp trải nghiệm học tập mượt mà, giúp người dùng theo dõi tiến độ, tham gia vào các nội dung động và nhận phản hồi cá nhân hóa.

#### Các Tính Năng Chính:

**Xác Thực Người Dùng:** Sử dụng Amazon Cognito, người dùng có thể đăng ký, đăng nhập và truy cập tài liệu học tập của mình một cách an toàn.

**Các Mô-đun Học Tương Tác:** Ứng dụng cung cấp nhiều bài học tương tác giúp người dùng mở rộng vốn từ vựng.

**Theo Dõi Tiến Độ:** Người dùng có thể theo dõi tiến độ và sự hoàn thành các bài học từ vựng, với báo cáo chi tiết được tạo ra thông qua AWS Lambda và DynamoDB.

**Thông Báo:** Các thông báo thời gian thực qua SNS sẽ thông báo cho người dùng về các bài học mới, mốc tiến độ, tài khoản hoặc các cập nhật khác.

**Chuyển Đổi Media:** Nếu nền tảng bao gồm nội dung video hoặc âm thanh, AWS MediaConvert sẽ xử lý việc chuyển đổi media để đảm bảo tính tương thích và phát lại mượt mà. (Phát triển trong tương lai)

**Lưu Trữ Nội Dung:** Tất cả tài liệu học tập được lưu trữ an toàn trên Amazon S3 với các quyền truy cập kiểm soát chặt chẽ.

**Bảo Mật Web:** Để bảo vệ nền tảng, AWS WAF giúp bảo vệ ứng dụng khỏi các mối đe dọa phổ biến trên web.

**Giám Sát và Cảnh Báo:** AWS CloudWatch được sử dụng để giám sát hiệu suất của nền tảng, với các cảnh báo được cấu hình cho các vấn đề tiềm ẩn.

#### Các Công Nghệ Được Sử Dụng:

**Frontend:** Được xây dựng bằng các công nghệ web hiện đại, cho phép trải nghiệm người dùng mượt mà và phản hồi nhanh.

**Backend:** Dựa trên các dịch vụ AWS như Lambda và DynamoDB, đảm bảo tính mở rộng và hiệu suất.

**Lưu Trữ:** Tất cả dữ liệu và nội dung media được lưu trữ an toàn trên Amazon S3.

#### Nội dung

1. [Tổng quan về workshop](5.1-Workshop-overview/)
2. [Các bước chuẩn bị](5.2-Prerequiste/)
3. [Create Amplify](5.3-Create%20Amplify/)
4. [MediaConvert](5.4-Create%20MediaConvert/)
5. [Amazon SES để gửi email thông báo](5.5-Create%20SES/)
6. [CloudWatch](5.6-Create%20Cloudwatch/)
7. [IAM Roles - Policies](5.7-Create%20IAM%20Roles-Policies/)
8. [Amazon Route 53](5.8-Route%2053/)
9. [Clean up](5.9-Cleanup/)