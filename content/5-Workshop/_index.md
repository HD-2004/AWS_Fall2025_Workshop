---
title: "Workshop"
date: 2025-09-10
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# English Journey

#### Overview

**English Journey** is an innovative web application designed to help users learn English vocabulary in a structured and interactive way. This platform leverages various AWS services to provide a smooth learning experience, allowing users to track their progress, engage with dynamic content, and receive personalized feedback.

#### Key Features:

**User Authentication:** Using Amazon Cognito, users can register, log in, and securely access their learning materials.

**Interactive Learning Modules:** The app offers various interactive lessons to help users expand their vocabulary.

**Progress Tracking:** Users can track their progress and completion of vocabulary lessons, with detailed reports generated through AWS Lambda and DynamoDB.

**Notifications:** Real-time notifications via SNS will inform users about new lessons, progress milestones, account updates, or other updates.

**Media Conversion:** If the platform includes video or audio content, AWS MediaConvert will handle media processing to ensure compatibility and smooth playback.

**Content Storage:** All learning materials are securely stored in Amazon S3 with proper access control.

**Web Security:** To protect the platform, AWS WAF ensures the application is protected from common web threats.

**Monitoring and Alerts:** AWS CloudWatch is used to monitor platform performance, with alerts configured for potential issues.

#### Technologies Used:

**Frontend:** Built using modern web technologies, ensuring a smooth and responsive user experience.

**Backend:** Powered by AWS services like Lambda and DynamoDB, ensuring scalability and performance.

**Storage:** All data and media content are securely stored in Amazon S3.

#### Content

1. [Workshop overview](5.1-Workshop-overview/)
2. [Prerequiste](5.2-Prerequiste/)
3. [Create Amplify](5.3-Create%20Amplify/)
4. [MediaConvert](5.4-Create%20MediaConvert/)
5. [SNS](5.5-Create%20SNS/)
6. [CloudWatch](5.6-Create%20Cloudwatch/)
7. [IAM Roles - Policies](5.7-Create%20IAM%20Roles-Policies/)
8. [Clean up](5.8-Cleanup/)