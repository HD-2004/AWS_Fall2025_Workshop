---
title: "Bản đề xuất"
date: 2025-09-16
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
Tại phần này, bạn cần tóm tắt các nội dung trong workshop mà bạn **dự tính** sẽ làm.

# Studying English Website   

### 1. Tóm tắt điều hành  
Studying English Website được thiết kế dành cho các bạn học tiếng anh nhằm nâng cao khả năng học từ vựng, ngữ pháp và giao tiếp hằng ngày. Nền tảng tận dụng các dịch vụ AWS Serverless để cung cấp giám sát thời gian học tập, phân tích dự đoán khả năng học người học để đưa ra những chính sách học tập theo trình từ cơ bản đến nâng cao và tiết kiệm chi phí ở mức thấp nhất. 

### 2. Tuyên bố vấn đề  
*Vấn đề hiện tại*  
Tiếng anh là ngoại ngữ thiết yếu cho công việc và đời sống. Tuy nhiên, người học đang không có không gian và môi trường luyện tập, đặc biệt là trong việc giao tiếp. 

*Giải pháp*
Để giải quyết vấn đề thiếu môi trường luyện tập tiếng Anh và hỗ trợ người học nâng cao kỹ năng từ vựng, ngữ pháp và giao tiếp, chúng tôi đề xuất xây dựng Studying English Website trên nền tảng serverless của AWS, cho phép học tập cá nhân hóa dựa trên dữ liệu người dùng, tích hợp bài tập nghe – nói và video hướng dẫn với ghi âm người học lưu trữ trên S3, theo dõi và phân tích tiến trình học bằng AWS Lambda , đảm bảo bảo mật và quản lý người dùng qua Cognito đồng thời triển khai giao diện web nhanh chóng, tiết kiệm chi phí bằng AWS Amplify, mang đến môi trường học linh hoạt, an toàn và hiệu quả, đồng thời giúp nhà quản lý cải thiện phương pháp học dựa trên dữ liệu thực tế. 

*Lợi ích và hoàn vốn đầu tư (ROI)* 
Nền tảng Studying English Website giúp người học nâng cao kỹ năng tiếng Anh một cách cá nhân hóa và linh hoạt, giảm thời gian và chi phí so với phương pháp học truyền thống, đồng thời cung cấp dữ liệu phân tích tiến trình học cho nhà quản lý để tối ưu phương pháp giảng dạy; với chi phí hạ tầng AWS thấp (~6,45 USD/tháng), dự án có khả năng hoàn vốn nhanh thông qua việc tăng hiệu quả học tập và mở rộng số lượng người dùng, đồng thời tạo nền tảng dữ liệu giá trị cho các dự án AI và phân tích lâu dài.

### 3. Kiến trúc giải pháp  
Kiến trúc giải pháp của Studying English Website dựa trên nền tảng serverless của AWS, sử dụng S3 để lưu trữ dữ liệu thô và dữ liệu đã xử lý, Amplify Gen 2 để triển khai giao diện web Route53 quản lý DNS và định tuyến, Cognito xác thực và quản lý người dùng, Secrets Manager bảo mật thông tin nhạy cảm, IAM quản lý quyền truy cập, Lambda xử lý logic serverless theo sự kiện, WAF bảo vệ ứng dụng khỏi tấn công tạo ra một hệ thống học tiếng Anh linh hoạt, cá nhân hóa, an toàn và dễ mở rộng.

![Studying English Website Architecture](/images/2-Proposal/architecture.jpeg)

*Dịch vụ AWS sử dụng*  
- *AWS Amplify gen 2*: Lưu trữ giao diện web  
- *AWS Route53*: Quản lý DNS và định tuyến. 
- *AWS Cognitor*: Xác thực và quản lý người dùng.
- *AWS IAM*: Quản lý quyền truy cập AWS.
- *AWS Lambda*: Chạy code serverless theo sự kiện.
- *AWS WAF*: Bảo vệ ứng dụng web khỏi tấn công. 

*Thiết kế thành phần*
- *Tiếp nhận dữ liệu*: Dữ liệu từ người dùng và các nguồn được gửi tới AWS Lambda, Lambda nhận và kích hoạt các quy trình xử lý.  
- *Lưu trữ dữ liệu*: Dữ liệu thô và dữ liệu đã xử lý được lưu trữ trên AWS S3 với nhiều bucket riêng biệt, tạo data lake và kho dữ liệu sẵn sàng phân tích.
- *Xử lý dữ liệu*: AWS Lambda xử lý các sự kiện serverless, MediaConvert chuyển đổi video/audio, dữ liệu được lập chỉ mục.
- *Giao diện web*: AWS Amplify Gen 2 lưu trữ ứng dụng Next.js cung cấp bảng điều khiển, phân tích thời gian thực và truy cập dữ liệu người dùng.
- *Quản lý người dùng8: Amazon Cognito xác thực và quản lý quyền truy cập người dùng, kết hợp AWS IAM kiểm soát quyền truy cập dịch vụ AWS, bảo vệ thông tin nhạy cảm qua AWS Secrets Manager và bảo vệ toàn bộ ứng dụng bằng AWS WAF; DNS và định tuyến được quản lý bởi Route53.

### 4. Triển khai kỹ thuật  
*Các giai đoạn triển khai*  
Dự án gồm 2 phần — xây dựng nền tảng Studying English Website — mỗi phần trải qua 4 giai đoạn:  
1. *Nghiên cứu và vẽ kiến trúc*: Nghiên cứu và thiết kế kiến trúc AWS Serverless (1 tháng trước kỳ thực tập).  
2. *Tính toán chi phí và kiểm tra tính khả thi*: Sử dụng AWS Pricing Calculator để ước tính chi phí hạ tầng và điều chỉnh dịch vụ, đảm bảo dự án vừa khả thi vừa tiết kiệm (Tháng 1).  
3. *Điều chỉnh kiến trúc để tối ưu chi phí/giải pháp*: Tinh chỉnh các dịch vụ (ví dụ tối ưu Lambda, MediaConvert, Amplify) và quy trình xử lý dữ liệu để đạt hiệu quả tối đa (Tháng 2).  
4. *Phát triển, kiểm thử, triển khai*: triển khai các dịch vụ AWS bằng CDK/SDK, phát triển giao diện Next.js trên Amplify, kiểm thử toàn bộ hệ thống và đưa vào vận hành (Tháng 2–3).  

*Yêu cầu kỹ thuật* 
- Hệ thống yêu cầu kết nối internet ổn định để vận hành các dịch vụ AWS, bao gồm lưu trữ và truy xuất dữ liệu trên S3, xử lý dữ liệu serverless bằng Lambda, triển khai giao diện web Next.js trên Amplify Gen 2, quản lý DNS và định tuyến bằng Route53, xác thực và quản lý quyền truy cập người dùng với Cognito, bảo mật thông tin nhạy cảm qua Secrets Manager, kiểm soát quyền truy cập dịch vụ AWS bằng IAM và bảo vệ ứng dụng bằng WAF, đồng thời hỗ trợ phân tích dữ liệu và bảng điều khiển thời gian thực. 

### 5. Lộ trình & Mốc triển khai  
- *Trước thực tập (Tháng 0)*: lên kế hoạch học tập  
- *Thực tập (Tháng 1–3)*:  
    - Tháng 1: Học AWS và nâng cấp phần cứng.  
    - Tháng 2: Học cách triển khai, lên kế hoạch và vẽ kiến trúc  
    - Tháng 3: Triển khai, kiểm thử và đưa vào sử dụng   
- *Sau triển khai*: Nghiên cứu tiềm năng phát triển và chức năng mới cho chương trình 
Ước tính ngân sách  

### 6. Ước tính ngân sách  
Có thể xem chi phí trên [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=6a8b0970827e19a863c2bfc704a13b136f700574)  
Hoặc tải [tệp ước tính ngân sách](../attachments/budget_estimation.pdf).  

*Chi phí hạ tầng*  
- AWS S3: 0,15 USD/tháng (6 GB, 2 bucket, 2.100 request)
- AWS Amplify gen 2: 0,35 USD/tháng (256 MB, request 500 ms)  
- AWS Route53: 0,50 USD/tháng (1 domain, 1 triệu query)  
- AWS Cognito: 0,00 USD/tháng (5 người dùng Free tier) 
- AWS IAM: 0 USD/tháng
- AWS Lambda: 0,00 USD/tháng (1.000 request, 512 MB RAM)
- AWS WAF: 5,00 USD/tháng (1 Web ACL cơ bản)

*Tổng*: 5.85 USD/tháng, ~70.2 USD/12 tháng    

### 7. Đánh giá rủi ro  
*Ma trận rủi ro*  
- Sập server: Ảnh hưởng cao, xác xuất trung bình  
- Vượt ngân sách: Ảnh hưởng trung bình, xác suất cao   

*Chiến lược giảm thiểu*  
- Chỉ phí: sử dụng AWS Budget để cảnh báo, tối ưu dịch vụ 

*Kế hoạch dự phòng*  
- Quay lại thu thập thủ công nếu AWS gặp sự cố.  

### 8. Kết quả kỳ vọng  
*Cải tiến kỹ thuật*: Dữ liệu và phân tích thời gian thực thay thế quy trình thủ công. Có thể mở rộng tới 10–15 trạm.  
*Giá trị dài hạn*: Nền tảng dữ liệu 1 năm cho nghiên cứu AI, có thể tái sử dụng cho các dự án tương lai.