---
title: "Blog 3"
date: 2025-09-10
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# Validate Your Lambda Runtime with CloudFormation Lambda Hooks

(Validate your Lambda runtime environment using Lambda Hooks in CloudFormation.)

Author: Matteo Luigi Restelli and Stella Hie | April 02, 2025 | In AWS CloudFormation, AWS Lambda, Configuration, compliance, and auditing, DevOps, Intermediate (200), Learning Levels, Management & Governance, Management Tools

### Introduction:

This article illustrates how to leverage AWS CloudFormation Lambda Hooks to enforce compliance rules during resource initialization, allowing you to evaluate and validate the configuration of Lambda functions against custom policies before deployment. Typically, these policies affect how software is built, such as limiting language versions or runtimes. A prime example is applying these policies to AWS Lambda — the serverless compute service that allows code to run without the need to provide or manage servers. While AWS Lambda automatically handles the deprecation of runtimes, preventing you from deploying unsupported runtimes, some organizations still need to define and enforce their own compliance rules — rules that aren't directly related to deprecating a specific language version.

### Introduction to Lambda Hooks:

AWS CloudFormation Lambda Hooks are a powerful feature that allows developers to evaluate CloudFormation and AWS Cloud Control API operations via custom code deployed as Lambda functions. This capability enables proactive evaluation of resource configurations before initialization, thereby enhancing security, compliance, and operational efficiency.

Lambda Hooks provide a mechanism to intercept and evaluate a wide range of operations within AWS CloudFormation, including resource operations, stack operations, and change sets (this feature can also be used with the Cloud Control API, but this article focuses on CloudFormation). When you trigger a Lambda Hook, CloudFormation registers an entry in your account's registry as a Private Hook, allowing you to configure the hook for specific AWS accounts and regions. During the Lambda Hooks configuration process, you can specify one or more Lambda functions to be invoked during the evaluation process. These functions can reside in the same account and region as the Hook, or in a different account (as long as the proper access permissions are set up). The evaluation occurs at specific points in the CloudFormation Stack lifecycle — such as during stack creation, update, or deletion. At these points, the configured Lambda functions are called to evaluate the proposed changes based on the compliance rules you have defined. Based on the evaluation results, the hook can either block the operation or trigger a warning, allowing the operation to proceed.

Lambda Hooks allow for proactive evaluation of resources before they are initialized by CloudFormation, creating a governance layer. This means non-compliant resources are detected and blocked before deployment, rather than needing to be fixed afterward. By leveraging Lambda Hooks, organizations can automate and standardize compliance checks across all AWS accounts and regions, ensuring consistency and reducing the burden of manual management.

### Solution Overview:

The following sections demonstrate a practical use case of AWS CloudFormation Lambda Hooks, focusing on enforcing compliance rules for AWS Lambda runtimes.

Introducing AnyCompany — a pioneering company with strict compliance rules in software development. Among these rules is a strict policy on the use of specific AWS Lambda runtimes.

As AnyCompany continues to transition to a serverless architecture, they face a challenge: how to prevent the deployment of Lambda functions using non-compliant runtimes. Since this company uses AWS CloudFormation to deploy Lambda functions, AnyCompany seeks to leverage the power of AWS CloudFormation Lambda Hooks to solve this problem.

We will explore the setup process, demonstrate how the Hook works, and discuss the broader implications of maintaining compliance in a dynamic cloud computing environment.

### Architecture:

The architecture diagram below illustrates how the Lambda Hook is deployed. In this model, AWS CloudFormation Lambda Hooks are used to intercept the deployment of Lambda functions and perform compliance checks on those resources.

The Lambda Hook interacts with another AWS Lambda function, responsible for performing compliance checks.

Finally, the system uses AWS Systems Manager Parameter Store to store a configuration parameter containing a list of permitted Lambda runtimes.

1. A developer (or CI/CD pipeline) deploys a CloudFormation Stack that includes Lambda functions.
2. CloudFormation will call the corresponding Lambda Hook, which is configured to intercept AWS Lambda-related resource operations. In this case, the hook is set up to "FAIL" the deployment process if the compliance checks fail.
3. The Lambda Hook checks if the runtime of the Lambda function complies with the company’s regulations. To do this, it compares the runtime of the Lambda function against the list of permitted runtimes, which is stored as a Parameter in AWS Systems Manager Parameter Store. Note that in this example, SSM Parameter Store is used to store the configuration, but other services such as Amazon DynamoDB, AWS Secrets Manager, or AppConfig could be used as well.
4. After checking the runtime’s compliance, the Lambda Hook responds as follows:

   * Failure: if the Lambda runtime does not comply with the policy.
   * Success: if the Lambda runtime complies with the company’s policy.
5. Based on the Lambda Hook’s response, the CloudFormation deployment process will either continue or be stopped.

* **hook-lambda**: a directory containing all source code related to the CloudFormation Lambda Hook, including the Lambda function used for validation (Validation Lambda Function) and the CloudFormation template.
* **sample**: a directory containing sample code used to test the CloudFormation Lambda Hook.
* **deploy.sh**: a utility script used to deploy the solution via the AWS CLI.
* **cleanup.sh**: a utility script used to clean up the infrastructure — CloudFormation Hook on AWS via AWS CLI.
* **template.yml**: the CloudFormation template file (AWS CloudFormation Template) containing all the AWS resources used in this solution.

### Prerequisites:

You must meet the following prerequisites for the solution:

* AWS Account — or sign up to create and activate a new AWS account.
* Software required on the development machine:

  * Install the AWS Command Line Interface (AWS CLI) and configure it to connect to your AWS account.
  * Install Node.js and use a package manager like npm.
  * Appropriate AWS credentials to interact with the resources in your AWS account.

### Procedure:

#### Create the AWS Lambda Validation Function – Lambda Code

The CloudFormation Lambda Hook will interact with a specific Lambda function (referred to as the Validation Lambda for the rest of the article). This function will be invoked during the CloudFormation CREATE or UPDATE STACK operations involving Lambda functions. The goal is to check whether these Lambda functions are using a runtime that complies with AnyCompany's policies.

Here’s a detailed description of the steps the Validation Lambda function handler performs (the code is written in TypeScript):

The Validation Lambda first retrieves the value of an environment variable — this variable contains the parameter name in AWS Systems Manager Parameter Store where the list of compliant runtimes is stored.
Next, the function performs safety checks to ensure that only Lambda resources are considered. It also ensures that the Lambda function's Runtime property is explicitly defined.

Note: The two safety checks above could theoretically be skipped, as the Hook is already configured to interact only with Lambda resources, and the Lambda Runtime property is always required. However, these checks are kept to demonstrate how to extract information from the Lambda Hook event in your handler.

```typescript
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
Next, the **Validation Lambda** will retrieve the configuration parameter value from the AWS Systems Manager Parameter Store through a utility class called `ParameterStoreService`. In the example presented in this article, the value inside the configuration parameter is a list of strings, where each string represents a valid AWS Lambda runtime value, such as:

`nodejs22.x`, `nodejs20.x`, `python3.11`, `python3.10`, `java17`, `java11`, `dotnet6`

Once this value is retrieved, the Validation Lambda will check if the runtime of the Lambda resource being deployed is within the list of permitted runtimes. If the runtime is non-compliant, the function will return a correctly formatted response with `hookStatus = FAILURE`. If the runtime is compliant, the response will contain `hookStatus = SUCCESS`.

```typescript
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

For more information on possible response values of CloudFormation Lambda Hooks, you can refer to the provided link.

### Creating the Validation Lambda – Lambda Definition in CloudFormation

The **Validation Lambda** will be deployed through CloudFormation, alongside the CloudFormation Lambda Hook definition and the AWS Systems Manager Parameter Store parameter. Below is a fragment of the CloudFormation template describing the configuration for the Validation Lambda:

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

You need to assign the Lambda function an IAM (IAM role) with the appropriate permissions to access the parameter in the AWS Systems Manager Parameter Store.

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

### Creating the CloudFormation Lambda Hook

At this step, you will create a CloudFormation Lambda Hook that meets the following requirements:

* Activated during the **CREATE** and **UPDATE** phases of CloudFormation.
* Only considers resources of type `AWS::Lambda::Function`.
* Executes during the “**Pre-Provisioning**” phase of the CloudFormation template (i.e., before resources are initialized).
* Applies to both **Stack** and **Resource** operations.
* Calls the previously defined **Validation Lambda** function.

Here’s the corresponding definition in the CloudFormation template:

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

Please note that the CloudFormation template above references an IAM role, as the Hook needs the proper permissions to invoke the target Lambda function. Below is the definition for the IAM role:

```YAML
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

### Configuring Compliant Runtimes - Using Systems Manager Parameter Store

AWS Systems Manager Parameter Store is a secure, hierarchical service for managing configuration data and secrets, allowing users to store and retrieve data such as configuration, database strings, etc., in the form of parameter values.

In this specific example, we will utilize **Parameter Store** to store the list of allowed Lambda runtimes. This configuration value will be a `StringList` parameter containing a list of permitted runtimes, separated by commas. Below is a sample CloudFormation snippet defining the parameter:

```YAML
# Parameter Store Parameter
ConfigParameter:
  Type: AWS::SSM::Parameter
  Properties:
    Name: !Ref ParameterStoreParamName
    Type: StringList
    Value: !Ref ParameterStoreDefaultValue
    Description: "Configuration for Lambda Hook"
```

Note: The use of parameters in CloudFormation for the `Name` and `Value` attributes enables dynamic input when deploying the CloudFormation template.

### Deploying the Solution

To deploy this solution, you can use the `deploy.sh` script located in the root of the repository.

This script will perform the following steps:

* Compile and build the Validation Lambda function.
* Create an Amazon S3 bucket to store the CloudFormation template.
* Upload the CloudFormation template and Lambda source code to the S3 bucket.
* Deploy the CloudFormation template.

### Testing the Lambda Hook

To test the CloudFormation Lambda Hook, you can deploy a simple CloudFormation template containing a "Hello World" Lambda function. First, test the Lambda function configured with a compliant runtime. Then, modify the template to configure the Lambda function with a non-compliant runtime.

Below is the initial definition of the CloudFormation template for testing:

```YAML
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

Note that the runtime value is `nodejs22.x`, which is currently within the list of allowed runtimes. Therefore, the expectation is that the deployment of this Lambda function will succeed.

Deploy this template using the AWS CLI:

```bash
aws cloudformation deploy \
  --template-file sample/template.yml \
  --stack-name hook-test \
  --capabilities CAPABILITY_IAM
```

Check the **CloudFormation Console**:

As expected, the deployment will succeed. You can also verify that the CloudFormation Lambda Hook was triggered by checking **CloudWatch Logs**.

Now, edit the original template to set up a Lambda runtime not included in the list of permitted runtimes:

```YAML
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

Deploy this template using the AWS CLI with the same command and check the CloudFormation Console:

As expected, the deployment will fail. The CloudFormation Lambda Hook was triggered, and since the Lambda runtime is not in the list of allowed runtimes, the deployment was blocked. You can also verify that the Hook failed in **CloudWatch Logs**.

### Cleaning Up Resources:

To remove the resources related to the test template, you can run the `cleanup_sample.sh` script in the **sample** directory. This script will delete

To delete the resources related to the main solution described above (based on the AWS CloudFormation Lambda Hook), you can use the `cleanup.sh` script in the root folder of the repository. This script will perform the following tasks:

* Delete the CloudFormation Stack
* Empty the S3 Bucket used for the deployment of the Stack
* Delete the S3 Bucket

### Conclusion:

In this article, you learned how to implement CloudFormation Hooks to ensure compliance with Lambda runtime across your entire AWS infrastructure. By leveraging the power of Lambda Hooks, you learned how to create a preventive control mechanism to validate Lambda runtime configurations before deployment.

By enabling Lambda Hooks and deploying a custom Lambda function for validation, you have established an automated process that ensures only compliant runtimes are used in your organization's Lambda functions when creating or updating CloudFormation Stacks. This solution can be easily integrated with popular development tools like AWS CLI, AWS SAM, CI/CD pipelines, and AWS CDK, helping streamline control implementation in your current workflows and eliminating the need for manual checks or post-deployment fixes.

The validation method presented in this article is not limited to Lambda runtimes but can be extended to other AWS resources supported by CloudFormation, enabling you to enforce governance policies across various infrastructure components in your AWS environment.

### About the Authors:

#### Matteo Luigi Restelli

Matteo Luigi Restelli is a Senior Partner Solutions Architect at AWS. He primarily works with AWS consulting partners in Italy and specializes in areas such as Infrastructure as Code, Cloud Native App Development, and DevOps. Outside of work, he enjoys swimming, rock & roll music, and learning new things every day, particularly in the field of Computer Science.

#### Stella Hie

Stella Hie is a Senior Product Manager Technical responsible for AWS Infrastructure as Code. She focuses on proactive control and governance, with the goal of providing customers with the best experience when using AWS solutions securely. Outside of work, she enjoys mountain climbing, playing the piano, and attending live shows.