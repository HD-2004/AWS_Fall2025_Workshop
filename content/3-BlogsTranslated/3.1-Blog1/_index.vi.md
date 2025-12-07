---
title: "Blog 1"
date: 2025-09-10
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
# Visually build telephony applications with AWS Step Functions
(Xây dựng ứng dụng điện thoại trực quan với AWS Step Functions)
#### Tác giả: Reynaldo Hidalgo | ngày 17 tháng 3 năm 2025 | trong Amazon Chime SDK, Application Services, Architecture, AWS Step Functions, Best Practices, Business Productivity, Contact Center, Technical How-to

Các nhà phát triển phải đối mặt với nhiều thách thức khi xây dựng ứng dụng điện thoại: quản lý phản hồi không thể đoán trước của người dùng, xử lý ngắt kết nối, xử lý dữ liệu đầu vào không chính xác và giải quyết lỗi. Những thách thức này kéo dài chu kỳ phát triển và tạo ra các ứng dụng không ổn định, không đáp ứng được kỳ vọng của người dùng.

Bài viết này trình bày cách Amazon Web Services (AWS) Step Functions, kết hợp với dịch vụ âm thanh Amazon Chime SDK Public Switched Telephone Network (PSTN), mang đến giải pháp để vượt qua những thách thức này.

### Tổng quan giải pháp 

Để minh họa giải pháp của mình, chúng tôi đã xây dựng một ứng dụng điện thoại mẫu cho phép chủ doanh nghiệp quản lý các cuộc gọi của khách hàng thông qua một số điện thoại doanh nghiệp chuyên dụng. Giải pháp này giúp chủ doanh nghiệp nhỏ tách biệt liên lạc cá nhân và liên lạc doanh nghiệp, đồng thời quản lý tất cả các cuộc gọi từ điện thoại hiện có của họ.

Phiên bản beta của ứng dụng mẫu này cung cấp sáu luồng cuộc gọi cốt lõi sau:

1. Trong giờ làm việc: Chuyển hướng các cuộc gọi đến của khách hàng đến chủ doanh nghiệp
2. Ngoài giờ làm việc: Cho phép khách hàng để lại tin nhắn thoại
3. Truy xuất tin nhắn: Cho phép chủ doanh nghiệp truy cập tin nhắn thoại của khách hàng
4. ID người gọi doanh nghiệp: Cho phép chủ doanh nghiệp gọi cho khách hàng bằng số điện thoại doanh nghiệp
5. Lên lịch cuộc gọi: Cho phép chủ doanh nghiệp lên lịch các cuộc gọi cho khách hàng vào thời điểm sau trong ngày
6. Gọi tự động: Tự động khởi tạo các cuộc gọi theo lịch trình giữa chủ doanh nghiệp và khách hàng


Sử dụng Workflow Studio, chúng tôi đã xây dựng quy trình làm việc Step Functions xử lý tất cả sáu luồng cuộc gọi và xử lý các tình huống bất ngờ.


### Nó hoạt động như thế nào 

AWS Step Functions cho phép thiết kế quy trình làm việc trực quan linh hoạt, thông qua các thành phần được xây dựng sẵn và các quy tắc xử lý lỗi. Điều này tạo ra các quy trình làm việc bao gồm các trạng thái dựa trên sự kiện, cho phép nhập, xử lý và xuất các tin nhắn định dạng JavaScript Object Notation (JSON). Dịch vụ âm thanh PSTN hợp lý hóa các ứng dụng điện thoại thông qua phương pháp không máy chủ sử dụng mô hình lập trình yêu cầu/phản hồi. Dịch vụ này gọi các hàm AWS Lambda với các Sự kiện và chờ phản hồi Hành động, cả hai đều ở định dạng JSON được xác định trước. Định dạng JSON chung này cho phép tích hợp liền mạch giữa dịch vụ âm thanh PSTN và Step Functions, dẫn đến việc chúng tôi thiết kế một kiến ​​trúc không máy chủ (Hình 2) cho phép trao đổi tin nhắn JSON hai chiều giữa hai dịch vụ.

### Các thành phần chính:

- eventRouter: Hàm Lambda quản lý trao đổi tin nhắn JSON
- appWorkflow: Các hàm Step triển khai logic luồng cuộc gọi
- actionsQueue: Hàng đợi Amazon Simple Queue Service (Amazon SQS) lưu trữ các hành động phản hồi

### Dòng kiến trúc:

1. Dịch vụ âm thanh PSTN nhận cuộc gọi đến
2. Dịch vụ gửi sự kiện NEW_INBOUND_CALL đến eventRouter
3. eventRouter tạo actionsQueue
4. eventRouter thực thi appWorkflow không đồng bộ với dữ liệu sự kiện
5. eventRouter bắt đầu gửi thông báo dài từ actionsQueue, chờ thông báo hành động tiếp theo
6. appWorkflow xử lý dữ liệu sự kiện định dạng JSON, tính toán hành động tiếp theo
7. appWorkflow xếp hàng hành động tiếp theo bằng API SendMessage của Amazon SQS với mô hình tích hợp Wait for Callback với Mã thông báo tác vụ để dừng quy trình làm việc cho đến khi nhận được cuộc gọi sự kiện tiếp theo
8. eventRouter truy xuất và xóa hành động khỏi actionsQueue
9. eventRouter trả về hành động cho dịch vụ âm thanh PSTN


### Quan sát: 

- Logic mã eventRouter mang tính chung chung và không phụ thuộc vào các lệnh gọi và quy trình làm việc Step Function khác nhau
- eventRouter truy vấn một biến môi trường để xác định quy trình làm việc cần gọi.
Các cặp thể hiện actionsQueue và appWorkflow tồn tại trong suốt thời gian của mỗi lệnh gọi.
- eventRouter chịu trách nhiệm tạo và xóa từng actionsQueue.
- Các thể hiện appWorkflow được eventRouter tạo ra khi bắt đầu mỗi lệnh gọi.
- Các thể hiện appWorkflow hoàn tất việc thực thi khi tất cả các bên liên quan trong cuộc gọi cúp máy.

### Xây dựng ứng dụng điện thoại của bạn: 

#### Điều kiện thực hiện

- Làm quen với việc phát triển quy trình làm việc trong Step Functions Workflow Studio
- Truy cập vào AWS Management Console

### Hướng dẫn triển khai

- Tạo quy trình làm việc Step Functions chuyên dụng cho từng ứng dụng điện thoại
- Thiết kế và triển khai quy trình làm việc bằng Workflow Studio
- Sử dụng loại Standard workflow để đáp ứng thời lượng cuộc gọi kéo dài
- Cập nhật biến môi trường "CallFlowsDIDMap" của hàm eventRouter Lambda để ánh xạ số điện thoại vào quy trình làm việc của chúng Tên Tài nguyên Amazon (ARN)
- Đặt biến quy trình làm việc trong tab Biến trạng thái "Khởi tạo" (Hình 3). Hàm eventRouter tự động đặt "QueueUrl" và việc thêm các biến khác vào đây sẽ loại bỏ nhu cầu lưu trữ ngoài

- Cấu hình quy tắc trạng thái Choice để định tuyến cuộc gọi dựa trên các điều kiện. Quy tắc từ một đến ba (Hình 4) xử lý việc định tuyến cuộc gọi dựa trên hướng đến/đi, nhận dạng chủ sở hữu/khách hàng, trong khi quy tắc mặc định quản lý các tình huống bất ngờ.

- Cấu hình trạng thái SQS: SendMessage (Hình 5) để hướng dẫn hành động tiếp theo cho dịch vụ âm thanh PSTN bằng cách:
Định dạng nội dung tin nhắn để phù hợp với supported actions cho dịch vụ âm thanh PSTN
Thiết lập TransactionAttributes để truyền qua lại các giá trị của “WaitToken” và “QueueUrl” trong suốt thời gian cuộc gọi
Kích hoạt mẫu tích hợp Chờ gọi lại với Mã thông báo tác vụ

- Tận dụng trạng thái tích hợp dịch vụ AWS để tương tác với các dịch vụ AWS khác trực tiếp từ quy trình làm việc.

Ví dụ: Sử dụng trạng thái PutItem của DynamoDB (Hình 6) để lưu trữ các tệp ghi Amazon Simple Storage Service (Amazon S3), bao gồm tên và khóa bucket, trong Amazon DynamoDB.

- Sử dụng biểu thức JSONata  (Hình 7) để giảm thiểu số lượng hàm Lambda.

Ví dụ: Đối với việc lập lịch Amazon EventBridge, hãy tính toán biểu thức thời gian bằng các hàm JSONata [$fromMillis(), $millis(), number()] và nối chuỗi để xử lý việc lập lịch cuộc gọi của khách hàng.

### Lợi ích chính

Phương pháp này để xây dựng các ứng dụng điện thoại mang lại nhiều lợi thế:

1. Trình thiết kế dựa trên quy trình làm việc trực quan
2. Logic luồng cuộc gọi tự ghi lại
3. Quản lý phiên bản và xuất bản
4. Tích hợp gốc với Dịch vụ AWS
5. Nhật ký trực quan và kiểm tra cho mỗi cuộc gọi
6. Tự động mở rộng
7. Giá theo mức sử dụng

### Triển khai giải pháp

Các bước sau đây cho phép bạn triển khai ứng dụng điện thoại mẫu cùng với kiến ​​trúc không máy chủ (Hình 2).

#### Điều kiện thực hiện

1. Truy cập AWS Management Console 
2. Cài đặt Node.js và npm 
3. Cài đặt và cấu hình AWS Command Line Interface (AWS CLI) 

#### Hướng dẫn:
Dự án Cloud Development Kit (CDK) trên kho lưu trữ GitHub của AWS sẽ triển khai các tài nguyên sau:

- phoneNumberBusiness – Số điện thoại được cung cấp cho ứng dụng mẫu
- sipMediaApp – Ứng dụng SIP media định tuyến cuộc gọi đến - lambdaProcessPSTNAudioServiceCalls
- sipRule – Quy tắc SIP chuyển hướng cuộc gọi từ phoneNumberBusiness đến sipMediaApp.
- stepfunctionBusinessProxyWorkflow – Quy trình làm việc Step Functions cho ứng dụng - mẫu
- roleStepfuntionBusinessProxyWorkflow – Vai trò IAM cho - stepfunctionBusinessProxyWorkflow
- lambdaProcessPSTNAudioServiceCalls – Hàm Lambda để xử lý cuộc gọi
- roleLambdaProcessPSTNAudioServiceCalls – Vai trò IAM cho - lambdaProcessPSTNAudioServiceCalls
- dynamoDBTableBusinessVoicemails – Bảng DynamoDB để lưu trữ thư thoại của khách hàng
- s3BucketApp – Thùng S3 để lưu trữ bản ghi âm hệ thống và thư thoại của khách hàng
- s3BucketPolicy – ​​Chính sách IAM cấp quyền truy cập dịch vụ âm thanh PSTN cho - s3BucketApp
- lambdaOutboundCall – Hàm Lambda để đặt lịch gọi cho khách hàng
- roleLambdaOutboundCall – Vai trò IAM cho lambdaOutboundCall
- roleEventBridgeLambdaCall – Vai trò IAM cho phép dịch vụ EventBridge thực thi lambdaOutboundCall

### Thực hiện theo các bước sau để triển khai ngăn xếp CDK:
1. Sao chép kho lưu trữ (Clone the repository)

git clone https://github.com/aws-samples/amazon-chime-sdk-visual-media-applications 
cd amazon-chime-sdk-visual-media-applications 
npm install

2. Khởi động lại ngăn xếp (Bootstrap the stack)

#default AWS CLI credentials are used, otherwise use the –-profile parameter
#provide the <account-id> and <region> to deploy this stack 
cdk bootstrap aws://<account-id>/<region>

3. Triển khai ngăn xếp 

#default AWS CLI credentials are used, otherwise use the –-profile parameter
#personalNumber: the personal phone number of the business owner in E.164 format 
#businessAreaCode: the United States area code used to provision the business number 
cdk deploy –-context personalNumber=+1NPAXXXXXXX –-context businessAreaCode=NPA

Gọi đến số điện thoại được cung cấp để kiểm tra ứng dụng mẫu. Tùy chọn, chỉnh sửa quy trình làm việc để cập nhật tên doanh nghiệp và giờ làm việc trên trạng thái Nhiệm vụ "Khởi tạo" trong tab Biến.

### Dọn dẹp tài nguyên 
Để dọn dẹp bản thử nghiệm này, khai báo: 

cdk destroy

Blog này trình bày cách kết hợp AWS Step Functions và dịch vụ âm thanh PSTN Amazon Chime SDK giúp đơn giản hóa quá trình phát triển các ứng dụng điện thoại đáng tin cậy thông qua thiết kế quy trình làm việc trực quan và quản lý lỗi. Chúng tôi đã cung cấp một ứng dụng mẫu, triển khai sáu tính năng cốt lõi của điện thoại doanh nghiệp, minh họa cách giải pháp này quản lý hiệu quả nhiều đường dẫn có điều kiện và các trường hợp ngoại lệ như ngắt kết nối và đầu vào không hợp lệ.
Kiến trúc không máy chủ được tạo ra cho phép tích hợp liền mạch giữa hai dịch vụ thông qua giao tiếp dựa trên JSON, đồng thời cung cấp khả năng tự động mở rộng quy mô và tính phí theo mức sử dụng. Cùng nhau, các thành phần này tạo nên một nền tảng vững chắc để xây dựng các ứng dụng điện thoại tinh vi, giúp giảm chi phí bảo trì và nâng cao độ tin cậy.

TAGS: best practices, customer engagement, Messaging and Targeting
#### Về tác giả

### Reynaldo Hidalgo

Reynaldo là Arquitecto de Soluciones en la Nube en AWS, với hơn 20 năm trải nghiệm và giải mã phần mềm, cơ sở dữ liệu và trí tuệ doanh nghiệp, cơ sở hạ tầng của trung tâm cuộc gọi/điện thoại và ứng dụng tại thời điểm hiện tại. Đồng thời đồng tài trợ cho PrimeVoiX, một công ty khởi nghiệp về giải pháp trung tâm liên lạc quốc tế.