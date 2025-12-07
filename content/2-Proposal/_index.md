---
title: "Proposal"
date: 2025-09-16
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
This section summarizes the contents of the workshop you **plan** to conduct.

# Studying English Website

### 1. Executive Summary  
The Studying English Website is designed for learners aiming to improve their vocabulary, grammar, and daily communication skills. The platform leverages AWS Serverless services to provide learning time monitoring, predictive analysis of learners’ abilities to offer learning policies from basic to advanced levels, while minimizing costs.

### 2. Problem Statement  
*Current Problem*  
English is an essential language for work and daily life. However, learners currently lack space and an environment for practice, especially for communication.

*Solution*  
To address the lack of an English practice environment and support learners in improving vocabulary, grammar, and communication skills, we propose building the Studying English Website on the AWS serverless platform. It enables personalized learning based on user data, integrates listening-speaking exercises and instructional videos with learner recordings stored on S3 and MediaConvert, tracks and analyzes learning progress via AWS Lambda, ensures security and user management through Cognito, IAM, and Secrets Manager, and rapidly deploys a cost-effective web interface using AWS Amplify, providing a flexible, safe, and effective learning environment while helping administrators improve teaching methods based on real data.

*Benefits and Return on Investment (ROI)*  
The Studying English Website helps learners enhance their English skills in a personalized and flexible way, reducing time and cost compared to traditional learning methods. It also provides learning progress analytics to administrators for optimized teaching methods. With low AWS infrastructure costs (~6.45 USD/month), the project has a fast ROI potential through increased learning efficiency and expanded user base, while creating valuable data for AI projects and long-term analytics.

### 3. Solution Architecture  
The architecture of the Studying English Website is based on the AWS serverless platform, using S3 to store raw and processed data, Amplify Gen 2 for web interface deployment, MediaConvert for video and audio conversion, Route53 for DNS and routing management, Cognito for user authentication and management, Secrets Manager for sensitive information security, IAM for access control, Lambda for event-driven serverless logic, and WAF to protect the application from attacks, creating a flexible, personalized, secure, and scalable English learning system.

![Studying English Website Architecture](/images/2-Proposal/architecture.jpeg)

*AWS Services Used*  
- *AWS S3*: Store raw data (data lake) and processed data (2 buckets)  
- *AWS Amplify Gen 2*: Host the web interface  
- *AWS MediaConvert*: Convert video/audio  
- *AWS Route53*: Manage DNS and routing  
- *AWS Cognito*: Authenticate and manage users  
- *AWS Secrets Manager*: Store and secure sensitive information  
- *AWS IAM*: Manage AWS access permissions  
- *AWS Lambda*: Run serverless code triggered by events  
- *AWS WAF*: Protect the web application from attacks  

*Component Design*  
- *Data Ingestion*: Data from users and sources is sent to AWS Lambda, which triggers processing workflows.  
- *Data Storage*: Raw and processed data are stored in two separate S3 buckets, forming a data lake and ready-to-analyze data repository.  
- *Data Processing*: AWS Lambda handles serverless events, MediaConvert converts video/audio, and data is indexed.  
- *Web Interface*: AWS Amplify Gen 2 hosts a Next.js application providing dashboards, real-time analytics, and user data access.  
- *User Management*: Amazon Cognito handles authentication and user access management, combined with AWS IAM for service access control, securing sensitive information via AWS Secrets Manager, and protecting the entire application with AWS WAF. DNS and routing are managed by Route53.

### 4. Technical Deployment  
*Deployment Phases*  
The project consists of two parts — building the Studying English Website — each with four phases:  
1. *Research and Architecture Design*: Study and design AWS Serverless architecture (1 month before internship).  
2. *Cost Calculation and Feasibility Check*: Use AWS Pricing Calculator to estimate infrastructure costs and adjust services to ensure feasibility and cost-efficiency (Month 1).  
3. *Architecture Adjustment for Cost/Performance Optimization*: Refine services (e.g., optimize Lambda, MediaConvert, Amplify) and data workflows for maximum efficiency (Month 2).  
4. *Development, Testing, Deployment*: Deploy AWS services using CDK/SDK, develop Next.js interface on Amplify, test the system, and put it into operation (Month 2–3).

*Technical Requirements*  
The system requires a stable internet connection to operate AWS services, including storing and retrieving data on S3, serverless data processing via Lambda, video/audio conversion with MediaConvert, deploying Next.js web interface on Amplify Gen 2, DNS and routing management via Route53, user authentication and access control with Cognito, sensitive information security via Secrets Manager, service access control via IAM, WAF application protection, as well as supporting data analytics and real-time dashboards.

### 5. Roadmap & Milestones  
- *Before Internship (Month 0)*: Study plan preparation  
- *Internship (Month 1–3)*:  
    - Month 1: Learn AWS and upgrade hardware  
    - Month 2: Learn deployment, plan and design architecture  
    - Month 3: Deploy, test, and launch  
- *Post Deployment*: Research potential development and new features  

### 6. Budget Estimation  
See costs on [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=6a8b0970827e19a863c2bfc704a13b136f700574)  
Or download [budget estimation file](../attachments/budget_estimation.pdf).

*Infrastructure Costs*  
- AWS S3: 0.15 USD/month (6 GB, 2 buckets, 2,100 requests)  
- AWS Amplify Gen 2: 0.35 USD/month (256 MB, 500 ms request)  
- AWS MediaConvert: 0.05 USD/month (small video conversion)  
- AWS Route53: 0.50 USD/month (1 domain, 1 million queries)  
- AWS Cognito: 0.00 USD/month (5 Free Tier users)  
- AWS Secrets Manager: 0.40 USD/month (10 secrets)  
- AWS IAM: 0 USD/month  
- AWS Lambda: 0.00 USD/month (1,000 requests, 512 MB RAM)  
- AWS WAF: 5.00 USD/month (1 basic Web ACL)

*Total*: 6.45 USD/month, ~77.4 USD/12 months  

### 7. Risk Assessment  
*Risk Matrix*  
- Server downtime: High impact, medium probability  
- Budget overrun: Medium impact, high probability  

*Mitigation Strategy*  
- Costs: Use AWS Budget for alerts, optimize services  

*Contingency Plan*  
- Revert to manual data collection if AWS services fail.

### 8. Expected Outcomes  
*Technical Improvement*: Real-time data and analytics replace manual processes. Scalable to 10–15 stations.  
*Long-term Value*: One-year data platform for AI research, reusable for future projects.