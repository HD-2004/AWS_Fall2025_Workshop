---
title: "Blog 2"
date: 2025-09-10
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
# Visualize and gain insights into your VPC with Amazon Q in Amazon QuickSight
(Trực quan hóa và khai thác thông tin về VPC của bạn với Amazon Q trong Amazon QuickSight)

Tác giả: Diego Hernandez, Opeoluwa Victor Babasanmi, và Rashmiman Ray | ngày 27 tháng 2 năm 2025 | trong Amazon Q, Amazon QuickSight, Amazon VPC, Analytics, Networking & Content Delivery 


### Giới thiệu

Các dịch vụ của AWS tạo ra lượng dữ liệu phong phú dưới dạng log và metric, cho phép bạn xây dựng các bảng điều khiển (dashboard) toàn diện để khai thác những thông tin chuyên sâu có giá trị, bao gồm khả năng quan sát chi tiết các mẫu kết nối trong Virtual Private Cloud (VPC). Bài viết này minh họa cách Amazon QuickSight và Amazon Q trong QuickSight cho phép trực quan hóa dữ liệu từ bất kỳ nguồn nào. Chúng ta sẽ tập trung vào việc trực quan hóa các mẫu kết nối trong VPC, nhằm làm nổi bật lợi ích của QuickSight cho người dùng ở nhiều mức độ kỹ năng kỹ thuật khác nhau.

Amazon QuickSight là dịch vụ phân tích dữ liệu và trí tuệ doanh nghiệp (BI) được quản lý hoàn toàn và có khả năng mở rộng trên đám mây, giúp người dùng tạo và chia sẻ dashboard tương tác, phân tích dữ liệu, và rút ra thông tin chi tiết thông qua trực quan hóa. Vào ngày 30 tháng 4 năm 2024, AWS đã công bố tính khả dụng chung (General Availability) của Amazon Q trong QuickSight, một tính năng mở rộng QuickSight bằng cách tích hợp AI sinh (generative AI) để trực quan hóa dữ liệu thông qua truy vấn ngôn ngữ tự nhiên.

Trong các phần tiếp theo, chúng ta sẽ khám phá hai trường hợp sử dụng (use cases) — trong đó dữ liệu được thu thập từ các nguồn khác nhau, được xử lý và tăng cường, sau đó trực quan hóa bằng QuickSight. Những ví dụ này cho thấy cách Amazon Q có thể nhanh chóng tạo ra các biểu đồ trực quan chỉ từ truy vấn ngôn ngữ tự nhiên, thể hiện sự tiện lợi và hiệu quả của việc sử dụng QuickSight và Q để xây dựng các dashboard mang tính khám phá và phân tích cao.

### Tổng quan

Bài viết này giới thiệu hai trường hợp sử dụng (use cases) minh họa cách trực quan hóa dữ liệu nhật ký luồng VPC (VPC Flow Logs) bằng Amazon Q trong QuickSight. Các ví dụ này có thể áp dụng linh hoạt, bất kể nguồn dữ liệu hoặc giải pháp nào được sử dụng để thu thập và làm giàu (enrich) dữ liệu. Mặc dù các trường hợp này được xây dựng riêng cho bài viết, bạn có thể tham khảo và tùy chỉnh để áp dụng cho các tình huống thực tế khác trong hệ thống của mình.

#### Trường hợp sử dụng #1: VPC Flow Logs được làm giàu với thông tin nhóm bảo mật (security group) và thẻ chi phí (cost tags)

Nhật ký luồng VPC tạo bản ghi bằng các trường được xác định trước. AWS Lambda có thể bổ sung thêm trường cho Nhật ký luồng này, tạo ra chế độ xem tùy chỉnh về luồng lưu lượng VPC.
Trong kiến ​​trúc được hiển thị trong Hình 1, Nhật ký luồng VPC được tạo và gửi đến Amazon Data Firehose. Nhật ký sau đó được chuyển tiếp đến Lambda để làm giàu. Lambda thêm các thuộc tính vào luồng, chẳng hạn như nhóm bảo mật và thẻ chi phí cho các phiên bản Amazon Elastic Compute Cloud (Amazon EC2) trong mục Nhật ký luồng VPC, trước khi phân phối chúng đến Amazon S3. AWS Glue Catalog lưu trữ các định nghĩa bảng cho Nhật ký luồng VPC đã được làm giàu. Điều này cho phép Amazon Athena truy vấn dữ liệu một cách hiệu quả. Cuối cùng, QuickSight sẽ trực quan hóa dữ liệu từ Athena. Luồng dữ liệu như sau:



Luồng dữ liệu (Data Flow) chi tiết:
- Kích hoạt VPC Flow Logs trên VPC hiện có thông qua AWS CloudFormation template.
- Log records được gửi tới Amazon Data Firehose.
- Data Firehose được cấu hình để phát ra metrics và logs tới Amazon CloudWatch, hỗ trợ xử lý sự cố (troubleshooting) khi cần.
- Data Firehose kích hoạt Lambda, hàm này sẽ làm giàu Flow Logs bằng các thuộc tính như security group và cost tag của Amazon EC2.
- Lambda cũng được cấu hình để gửi metrics và logs tới CloudWatch cho mục đích giám sát và khắc phục sự cố.
- Dữ liệu VPC Flow Logs sau khi làm giàu được lưu vào S3 bucket.
- AWS Glue sẽ quét (crawl) S3 bucket để tạo danh mục dữ liệu (data catalog) cho việc truy vấn.
- Amazon Athena thực hiện truy vấn dữ liệu đã được lập danh mục từ AWS Glue.
- Amazon QuickSight sử dụng Athena và Amazon S3 làm nguồn dữ liệu (data source) cho dataset. Một phân tích trống (blank analysis) được tạo sẵn để bắt đầu trực quan hóa Flow Logs.
- Amazon Q được kích hoạt trong phần phân tích thông qua chủ đề Q (Q topic). Q topic cho phép QuickSight diễn giải các truy vấn ngôn ngữ tự nhiên, giúp tự động tạo biểu đồ và trực quan hóa dữ liệu dễ dàng hơn.


#### Trường hợp sử dụng #2: VPC Flow Logs và Amazon Route 53 Resolver Logs

VPC Flow Logs và Amazon Route 53 Resolver Logs cung cấp thông tin bổ sung cho nhau về lưu lượng mạng trong VPC của bạn.

Trong kiến trúc minh họa ở Hình 2, VPC Flow Logs ghi lại thông tin chi tiết về lưu lượng mạng giữa các địa chỉ IP khác nhau trong VPC, nhưng không bao gồm tên miền (domain names). Khi kết hợp với Route 53 Resolver Logs, bạn có thể có được cái nhìn toàn diện hơn về lưu lượng mạng trong VPC, bao gồm cả thông tin IP và tên miền mà các máy chủ hoặc ứng dụng liên hệ đến.

- VPC Flow Logs được kích hoạt trên VPC hiện có thông qua - CloudFormation template.
- VPC Flow Logs được gửi đến Amazon S3 để lưu trữ.
- Route 53 Resolver Logs cũng được tạo và liên kết với VPC tương ứng nơi VPC Flow Logs đang hoạt động.
- Route 53 Resolver Logs được gửi đến Amazon S3.
- AWS Lambda được sử dụng để thực thi các truy vấn Athena, nhằm kết hợp dữ liệu giữa VPC Flow Logs và Route 53 Resolver Logs.
- Cả hai loại log này được kết hợp dựa trên địa chỉ IP mỗi ngày.
- Quá trình này giúp liên kết lưu lượng mạng trong VPC Flow Logs với tên miền (domain name). Phân tích được cập nhật hàng ngày, và mọi thay đổi trong ánh xạ giữa domain và IP đều được cập nhật tự động.
- Amazon QuickSight sử dụng Athena và Amazon S3 làm nguồn dữ liệu (data source) cho dataset.
-  Một phân tích trống (blank analysis) được tạo sẵn để bắt đầu trực quan hóa dữ liệu VPC Flow Logs.
- Amazon Q được kích hoạt trong phần phân tích thông qua Q topic.
 Q topic cho phép QuickSight hiểu và xử lý các truy vấn ngôn ngữ tự nhiên, giúp tạo biểu đồ và trực quan hóa dữ liệu dễ dàng hơn.


Bài viết này cung cấp hướng dẫn chi tiết (walkthrough) cho trường hợp sử dụng đầu tiên. Để tìm hiểu thông tin chi tiết hơn về cả hai trường hợp, bao gồm các bước triển khai và CloudFormation template liên quan, bạn có thể truy cập kho AWS Samples trên GitHub (GitHub repository).

### Điều kiện thực hiện: 

Trước khi bắt đầu, hãy thực hiện các bước sau:
- Triển khai một trong các mẫu CloudFormation được cung cấp trong kho GitHub aws-samples. Mỗi ví dụ trong kho này đều có tài liệu hướng dẫn chi tiết riêng, được lưu trữ trực tiếp trên GitHub của aws-samples. 
- Đảm bảo tài khoản người dùng QuickSight của bạn có vai trò PRO để có thể truy cập các tính năng Amazon Q được minh họa trong bài viết này.
Bài viết này cung cấp hai ví dụ minh họa. Trong tương lai, AWS có thể bổ sung thêm các ví dụ khác vào kho aws-samples. Các bước hướng dẫn (walkthrough) trong bài viết này có thể áp dụng cho tất cả các ví dụ được cung cấp.
### Hướng dẫn chi tiết: Khai thác thông tin từ dữ liệu của bạn với Q trong QuickSight
1. Xem lại các chủ đề Q (Q topics) được cấu hình sẵn trong mẫu CloudFormation đã triển khai. Các Q topics giúp đơn giản hóa truy vấn trực quan hóa dữ liệu bằng cách thiết lập từ đồng nghĩa ngôn ngữ tự nhiên cho các trường dữ liệu.
2. Truy vấn Q trong QuickSight để khám phá các thông tin chi tiết (insights) từ dữ liệu VPC Flow Log của bạn.


### Xem lại Q topics trong QuickSight
Trong phần này, chúng ta sẽ xem xét Q topic đã được triển khai tự động thông qua CloudFormation template.
1. Mở AWS Console và điều hướng đến Amazon QuickSight.

2. Ở bảng điều hướng bên trái, chọn Topics.

3. Chọn tên chủ đề (topic name) tương ứng.

Chọn các tab ở trên cùng, điều hướng đến Dữ liệu (Data), sau đó đến TRƯỜNG DỮ LIỆU (Data Fields). QuickSight tự động điền các trường dữ liệu từ tập dữ liệu vào các trường dữ liệu. Công cụ cũng sẽ điền các từ đồng nghĩa cho các trường tương ứng nếu có thể. Trong ví dụ này, các từ đồng nghĩa đã được định nghĩa trong mẫu CloudFormation. Từ đồng nghĩa giúp Amazon Q liên kết các truy vấn của bạn với các trường trong tập dữ liệu. Bạn có thể tùy chỉnh thêm các trường dữ liệu để phù hợp với thuật ngữ của tổ chức mình. Điều này sẽ mang lại phản hồi có ý nghĩa và chính xác hơn từ Amazon Q. Hãy làm mới các chỉ mục chủ đề của Q để phản ánh những thay đổi này trong các phân tích trong tương lai.

Một tùy chỉnh hữu ích khác là Field Value Synonyms. Tính năng này cho phép bạn thêm từ đồng nghĩa vào các giá trị trong dữ liệu VPC Flow Logs. Trong ví dụ sau, từ đồng nghĩa đã được thêm vào số giao thức IP. Điều này cho phép Q diễn giải chính xác các truy vấn VPC Flow Logs với các từ icmp, tcp, udp.

### Truy vấn Q để biết thông tin chi tiết về dữ liệu Nhật ký luồng VPC

Chúng tôi hỏi Q trong QuickSight một câu hỏi liên quan đến dữ liệu Nhật ký luồng VPC. Chọn "Đặt câu hỏi về" ở giữa phía trên cùng của màn hình QuickSight.

Như được hiển thị trong Hình 7 sau đây, chúng tôi đã hỏi Amazon Q "thẻ chi phí nào có nhiều megabyte nhất vào tháng 10 năm 2024 theo giờ". Amazon Q đã xử lý câu hỏi của chúng tôi và, sử dụng các từ đồng nghĩa đã được xác định, diễn giải câu hỏi là "Tổng số megabyte theo giờ Ngày giờ bắt đầu và Thẻ chi phí cho Ngày giờ bắt đầu vào tháng 10 năm 2024". Amazon Q đã đưa ra câu trả lời cho câu hỏi, được hỗ trợ bởi dữ liệu trực quan chi tiết để minh họa cho câu trả lời.

#### Hướng dẫn: Điền Phân tích QuickSight với Amazon Q

Liên kết chủ đề Q với Phân tích QuickSight
Điền Phân tích QuickSight bằng cách thêm hình ảnh do Amazon Q tạo ra. Bài viết này cung cấp một vài truy vấn ví dụ.

1. Liên kết Phân tích QuickSight với chủ đề Q

Sau khi xem xét các chủ đề Q, chúng ta sẽ khám phá Phân tích. Phân tích là tập hợp các hình ảnh trực quan, bảng thông tin tương tác và thông tin chi tiết về dữ liệu được tạo và sắp xếp trên một hoặc nhiều trang tính để khám phá và trình bày dữ liệu một cách hiệu quả.

Chúng ta tạo các hình ảnh trực quan đầu tiên trong Phân tích. Mẫu CloudFormation đã tạo một Phân tích trống được liên kết với tập dữ liệu nhập dữ liệu Nhật ký luồng VPC từ Athena. Chọn tên Phân tích để bắt đầu, như được hiển thị trong Hình 8.

Để chạy truy vấn trên Amazon Q, hãy liên kết chủ đề Q với Phân tích. Ở giữa thanh trên cùng, chọn dấu ba chấm dọc (⋮) bên cạnh Xây dựng hình ảnh trực quan và chọn Liên kết Chủ đề từ menu. Bật tùy chọn Liên kết chủ đề cho Xây dựng hình ảnh trực quan và Hỏi & Đáp, rồi chọn chủ đề được CloudFormation triển khai từ menu thả xuống. Chọn ÁP DỤNG THAY ĐỔI, như minh họa trong Hình 9.

2. Tạo nội dung cho QuickSight Analysis 

Ở giữa thanh trên cùng, chọn Build visual. Ngăn bên phải Build a visual sẽ hiển thị. Bắt đầu bằng cách nhập câu hỏi ngôn ngữ tự nhiên đầu tiên và chọn BUILD. Chúng tôi đã cung cấp bốn câu hỏi mẫu để bạn bắt đầu cho trường hợp sử dụng đầu tiên. Tài liệu QuickSight cung cấp các loại câu hỏi được Q hỗ trợ cùng với nhiều câu hỏi mẫu hơn. QuickSight Enterprise Edition là điều kiện tiên quyết để đặt câu hỏi bằng Amazon Q.

Câu hỏi mẫu:

hiển thị IP nguồn và đích hàng đầu theo gigabyte
hiển thị IP nguồn và đường dẫn cổng internet hàng đầu theo gigabyte
hiển thị nhóm bảo mật hàng đầu theo megabyte
hiển thị megabyte ngày khởi hành tháng 5 theo giờ

Trường hợp sử dụng thứ hai có các câu hỏi mẫu riêng được ghi lại trong kho lưu trữ GitHub.

Amazon Q diễn giải các câu hỏi và đưa ra truy vấn dựa trên trường từ tập dữ liệu và các từ đồng nghĩa đã xác định trong chủ đề được liên kết. Amazon Q cũng chọn một loại hình ảnh, có thể thay đổi. Ở góc trên bên phải của hình ảnh, hãy chọn hình ảnh biểu đồ thanh và chọn loại hình ảnh thể hiện dữ liệu trực quan nhất cho bạn. Chọn THÊM VÀO PHÂN TÍCH, như thể hiện trong Hình 10.

Bảng thông tin sau đây được điền bằng hình ảnh từ bốn câu hỏi ví dụ, như thể hiện trong Hình 11.

### Kết Luận

Trong bài viết này, bạn đã tìm hiểu cách sử dụng Amazon QuickSight để trực quan hóa dữ liệu từ nhiều nguồn dữ liệu khác nhau bằng các truy vấn ngôn ngữ tự nhiên. Amazon Q trong QuickSight giúp dân chủ hóa quyền truy cập dữ liệu, trao quyền cho mọi người trong tổ chức của bạn đưa ra quyết định dựa trên dữ liệu. Việc sắp xếp dữ liệu thành các chủ đề trực quan và cho phép truy vấn ngôn ngữ tự nhiên cho phép người dùng Amazon Q hiểu rõ hơn về luồng VPC. Để giúp nhóm của bạn bắt đầu sử dụng QuickSight, chúng tôi khuyên bạn nên xem các hướng dẫn sau: Amazon QuickSight Q là gì và các phương pháp hay nhất dành cho tác giả QuickSight Q.

### Về tác giả: 

#### Rashmiman Ray

Rashmiman là Quản lý Tài khoản Kỹ thuật tại AWS, có trụ sở tại New Jersey. Anh làm việc với khách hàng AWS Enterprise, cung cấp hướng dẫn kỹ thuật và khuyến nghị thực hành tốt nhất để giúp họ thành công trên nền tảng đám mây. Ngoài giờ làm việc, anh thích đi bộ đường dài, chơi cricket và nấu các món ngon Ấn Độ.

#### Opeoluwa Victor Babasanmi

Victor là Kiến trúc sư Giải pháp Chuyên gia Mạng Cấp cao tại AWS. Anh tập trung cung cấp cho khách hàng hướng dẫn kỹ thuật về lập kế hoạch và xây dựng giải pháp dựa trên các phương pháp hay nhất, đồng thời chủ động duy trì hoạt động ổn định cho môi trường AWS của họ. Khi không hỗ trợ khách hàng, bạn có thể thấy anh ấy đang chơi bóng đá, tập thể dục hoặc tìm kiếm một cuộc phiêu lưu mới ở đâu đó.

#### Diego Hernandez

Diego Hernandez là Quản lý Tài khoản Kỹ thuật tại Canada. Niềm đam mê của Diego là tất cả những gì liên quan đến mạng lưới quan hệ. Trong thời gian rảnh rỗi, Diego thích dành thời gian cho gia đình và trượt tuyết.