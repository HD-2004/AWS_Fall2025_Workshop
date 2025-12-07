---
title: "Worklog Tuần 2"
date: 2025-09-16
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---
### Mục tiêu tuần 2:

* Tìm hiểu về dịch vụ mạng AWS.
* Tìm hiểu lý thuyết và thực hành EC2 cơ bản.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Tìm hiểu lý thuyết EC2 cơ bản: <br>&emsp; + Instance types <br>&emsp; + AMI <br>&emsp; + EBS <br> - Các cách remote SSH vào EC2. <br> - Tìm hiểu Elastic IP. <br> - **Thực hành:** <br>&emsp; + Tạo EC2 instance <br>&emsp; + Kết nối SSH <br>&emsp;                                                                                              | 14/09/2025   | 14/09/2025      | <https://000004.awsstudygroup.com/vi>
| 3   | - Tìm hiểu về dịch vụ mạng AWS <br>&emsp; + Amazon Virtual Private Cloud ( VPC ) <br>&emsp; + VPC Peering & Transit Gateway <br>&emsp; + VPN & Direct Connect <br>&emsp; + Elastic Load Balancing <br>&emsp; <br>                                            | 15/09/2025   | 17/09/2025      | <https://www.youtube.com/watch?v=O9Ac_vGHquM&list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i&index=25> |
| 4   | - Tìm hiểu về dịch vụ mạng AWS <br>&emsp; + Amazon Virtual Private Cloud ( VPC ) <br>&emsp; + VPC Peering & Transit Gateway <br>&emsp; + VPN & Direct Connect <br>&emsp; + Elastic Load Balancing <br>&emsp; <br> | 15/09/2025   | 17/09/2025      | <https://000003.awsstudygroup.com/vi> |
| 5   | - **Thực hành:** <br>&emsp; + Cấu hình Site to Site VPN ( VPC ) <br>&emsp; + Triển khai Amazon EC2 Instance <br>&emsp;  <br>                                                                                                 | 16/09/2025   | 17/09/2025      | <https://000003.awsstudygroup.com/vi/1-introduce> |
| 6   | -  Họp nhóm về ý tưởng project và viết worklog                                                                                        | 18/09/2025   | 18/09/2025      |  


### Kết quả đạt được tuần 2:

* Tìm hiểu lý thuyết EC2 cơ bản: 
  * Instance types: các cấu hình phần cứng ảo khác nhau mà AWS cung cấp để chạy máy ảo (EC2 instance). Mỗi loại được tối ưu cho những nhu cầu sử dụng khác nhau
  * AMI: bản mẫu để khởi tạo EC2 instance, gồm hệ điều hành, phần mềm, cấu hình volume.
  * Các cách remote SSH vào EC2: dùng SSH Client với key pair, EC2 Instance Connect qua trình duyệt, Session Manager không cần mở port 22, và PuTTY trên Windows với file .ppk.
  * Tìm hiểu Elastic IP: là một địa chỉ IPv4 tĩnh, cố định, công cộng được gán cho tài khoản AWS và có thể gắn/dỡ cho các EC2 instance, giúp EC2 có địa chỉ IP công cộng cố định, đảm bảo truy cập ổn định từ bên ngoài.
  
* Đã tạo và Kết nối SSH vào EC2 thành công.

* Tìm hiểu về dịch vụ mạng AWS:
  * Amazon Virtual Private Cloud: là dịch vụ mạng ảo tùy chỉnh nằm trong AWS Cloud, cho phép bạn tạo một môi trường mạng riêng biệt và hoàn toàn tách biệt với thế giới bên ngoài
  * VPC Peering & Transit Gateway: VPC Peering kết nối trực tiếp hai VPC bằng IP private, đơn giản nhưng không hỗ trợ định tuyến bắc cầu, còn Transit Gateway là trung tâm kết nối nhiều VPC và on-premises, hỗ trợ định tuyến bắc cầu, mở rộng và quản lý tập trung.
  * VPN & Direct Connect: VPN kết nối mạng on-premises với AWS qua Internet bằng đường hầm mã hóa, dễ triển khai nhưng độ trễ và băng thông phụ thuộc Internet; Direct Connect kết nối chuyên dụng từ on-premises đến AWS, ổn định, băng thông cao, độ trễ thấp nhưng chi phí cao và triển khai lâu hơn.
  * Elastic Load Balancing: là dịch vụ phân phối tự động lưu lượng đến nhiều tài nguyên để tăng tính sẵn sàng, mở rộng và chịu lỗi, gồm ALB (HTTP/HTTPS), NLB (TCP/UDP hiệu năng cao) và GWLB (thiết bị mạng ảo).