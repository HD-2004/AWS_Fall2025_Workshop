---
title: "Blog 3"
date: 2025-09-10
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
# Validate Your Lambda Runtime with CloudFormation Lambda Hooks
(Xác thực môi trường chạy Lambda của bạn bằng các lambda Hook trong CloudFormation.)

Tác Giả: Matteo Luigi Restelli và Stella Hie | Ngày 02 tháng 04 năm 2025 | Trong AWS CloudFormation, AWS Lambda, Configuration, compliance, and auditing, DevOps, Intermediate (200), Learning Levels, Management & Governance, Management Tools

### Giới thiệu: 

Bài viết này minh họa cách tận dụng AWS CloudFormation Lambda Hooks để áp dụng các quy tắc tuân thủ (compliance rules) ngay trong quá trình khởi tạo tài nguyên, cho phép bạn đánh giá và xác thực cấu hình của các hàm Lambda so với các chính sách tùy chỉnh trước khi triển khai. Thông thường, các chính sách này ảnh hưởng đến cách phần mềm được xây dựng, chẳng hạn như giới hạn phiên bản ngôn ngữ hoặc môi trường chạy (runtime). Một ví dụ điển hình là áp dụng các chính sách đó cho AWS Lambda — dịch vụ điện toán không máy chủ (serverless compute service) cho phép chạy mã mà không cần cung cấp hay quản lý máy chủ. Mặc dù AWS Lambda đã tự động xử lý việc ngừng hỗ trợ (deprecation) các môi trường chạy, ngăn bạn triển khai những runtime không còn được hỗ trợ, nhưng một số tổ chức vẫn cần tự thiết lập và thực thi các quy tắc tuân thủ riêng — những quy tắc không trực tiếp liên quan đến việc ngừng hỗ trợ một phiên bản ngôn ngữ cụ thể.


### Giới thiệu về Lambda Hooks: 

AWS CloudFormation Lambda Hooks là một tính năng mạnh mẽ cho phép các nhà phát triển đánh giá các thao tác của CloudFormation và AWS Cloud Control API thông qua mã tùy chỉnh được triển khai dưới dạng hàm Lambda. Khả năng này giúp kiểm tra cấu hình tài nguyên một cách chủ động trước khi khởi tạo, từ đó tăng cường bảo mật, tuân thủ quy định và hiệu quả vận hành.

Lambda Hooks cung cấp một cơ chế cho phép chặn (intercept) và đánh giá nhiều loại thao tác khác nhau trong AWS CloudFormation, bao gồm các thao tác trên tài nguyên (resource operations), trên stack, và trên change set (tính năng này cũng có thể được dùng với Cloud Control API, nhưng trong bài viết này chỉ tập trung vào CloudFormation). Khi bạn kích hoạt một Lambda Hook, CloudFormation sẽ tạo một mục đăng ký (entry) trong registry của tài khoản dưới dạng Private Hook, cho phép bạn cấu hình hook đó cho các tài khoản AWS và vùng (Region) cụ thể. Trong quá trình cấu hình Lambda Hooks, bạn có thể chỉ định một hoặc nhiều hàm Lambda sẽ được gọi trong quá trình đánh giá. Các hàm này có thể nằm trong cùng một tài khoản và vùng AWS với Hook, hoặc ở một tài khoản khác (miễn là bạn đã thiết lập quyền truy cập phù hợp). Quá trình đánh giá này diễn ra tại những thời điểm cụ thể trong vòng đời của CloudFormation Stack — ví dụ: trong quá trình tạo mới, cập nhật hoặc xóa stack. Lúc này, các hàm Lambda được cấu hình sẽ được gọi để đánh giá các thay đổi được đề xuất dựa trên các quy tắc tuân thủ (compliance rules) mà bạn đã xác định. Dựa trên kết quả đánh giá, hook có thể chặn thao tác hoặc phát cảnh báo, cho phép thao tác tiếp tục.

Lambda Hooks thực hiện việc đánh giá tài nguyên trước khi chúng được khởi tạo bởi CloudFormation, giúp tạo ra một lớp quản trị (governance layer) chủ động. Điều này có nghĩa là các tài nguyên không tuân thủ sẽ bị phát hiện và ngăn chặn trước khi triển khai, thay vì phải sửa chữa sau đó. Bằng cách tận dụng Lambda Hooks, các tổ chức có thể tự động hóa và tiêu chuẩn hóa quy trình kiểm tra tuân thủ trên tất cả các tài khoản và vùng AWS, giúp đảm bảo tính nhất quán và giảm bớt gánh nặng quản lý thủ công.

### Tổng quan giải pháp: 

Các phần sau đây minh họa một trường hợp sử dụng thực tế của AWS CloudFormation Lambda Hooks, tập trung vào việc thực thi các quy tắc tuân thủ (compliance rules) đối với các môi trường chạy (runtimes) của AWS Lambda.

Giới thiệu AnyCompany — một doanh nghiệp tiên phong với bộ quy tắc tuân thủ chặt chẽ trong hoạt động phát triển phần mềm. Trong số đó, có chính sách nghiêm ngặt về việc sử dụng các môi trường chạy AWS Lambda cụ thể.

Khi AnyCompany tiếp tục chuyển đổi sang kiến trúc serverless, họ gặp phải một thách thức: làm thế nào để ngăn việc triển khai các hàm Lambda sử dụng runtime không tuân thủ. Vì doanh nghiệp này sử dụng AWS CloudFormation để triển khai các hàm Lambda, AnyCompany mong muốn tận dụng sức mạnh của AWS CloudFormation Lambda Hooks để giải quyết vấn đề này.

Chúng ta sẽ tìm hiểu quy trình thiết lập, minh họa cách hoạt động của Hook, và thảo luận về ý nghĩa rộng hơn của việc duy trì tuân thủ trong môi trường điện toán đám mây năng động.


### Kiến trúc: 

Sơ đồ kiến trúc sau đây mô tả cách triển khai Lambda Hook. Trong mô hình này, AWS CloudFormation Lambda Hooks được sử dụng để chặn quá trình triển khai các hàm Lambda và thực hiện kiểm tra tính tuân thủ trên các tài nguyên đó.

Lambda Hook sẽ tương tác với một hàm AWS Lambda khác, hàm này chịu trách nhiệm thực hiện các bước kiểm tra tuân thủ.

Cuối cùng, hệ thống sử dụng AWS Systems Manager Parameter Store để lưu trữ tham số cấu hình (Configuration Parameter), chứa danh sách các môi trường chạy Lambda được phép sử dụng.


1. Nhà phát triển (Developer) hoặc pipeline CI/CD triển khai một CloudFormation Stack có chứa các hàm Lambda.

2. CloudFormation sẽ gọi đến Lambda Hook tương ứng, hook này được cấu hình để chặn (intercept) các thao tác liên quan đến tài nguyên AWS Lambda. Trong trường hợp này, hook được thiết lập để “FAIL” (thất bại) quá trình triển khai nếu các bước kiểm tra không đạt yêu cầu.

3. Lambda Hook sẽ kiểm tra xem runtime của hàm Lambda có tuân thủ quy định của công ty hay không. Để làm điều này, nó so sánh runtime của hàm Lambda với danh sách các runtime được phép, danh sách này được lưu dưới dạng Parameter trong AWS Systems Manager Parameter Store. Lưu ý rằng trong ví dụ này, ta sử dụng SSM Parameter Store để lưu cấu hình, nhưng có thể dùng các dịch vụ khác như Amazon DynamoDB, AWS Secrets Manager, hoặc AppConfig.

4. Sau khi kiểm tra tính tuân thủ của runtime, Lambda Hook phản hồi lại:
- Thất bại (failure): nếu runtime của Lambda không tuân thủ quy định.
- Thành công (success): nếu runtime tuân thủ chính sách của công ty.

5. Dựa theo phản hồi của Lambda Hook, quá trình triển khai CloudFormation sẽ được tiếp tực hoặc bị dừng lại. 

- hook-lambda: thư mục chứa toàn bộ nguồn code liên quan đến - CloudFormation Lambda Hook, bao gồm hàm Lambda dùng để kiểm tra  (Validation Lambda Function) và mẫu CloudFormation. 
- sample: thư mục chứa sample code được sử dụng để kiểm thử - CloudFormation Lambda Hook. 
- deploy.sh: tập lệnh tiện ích (utility script) dùng để deploy giải pháp thông qua AWS CLI. 
- cleanup.sh: tập lệnh tiện ích dùng để dọn dẹp (clean up) hạ tầng - CloudFormation Hook trên AWS bằng AWS CLI.
- template.yml: tệp mẫu CloudFormation (AWS CloudFormation Template) chứa toàn bộ các tài nguyên AWS được sử dụng trong giải pháp này.

### Điều kiện tiên quyết: 

Bạn phải thực hiện các điều kiện sau cho giải pháp: 

- Tài khoản AWS — hoặc đăng ký để tạo và kích hoạt một tài khoản AWS mới.
- Phần mềm cần cài đặt trên máy phát triển (development machine):
- Cài đặt AWS Command Line Interface (AWS CLI) và cấu hình để kết nối với tài khoản AWS của bạn.
- Cài đặt Node.js và sử dụng trình quản lý gói như npm.
- Thông tin xác thực AWS (AWS credentials) phù hợp để tương tác với các tài nguyên trong tài khoản AWS của bạn.

### Quy trình thực hiện: 
#### Tạo hàm xác thực AWS Lambda – Mã Lambda (Creating the AWS Lambda Validation Function – Lambda Code)

CloudFormation Lambda Hook sẽ tương tác với một hàm Lambda cụ thể (được gọi là Validation Lambda trong suốt phần còn lại của bài viết). Hàm này sẽ được gọi (invoke) trong quá trình CloudFormation thực hiện các thao tác CREATE hoặc UPDATE STACK có liên quan đến các hàm Lambda. Mục tiêu là kiểm tra xem các hàm Lambda này có sử dụng runtime tuân thủ quy định của AnyCompany hay không.

Dưới đây là mô tả chi tiết các bước mà Validation Lambda function handler thực hiện (mã được viết bằng TypeScript):

Validation Lambda trước tiên lấy giá trị biến môi trường (environment variable) — biến này chứa tên tham số (parameter name) trong AWS Systems Manager Parameter Store, nơi lưu danh sách các runtime tuân thủ (compliant runtimes list).
Tiếp theo, hàm thực hiện các bước kiểm tra an toàn (safety checks) để. Đảm bảo rằng chỉ các tài nguyên Lambda (Lambda Resources) mới được xem xét. Đảm bảo rằng thuộc tính Runtime của hàm Lambda được xác định rõ ràng.

Lưu ý: Hai bước kiểm tra an toàn trên về lý thuyết có thể bỏ qua, vì Hook đã được cấu hình chỉ để tương tác với tài nguyên Lambda, và thuộc tính Runtime của Lambda luôn là bắt buộc. Tuy nhiên, các bước này vẫn được giữ lại nhằm minh họa cách lấy thông tin từ sự kiện Lambda Hook (Lambda Hook event) trong handler của bạn.

```
const parameterName = process.env.PERMITTED_RUNTIMES_PARAM;
if (!parameterName) {
	throw new Error('Permitted Runtimes Parameter is not set');
}

const resourceProperties = event.requestData.targetModel.resourceProperties;
// Check if this is a Lambda function resource
if (event.requestData.targetType !== 'AWS::Lambda::Function') {
console.log("Resource is not a Lambda function, skipping");
	return {
		hookStatus: 'SUCCESS',
		message: 'Not a Lambda function resource, skipping validation',
		clientRequestToken: event.clientRequestToken
	}
}

// Check runtime version compliance
const runtime = resourceProperties.Runtime;
if (!runtime) {
	console.log("Runtime not defined, failing");
	return {
		hookStatus: 'FAILURE',
		errorCode: 'NonCompliant',
		message: 'Runtime is required for Lambda functions',
		clientRequestToken: event.clientRequestToken
	}
}
```

Tiếp theo, Validation Lambda sẽ lấy giá trị của Tham số cấu hình (Configuration Parameter) từ AWS Systems Manager Parameter Store thông qua một lớp tiện ích (utility class) có tên là ParameterStoreService. Trong ví dụ của bài viết này, giá trị bên trong Tham số cấu hình là một danh sách các chuỗi (list of strings), trong đó mỗi chuỗi đại diện cho một giá trị runtime hợp lệ của AWS Lambda, ví dụ:

nodejs22.x, nodejs20.x, python3.11, python3.10, java17, java11, dotnet6

Sau khi lấy được giá trị này, Validation Lambda sẽ kiểm tra xem runtime của tài nguyên Lambda đang được triển khai có nằm trong danh sách runtime được phép hay không.Nếu runtime không tuân thủ (non-compliant) → hàm sẽ trả về phản hồi được định dạng đúng, trong đó hookStatus = FAILURE. Nếu runtime tuân thủ (compliant) → phản hồi sẽ chứa hookStatus = SUCCESS

```
// Retrieve configuration from Parameter Store
const compliantRuntimes = await parameterStoreService.getParameterFromStore(parameterName);
// Check if Lambda runtime is permitted or not
if (!compliantRuntimes.includes(runtime)) {
console.log("Runtime " + runtime + " not compliant ");
	return {
		hookStatus: 'FAILURE',
		errorCode: 'NonCompliant',
		message: `Runtime ${runtime} is not compliant. Please use one of: ${compliantRuntimes.join(', ')}`,
		clientRequestToken: event.clientRequestToken
	}
}
return {
	hookStatus: 'SUCCESS',
	message: 'Runtime version compliance check passed',
	clientRequestToken: event.clientRequestToken
}
```

Để biết thêm thông tin về các giá trị phản hồi (response values) có thể có của CloudFormation Lambda Hooks Lambda, bạn có thể tham khảo liên kết được cung cấp. 
Tạo hàm xác thực Lambda – Định nghĩa Lambda trong CloudFormation (Creating the validation Lambda – Lambda CloudFormation definition)

Hàm Validation Lambda sẽ được triển khai thông qua CloudFormation, trong cùng một Stack với: Định nghĩa CloudFormation Lambda Hook, và Tham số AWS Systems Manager Parameter Store. Dưới đây là một đoạn (fragment) trong mẫu CloudFormation Template mô tả cấu hình của Validation Lambda:

```YAML
# Lambda Function
ValidationFunction:
	Type: AWS::Lambda::Function
	Properties:
		Handler: index.handler
		Role: !GetAtt LambdaExecutionRole.Arn
		Code:
			S3Bucket: !Ref DeploymentBucket
			S3Key: hook-lambda.zip
		Runtime: nodejs22.x
		Timeout: 60
		MemorySize: 128
		Environment:
			Variables:
				PERMITTED_RUNTIMES_PARAM: !Ref ParameterStoreParamName
```

Bạn cần gán cho hàm Lambda với IAM (IAM role) có các quyền thích hợp để truy cập tham số (Parameter) trong AWS System Manager Parameter Store. 

```YAML
# Lambda Function Role
LambdaExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
# IAM Policy to access Parameter Store
ParameterStoreAccessPolicy:
    Type: AWS::IAM::RolePolicy
    Properties:
      RoleName: !Ref LambdaExecutionRole
      PolicyName: ParameterStoreAccess
      PolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Action:
              - ssm:GetParameter
            Resource: !Sub arn:aws:ssm:${AWS::Region}:${AWS::AccountId}:parameter${ParameterStoreParamName}
```

Tạo CloudFormation Lambda Hook (Creating the CloudFormation Lambda Hook)
Ở bước này, bạn chỉ cần tạo một CloudFormation Lambda Hook phù hợp.
 Hook này cần đáp ứng các yêu cầu sau:
Kích hoạt (activate) trong quá trình CREATE và UPDATE của CloudFormation.
Chỉ xem xét các tài nguyên CloudFormation loại AWS::Lambda::Function.
Thực thi trong giai đoạn “Pre-Provisioning” của CloudFormation Template (tức là trước khi tài nguyên được khởi tạo).
Áp dụng cho cả thao tác trên Stack và Resource (Stack và Resource Operations).
Gọi đến hàm Lambda Validation đã được định nghĩa từ trước.
Dưới đây là định nghĩa (definition) tương ứng trong CloudFormation Template:

```YAML

# Lambda Hook
ValidationHook:
    Type: AWS::CloudFormation::LambdaHook
    Properties:
      Alias: Private::Lambda::LambdaResourcesComplianceValidationHook
      LambdaFunction: !GetAtt ValidationFunction.Arn
      ExecutionRole: !GetAtt HookExecutionRole.Arn
      FailureMode: FAIL
      HookStatus: ENABLED
      TargetFilters:
        Actions:
          - CREATE
          - UPDATE
        InvocationPoints:
          - PRE_PROVISION
        TargetNames:
          - AWS::Lambda::Function
      TargetOperations:
        - RESOURCE
        - STACK
```

Xin lưu ý rằng mẫu CloudFormation ở trên có tham chiếu đến một IAM Role, vì Hook cần có quyền truy cập thích hợp (proper permissions) để gọi đến hàm Lambda mục tiêu (target Lambda Function). Dưới đây là định nghĩa của IAM Role:

```
# Hook Execution Role
HookExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: hooks.cloudformation.amazonaws.com
            Action: sts:AssumeRole

# IAM Policy for Lambda Invocation
LambdaInvokePolicy:
    Type: AWS::IAM::RolePolicy
    Properties:
      RoleName: !Ref HookExecutionRole
      PolicyName: LambdaInvokePolicy
      PolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Action:
              - lambda:InvokeFunction
            Resource: !GetAtt ValidationFunction.Arn
```

Cấu hình các runtime tuân thủ - Sử dụng Systems Manager Parameter Store

AWS Systems Manager Parameter Store là một dịch vụ lưu trữ phân cấp an toàn dành cho việc quản lý dữ liệu cấu hình và quản lý bí mật, cho phép người dùng lưu trữ và truy xuất dữ liệu như cấu hình, chuỗi cơ sở dữ liệu, v.v. dưới dạng giá trị tham số.

Trong ví dụ cụ thể này, chúng ta sẽ tận dụng Parameter Store để lưu trữ cấu hình thời gian chạy Lambda được phép. Giá trị cấu hình này là một tham số StringList, chứa danh sách các thời gian chạy được phép được phân tách bằng dấu phẩy. Dưới đây là đoạn mã mẫu CloudFormation định nghĩa Tham số:

```
# Parameter Store Parameter
ConfigParameter:
    Type: AWS::SSM::Parameter
    Properties:
      Name: !Ref ParameterStoreParamName
      Type: StringList
      Value: !Ref ParameterStoreDefaultValue
      Description: "Configuration for Lambda Hook"
``` 

Lưu ý: phần sử dụng tham số (parameters) trong CloudFormation cho các thuộc tính ‘Name’ và ‘Value’ giúp nhập giá trị một cách linh hoạt (dynamic input) khi triển khai (deploy) CloudFormation template.

### Triển khai giải pháp 

Để triển khai (deploy) giải pháp này, bạn có thể sử dụng tập lệnh deploy.sh nằm trong thư mục gốc của repository.

Tập lệnh này sẽ thực hiện các bước sau:

- Biên dịch và build hàm Lambda Validation
- Tạo một Amazon S3 Bucket để lưu trữ CloudFormation Template
- Tải lên (upload) CloudFormation Template và mã nguồn Lambda lên S3 Bucket
- Triển khai (deploy) CloudFormation Template

Kiểm thử Lambda Hook (Testing the Lambda Hook)
Để kiểm thử CloudFormation Lambda Hook, bạn hãy triển khai một CloudFormation Template đơn giản chứa một hàm Lambda “Hello World”. Trước tiên, kiểm thử hàm Lambda được cấu hình với một runtime được phép (permitted runtime). Sau đó, sửa đổi template để cấu hình hàm Lambda với một runtime không tuân thủ (non-compliant runtime).
Dưới đây là định nghĩa ban đầu của CloudFormation Template dùng để kiểm thử:

```
# Lambda Function
HelloWorldFunction:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: hello-world-function
      Runtime: nodejs22.x
      Handler: index.handler
      Role: !GetAtt LambdaExecutionRole.Arn
      Code:
        ZipFile: |
          exports.handler = async (event, context) => {
              console.log('Hello World!');
              const response = {
                  statusCode: 200,
                  body: JSON.stringify('Hello World!')
              };
              return response;
          };
      Timeout: 30
```

Lưu ý rằng giá trị Runtime là nodejs22.x, hiện đang nằm trong danh sách các runtime được phép sử dụng. Do đó, kỳ vọng là quá trình triển khai (deployment) của hàm Lambda này sẽ thành công.
Triển khai mẫu template này bằng AWS CLI:

```
aws cloudformation deploy \
  --template-file sample/template.yml \
  --stack-name hook-test \
  --capabilities CAPABILITY_IAM
```

Kiểm tra CloudFormation Console: 


Đúng như mong đợi, quá trình triển khai đã thành công. Bạn cũng có thể thấy rằng CloudFormation Lambda Hook đã được kích hoạt, bằng cách kiểm tra CloudWatch Logs.

Bây giờ, hãy chỉnh sửa mẫu câu template gốc để thiết lập một Lambda Runtime không nằm trong danh sách các runtime được phép:

```
# Lambda Function
HelloWorldFunction:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: hello-world-function
      Runtime: nodejs18.x
      Handler: index.handler
      Role: !GetAtt LambdaExecutionRole.Arn
      Code:
        ZipFile: |
          exports.handler = async (event, context) => {
              console.log('Hello World!');
              const response = {
                  statusCode: 200,
                  body: JSON.stringify('Hello World!')
              };
              return response;
          };
      Timeout: 30
      MemorySize: 128
```

Triển khai mẫu này cùng AWS CLI với lệnh tương tự và kiểm tra CloudFormation Console:


Đúng như dự đoán, quá trình triển khai không thành công. CloudFormation Lambda Hook đã được kích hoạt, và vì Lambda Runtime không nằm trong danh sách các runtime được phép, nên việc triển khai đã bị chặn.
Bạn cũng có thể thấy rằng Hook đã thất bại trong CloudWatch Logs.

### Dọn dẹp tài nguyên:
Để xóa các tài nguyên liên quan đến mẫu thử nghiệm, bạn có thể chạy tập lệnh cleanup_sample.sh trong thư mục sample. Tập lệnh này sẽ xóa CloudFormation Template của mẫu thử nghiệm thông qua AWS CLI.

Để xóa các tài nguyên liên quan đến giải pháp chính được mô tả ở trên (dựa trên AWS CloudFormation Lambda Hook), bạn có thể sử dụng tập lệnh cleanup.sh trong thư mục gốc (root folder) của kho mã nguồn (repository). Tập lệnh này sẽ thực hiện các thao tác sau:
- Xóa CloudFormation Stack
- Làm trống S3 Bucket được sử dụng cho quá trình triển khai Stack
- Xóa S3 Bucket


### Kết luận: 
Trong bài viết này, bạn đã tìm hiểu cách triển khai CloudFormation Hooks để đảm bảo tuân thủ (compliance) đối với Lambda runtime trong toàn bộ hạ tầng AWS của mình. Bằng cách tận dụng khả năng của Lambda Hook, bạn đã học được cách tạo cơ chế kiểm soát phòng ngừa nhằm xác thực cấu hình runtime của Lambda trước khi triển khai.

Bằng việc kích hoạt Lambda Hook và triển khai hàm Lambda tùy chỉnh để xác thực, bạn đã thiết lập được một cơ chế tự động đảm bảo rằng chỉ những runtime hợp lệ mới được sử dụng trong các hàm Lambda của tổ chức khi tạo hoặc cập nhật CloudFormation Stack. Giải pháp này có thể được tích hợp dễ dàng với các công cụ phát triển phổ biến như AWS CLI, AWS SAM, CI/CD pipelines, và AWS CDK, giúp đơn giản hóa việc áp dụng kiểm soát trong quy trình làm việc hiện tại, loại bỏ nhu cầu kiểm tra thủ công hoặc khắc phục sau triển khai.

Phương pháp xác thực được trình bày trong bài viết này không chỉ giới hạn ở Lambda runtimes, mà còn có thể mở rộng cho các tài nguyên AWS khác được CloudFormation hỗ trợ, giúp bạn áp dụng chính sách kiểm soát trên nhiều thành phần hạ tầng khác nhau trong môi trường AWS.

### Về tác giả: 

#### Matteo Luigi Restelli

Matteo Luigi Restelli là Kiến trúc sư Giải pháp Đối tác Cấp cao (Sr. Partner Solutions Architect) tại AWS. Ông chủ yếu làm việc với các đối tác tư vấn AWS tại Ý, và có chuyên môn trong các lĩnh vực như Hạ tầng dưới dạng mã (Infrastructure as Code), Phát triển ứng dụng gốc đám mây (Cloud Native App Development) và DevOps. Ngoài công việc, ông yêu thích bơi lội, âm nhạc rock & roll, và học hỏi điều mới mỗi ngày, đặc biệt là trong lĩnh vực Khoa học Máy tính.

#### Stella Hie

Stella Hie là Quản lý Sản phẩm Kỹ thuật Cấp cao (Sr. Product Manager Technical) phụ trách AWS Infrastructure as Code. Cô tập trung vào mảng kiểm soát chủ động và quản trị (proactive control and governance), với mục tiêu mang đến trải nghiệm tốt nhất cho khách hàng trong việc sử dụng các giải pháp AWS một cách an toàn. Ngoài công việc, cô yêu thích leo núi, chơi đàn piano, và xem các buổi biểu diễn trực tiếp (live shows).