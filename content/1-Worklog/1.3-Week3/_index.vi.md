---
title: "Worklog Tuần 3"
date: 2025-09-16
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

* Tìm hiểu về dịch vụ Compute VM trên AWS.
* Thực hành với dịch vụ Compute VM trên AWS.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Tìm hiểu về dịch vụ Compute VM trên AWS. <br>&emsp; + Amazon Elastic Compute Cloud (EC2) <br>&emsp; + Amazon Lightsail <br>&emsp; + Amazon EFS / FSX <br>&emsp; + AWS Application Migration Service (MGN)                                                                                           | 15/09/2025   | 16/09/2025      | <https://www.youtube.com/watch?v=-t5h4N6vfBs&list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i&index=72>
| 3   | - Tìm hiểu về dịch vụ Compute VM trên AWS. <br>&emsp; + Amazon Elastic Compute Cloud (EC2) <br>&emsp; + Amazon Lightsail <br>&emsp; + Amazon EFS / FSX <br>&emsp; + AWS Application Migration Service (MGN) <br>                                       | 15/09/2025   | 16/09/2025      | <https://www.youtube.com/playlist?list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i> |
| 4   | - **Thực hành:** <br>&emsp; + Triển khai AWS Backup cho hệ thống <br>&emsp; + Triển khai AWS File Storage Gateway <br>&emsp; + Tạo S3 bucket <br>&emsp; + Triển khai Autoscaling Group | 17/09/2025   | 19/09/2025      | <https://000013.awsstudygroup.com/vi> |
| 5   | - **Thực hành:** <br>&emsp; + Triển khai AWS Backup cho hệ thống <br>&emsp; + Triển khai AWS File Storage Gateway <br>&emsp; + Tạo S3 bucket <br>&emsp; + Triển khai Autoscaling Group                 | 17/09/2025   | 19/09/2025      | <https://000024.awsstudygroup.com> |
| 6   | - **Thực hành:** <br>&emsp; + Triển khai AWS Backup cho hệ thống <br>&emsp; + Triển khai AWS File Storage Gateway <br>&emsp; + Tạo S3 bucket<br>&emsp; + Triển khai Autoscaling Group                                                                                    | 17/09/2025   | 19/09/2025      | <https://000024.awsstudygroup.com> |


### Kết quả đạt được tuần 3:

* Hiểu rõ về dịch vụ Compute VM trên AWS: 
  * Amazon Elastic Compute Cloud (EC2): Amazon EC2 giống với máy chủ ảo hoặc máy chủ vật lý truyền thống. EC2 có khả năng khởi tạo nhanh, khả năng co dãn tài nguyên mạnh mẽ, linh hoạt.
  * Amazon Lightsail: là dịch vụ tính toán có chi phí thấp (giá tính theo tháng chỉ bắt đầu từ 3,5 $ / tháng ) ngoài ra mỗi Instance Lightsail tạo ra cũng sẽ có một mức data transfer đi kèm. (data transfer này có mức giá rẻ hơn data transfer từ EC2 tương đối nhiều), phù hợp cho các workload nhẹ , môi trường test dev, không yêu cầu tải CPU cao liên tục > hơn 2 giờ mỗi ngày.
  * Amazon EFS / FSX: 
  + EFS ( Elastic File System ) cho phép tạo các NFSv4 Network volume và gán vào nhiều EC2 Instances cùng lúc, quy mô lưu trữ lên đến hàng petrabyte. EFS chỉ support Linux. Sử dụng EFS chỉ tính chi phí theo dung lượng sử dụng,có thể được cấu hình để mount vào môi trường on-premise qua DX hoặc VPN.
  + FSx cho phép tạo các NTFS volume và gán vào nhiều EC2 Instances cùng lúc sử dụng giao thức SMB (Server Message Block), support Windows và Linux, chỉ tính chi phí theo dung lượng sử dụng
  * AWS Application Migration Service: dùng để migrate và replicate phục vụ mục đích xây dựng Disaster Recovery Site cho các máy chủ thực, ảo lên môi trường AWS, liên tục sao chép các máy chủ nguồn sang EC2 Instance trên tài khoản AWS (asynchronous / synchronous ).

* Thành công triển khai AWS Backup, File Storage Gateway và Autoscaling group.