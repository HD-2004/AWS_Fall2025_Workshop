---
title: "Event 5"
date: 2025-11-29
weight: 1
chapter: false
pre: " <b> 4.5. </b> "
---

# Bài thu hoạch “AWS Well-Architected Security Pillar”

### Mục Đích Của Sự Kiện

- Hiểu rõ về vai trò của Security Pillar trong AWS Well-Architected Framework: Tìm hiểu các best practices về bảo mật và cách áp dụng chúng trong kiến trúc đám mây, tập trung vào các dịch vụ và công cụ của AWS.
  
- Khám phá các nguyên tắc bảo mật cốt lõi: Tìm hiểu về các nguyên tắc Least Privilege, Zero Trust, và Defense in Depth trong việc triển khai bảo mật cho hệ thống đám mây.
  
- Nhận diện các mối đe dọa bảo mật trong môi trường đám mây tại Việt Nam: Thảo luận về những thách thức bảo mật và các mối đe dọa phổ biến đối với các tổ chức đang vận hành trên nền tảng đám mây tại Việt Nam.
  
- Khám phá các pillar bảo mật trong AWS: Tìm hiểu chi tiết về 5 pillar bảo mật trong AWS gồm Identity & Access Management, Detection, Infrastructure Protection, Data Protection, và Incident Response, và cách thực hiện chúng một cách hiệu quả.


### Nội Dung Nổi Bật

#### Opening & Security Foundation

- Security Pillar trong Well-Architected Framework: Giới thiệu về tầm quan trọng của Security Pillar trong AWS Well-Architected Framework và cách bảo vệ hệ thống trong kiến trúc đám mây.
  
- Các nguyên tắc cốt lõi: Nhấn mạnh các nguyên tắc Least Privilege, Zero Trust, và Defense in Depth là cơ sở để thiết kế các hệ thống bảo mật vững mạnh.
  
- Shared Responsibility Model: Hiểu rõ mô hình chia sẻ trách nhiệm giữa AWS và khách hàng trong việc bảo mật đám mây.
  
- Các mối đe dọa bảo mật phổ biến tại Việt Nam: Phân tích các mối đe dọa bảo mật mà các tổ chức ở Việt Nam gặp phải khi vận hành trên nền tảng đám mây.

#### Pillar 1 — Identity & Access Management

- Kiến trúc IAM hiện đại: Cách sử dụng Identity and Access Management (IAM) trong AWS, bao gồm người dùng, vai trò, chính sách và tầm quan trọng của việc tránh sử dụng thông tin đăng nhập dài hạn.

- IAM Identity Center: Giới thiệu Single Sign-On (SSO) và permission sets để quản lý quyền truy cập xuyên suốt các tài khoản AWS.

- SCP và Permission Boundaries: Sử dụng Service Control Policies (SCP) và permission boundaries để quản lý bảo mật trong môi trường đa tài khoản.

- MFA, quay vòng thông tin xác thực, Access Analyzer: Cách sử dụng Multi-Factor Authentication (MFA), quay vòng thông tin xác thực và Access Analyzer để đảm bảo quyền truy cập an toàn.

- Mini Demo: Xác thực chính sách IAM và mô phỏng quyền truy cập để hiểu cách IAM hoạt động thực tế.

#### Pillar 2 — Detection

- Phát hiện và giám sát liên tục: Giới thiệu các công cụ CloudTrail, GuardDuty, và Security Hub để theo dõi và phát hiện các sự kiện bảo mật trong môi trường AWS.

- Logging ở mọi lớp: Thực hành việc ghi log ở các lớp khác nhau như VPC Flow Logs, ALB/S3 logs để giám sát bảo mật đầy đủ.

- Cảnh báo & Tự động hóa với EventBridge: Cách sử dụng EventBridge để thiết lập cảnh báo và tự động phản hồi sự cố bảo mật.

- Detection-as-Code: Áp dụng Detection-as-Code cho hạ tầng và các quy tắc bảo mật để tự động hóa quá trình phát hiện sự cố.

#### Pillar 3 — Infrastructure Protection

- Bảo mật mạng và Workload: Tìm hiểu về VPC segmentation và cách phân chia giữa private và public placement để bảo vệ lưu lượng mạng.

- Security Groups vs NACLs: So sánh sự khác biệt giữa Security Groups và Network Access Control Lists (NACLs) và áp dụng mô hình nào cho từng trường hợp cụ thể.

- WAF + Shield + Network Firewall: Bảo vệ ứng dụng và mạng bằng các công cụ AWS WAF (Web Application Firewall), Shield, và Network Firewall để giảm thiểu các mối đe dọa.

- Bảo vệ Workload: Các best practices bảo mật cho EC2, ECS và EKS để bảo vệ các workload trong đám mây.

#### Pillar 4 — Data Protection

- Mã hóa, khóa và bí mật: Giới thiệu AWS KMS (Key Management Service) và các chính sách, cấp phép và quay vòng khóa để bảo vệ dữ liệu.

- Mã hóa dữ liệu khi nghỉ và khi truyền: Cách thực hiện mã hóa cho dữ liệu khi nghỉ (e.g., S3, EBS, RDS) và khi truyền (e.g., DynamoDB).

- Quản lý Bí mật: Quản lý các dữ liệu nhạy cảm như mật khẩu, khóa, và mã thông qua Secrets Manager và Parameter Store, và thực hiện quay vòng bí mật.

- Phân loại dữ liệu và các biện pháp bảo vệ quyền truy cập: Áp dụng các biện pháp phân loại dữ liệu và kiểm soát quyền truy cập để bảo vệ dữ liệu.

#### Pillar 5 — Incident Response

- Playbook & Tự động hóa IR: Cách triển khai vòng đời Incident Response (IR) theo AWS và tự động hóa quy trình phản ứng sự cố bằng AWS.

- Playbook IR: Các tình huống sự cố phổ biến như IAM key bị xâm phạm, S3 bị công khai và EC2 bị nhiễm mã độc, và cách giải quyết chúng.

- Phản ứng tự động bằng Lambda/Step Functions: Sử dụng AWS Lambda và Step Functions để tự động hóa các hành động phản ứng sự cố và giảm thiểu thiệt hại nhanh chóng.

#### Tổng kết & Q&A

- Tổng kết 5 pillar: Tóm tắt lại 5 pillar bảo mật và cách áp dụng chúng vào môi trường AWS thực tế.

- Các sai sót phổ biến và thực tế doanh nghiệp Việt Nam: Thảo luận về những thách thức bảo mật mà các doanh nghiệp Việt Nam gặp phải và cách giải quyết chúng bằng các công cụ AWS.

- Lộ trình học bảo mật: Giới thiệu các chứng chỉ Security Specialty và Solutions Architect – Professional (SA Pro) và lộ trình học cho những người muốn chuyên sâu về bảo mật.

### Những Gì Học Được

#### Nguyên Tắc và Pillars Bảo Mật

- Nguyên tắc bảo mật cốt lõi: Hiểu rõ về Least Privilege, Zero Trust, và Defense in Depth trong thiết kế bảo mật hệ thống.

- Các Pillars bảo mật của AWS: Tầm quan trọng của các pillar bảo mật: Identity & Access Management, Detection, Infrastructure Protection, Data Protection, và Incident Response trong việc bảo vệ môi trường AWS.

#### IAM và Quản lý Quyền Truy Cập

- Kiến trúc IAM hiện đại: Cách sử dụng IAM để quản lý quyền truy cập một cách an toàn và hiệu quả, đồng thời tránh sử dụng thông tin đăng nhập dài hạn.

- Bảo mật đa tài khoản: Áp dụng SCP và permission boundaries để quản lý bảo mật cho nhiều tài khoản AWS.

#### Giám sát và Phát hiện Liên Tục

- CloudTrail, GuardDuty, và Security Hub: Cài đặt và sử dụng các công cụ như CloudTrail và GuardDuty để theo dõi hoạt động và phát hiện các sự kiện bảo mật.

- Logging và Tự động hóa: Thực hành các best practices trong việc ghi log và tự động cảnh báo sự cố bảo mật bằng EventBridge.

#### Bảo vệ Dữ liệu

- KMS và Mã hóa: Áp dụng AWS KMS và các phương pháp mã hóa dữ liệu để bảo vệ dữ liệu nhạy cảm.

- Quản lý Bí mật: Sử dụng Secrets Manager và Parameter Store để quản lý bí mật và thực hiện quay vòng tự động.

#### Phản ứng Sự Cố

- Tự động hóa Phản ứng Sự Cố: Cách sử dụng AWS Lambda và Step Functions để tự động hóa các phản ứng sự cố, giảm thiểu thời gian và thiệt hại.

### Ứng Dụng Vào Công Việc

- Quản lý IAM: Áp dụng IAM policies và role-based access control trong tổ chức để đảm bảo quyền truy cập an toàn.

- Giám sát liên tục: Thiết lập CloudTrail, GuardDuty, và Security Hub để giám sát các mối đe dọa bảo mật trong môi trường AWS.

- Tự động hóa phản ứng sự cố: Sử dụng Lambda và Step Functions để tự động hóa các hành động phản ứng sự cố, tăng hiệu quả bảo mật.

- Mã hóa dữ liệu: Thực hiện mã hóa cho data-at-rest và data-in-transit, bảo vệ dữ liệu trong quá trình sử dụng và truyền tải.

### Trải Nghiệm Trong Sự Kiện

- Học hỏi từ các chuyên gia AWS: Sự kiện cung cấp những kiến thức sâu sắc về bảo mật đám mây và cách áp dụng các công cụ bảo mật của AWS.

- Demos thực hành: Các bài demo thực tế về IAM, IR automation, và Security Monitoring giúp tôi hiểu rõ hơn về cách áp dụng bảo mật trong công việc của mình.

- Mạng lưới và chia sẻ kinh nghiệm: Sự kiện tạo cơ hội để kết nối và chia sẻ kiến thức với các chuyên gia và đồng nghiệp trong ngành bảo mật.

### Kết luận

Sự kiện “AWS Well-Architected Security Pillar” đã cung cấp cho tôi những kiến thức quan trọng về các nguyên tắc bảo mật và cách áp dụng AWS tools để bảo vệ hệ thống đám mây. Các pillar bảo mật như IAM, Phát hiện liên tục, Bảo vệ dữ liệu, và Phản ứng sự cố sẽ là những yếu tố quan trọng trong việc triển khai và duy trì bảo mật hệ thống AWS của tôi.