---
title: "Blog 1"
date: 2025-09-10
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Visually build telephony applications with AWS Step Functions

#### Author: Reynaldo Hidalgo | March 17, 2025 | in Amazon Chime SDK, Application Services, Architecture, AWS Step Functions, Best Practices, Business Productivity, Contact Center, Technical How-to

Developers face several challenges when building telephony applications: managing unpredictable user responses, handling disconnects, processing incorrect input data, and resolving errors. These challenges extend development cycles and result in unstable applications that fail to meet user expectations.

This article explains how Amazon Web Services (AWS) Step Functions, combined with the Amazon Chime SDK Public Switched Telephone Network (PSTN) audio service, provides a solution to overcome these challenges.

### Solution Overview

To illustrate the solution, we built a sample telephony application that allows business owners to manage customer calls through a dedicated business phone number. This solution helps small business owners separate personal and business communications while managing all calls from their existing phone.

The beta version of this sample application offers six core call flows:

1. **During business hours**: Forward incoming customer calls to the business owner
2. **After business hours**: Allow customers to leave voicemail
3. **Retrieve voicemail**: Allow the business owner to access customer voicemail
4. **Business caller ID**: Allow the business owner to call customers using the business phone number
5. **Schedule a call**: Allow the business owner to schedule calls with customers for later in the day
6. **Automated calling**: Automatically initiate scheduled calls between the business owner and the customer

Using Workflow Studio, we built a Step Functions workflow that handles all six call flows and manages unexpected scenarios.

### How it Works

AWS Step Functions enables flexible, visual workflow design through pre-built components and error-handling rules. This creates workflows that include event-driven states, allowing the input, processing, and output of JavaScript Object Notation (JSON) formatted messages. The PSTN audio service streamlines telephony applications through a serverless approach using a request/response programming model. This service invokes AWS Lambda functions with Events and waits for Action responses, both of which are in a pre-defined JSON format. This common JSON format allows seamless integration between the PSTN audio service and Step Functions, leading us to design a serverless architecture (Figure 2) that allows two-way JSON message exchange between the two services.

### Key Components:

* **eventRouter**: A Lambda function that manages the JSON message exchange
* **appWorkflow**: Step functions that implement the call flow logic
* **actionsQueue**: An Amazon Simple Queue Service (Amazon SQS) queue that stores response actions

### Architecture Flow:

1. The PSTN audio service receives an incoming call
2. The service sends a NEW_INBOUND_CALL event to the eventRouter
3. The eventRouter creates the actionsQueue
4. The eventRouter executes the appWorkflow asynchronously with event data
5. The eventRouter starts sending long notifications from the actionsQueue, waiting for the next action notification
6. The appWorkflow processes the event data in JSON format, calculating the next action
7. The appWorkflow queues the next action using Amazon SQS SendMessage API with the Wait for Callback integration model, using a task token to pause the workflow until the next event call is received
8. The eventRouter retrieves and removes the action from the actionsQueue
9. The eventRouter returns the action to the PSTN audio service

### Observations:

* The eventRouter logic is generic and independent of specific calls and other Step Function workflows
* The eventRouter queries an environment variable to determine which workflow to call.
  The actionQueue and appWorkflow instances persist throughout each call.
* The eventRouter is responsible for creating and deleting each actionsQueue.
* The appWorkflow instances are created by the eventRouter at the start of each call.
* The appWorkflow instances complete execution when all call participants hang up.

### Building Your Telephony Application:

#### Prerequisites

* Familiarity with developing workflows in Step Functions Workflow Studio
* Access to the AWS Management Console

### Deployment Guide

* Create a dedicated Step Functions workflow for each telephony application

* Design and deploy the workflow using Workflow Studio

* Use the Standard workflow type to support long-duration calls

* Update the environment variable "CallFlowsDIDMap" in the eventRouter Lambda function to map phone numbers to their corresponding workflows’ Amazon Resource Names (ARN)

* Set the workflow variable in the "Initialization" state tab (Figure 3). The eventRouter function automatically sets the "QueueUrl", and adding other variables here eliminates the need for external storage

* Configure Choice state rules to route calls based on conditions. Rules one to three (Figure 4) manage call routing based on inbound/outbound direction and the caller/customer identity, while the default rule handles unexpected scenarios.

* Configure the SQS state: SendMessage (Figure 5) to guide the PSTN audio service’s next action by:
  Formatting the message content to match supported actions for the PSTN audio service
  Setting TransactionAttributes to pass the “WaitToken” and “QueueUrl” values throughout the call
  Triggering the "Wait for Callback with Task Token" integration model

* Leverage AWS service integration states to interact with other AWS services directly from the workflow.

Example: Use the DynamoDB PutItem state (Figure 6) to store Amazon Simple Storage Service (Amazon S3) recorded files, including the name and bucket key, in DynamoDB.

* Use JSONata expressions (Figure 7) to minimize the number of Lambda functions.

Example: For Amazon EventBridge scheduling, calculate time expressions using the JSONata functions [$fromMillis(), $millis(), number()] and concatenate strings to handle customer call scheduling.

### Key Benefits

This approach to building telephony applications offers several advantages:

1. Workflow designer based on a visual process
2. Self-recording call flow logic
3. Version management and publishing
4. Native integration with AWS Services
5. Visual logs and debugging for each call
6. Automatic scaling
7. Pay-as-you-go pricing

### Solution Deployment

The following steps allow you to deploy the sample telephony application along with a serverless architecture (Figure 2).

#### Prerequisites

1. Access to the AWS Management Console
2. Install Node.js and npm
3. Install and configure the AWS Command Line Interface (AWS CLI)

#### Deployment Guide:

The AWS Cloud Development Kit (CDK) project in AWS’s GitHub repository will deploy the following resources:

* **phoneNumberBusiness** – The business phone number provided for the sample application
* **sipMediaApp** – The SIP media application that routes calls to - lambdaProcessPSTNAudioServiceCalls
* **sipRule** – SIP rule that forwards calls from phoneNumberBusiness to sipMediaApp
* **stepfunctionBusinessProxyWorkflow** – Step Functions workflow for the sample app
* **roleStepfuntionBusinessProxyWorkflow** – IAM role for - stepfunctionBusinessProxyWorkflow
* **lambdaProcessPSTNAudioServiceCalls** – Lambda function to process the call
* **roleLambdaProcessPSTNAudioServiceCalls** – IAM role for - lambdaProcessPSTNAudioServiceCalls
* **dynamoDBTableBusinessVoicemails** – DynamoDB table to store customer voicemail
* **s3BucketApp** – S3 bucket to store system recordings and customer voicemails
* **s3BucketPolicy** – IAM policy granting PSTN audio service access to - s3BucketApp
* **lambdaOutboundCall** – Lambda function to schedule calls with customers
* **roleLambdaOutboundCall** – IAM role for lambdaOutboundCall
* **roleEventBridgeLambdaCall** – IAM role that allows EventBridge service to invoke lambdaOutboundCall

### Follow these steps to deploy the CDK stack:

1. Clone the repository

```bash
git clone https://github.com/aws-samples/amazon-chime-sdk-visual-media-applications  
cd amazon-chime-sdk-visual-media-applications  
npm install  
```

2. Bootstrap the stack

```bash
#default AWS CLI credentials are used, otherwise use the –-profile parameter  
#provide the <account-id> and <region> to deploy this stack  
cdk bootstrap aws://<account-id>/<region>  
```

3. Deploy the stack

```bash
#default AWS CLI credentials are used, otherwise use the –-profile parameter  
#personalNumber: the personal phone number of the business owner in E.164 format  
#businessAreaCode: the United States area code used to provision the business number  
cdk deploy –-context personalNumber=+1NPAXXXXXXX –-context businessAreaCode=NPA  
```

Call the provided phone number to test the sample application. Optionally, edit the workflow to update the business name and business hours in the "Initialization" Task state in the Variables tab.

### Clean Up Resources

To clean up this test, run:

```bash 
cdk destroy  
```

This blog demonstrates how combining AWS Step Functions and the Amazon Chime SDK’s PSTN audio service simplifies the development of reliable telephony applications through visual workflow design and error management. We provided a sample application, implementing six core features for business telephony, illustrating how this solution effectively manages multiple conditional paths and exceptions such as disconnects and invalid inputs.
The serverless architecture created enables seamless integration between the two services via JSON-based communication, providing automatic scaling

**TAGS:** best practices, customer engagement, Messaging and Targeting

**About the Author**

**Reynaldo Hidalgo**

Reynaldo is a Cloud Solutions Architect at AWS, with over 20 years of experience in software, database, and business intelligence decryption, as well as call center/telephony infrastructure and applications. He is also a co-founder of PrimeVoiX, a startup focused on international contact center solutions.
