---
title : "Introduction"
date: 2025-09-10
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### Project Introduction
This workshop presents the architecture and implementation of **English Journey**,  
a vocabulary-learning web application built on **AWS**.

The application allows students to:

- sign up and sign in securely,
- take a **level placement test** (A1–C1) before learning,
- practise vocabulary and quizzes,
- track their own learning progress.

On the AWS side, the project demonstrates how to combine several managed services:

- **AWS Amplify** as the central platform for the web app backend and hosting,
- **Amazon Cognito** for authentication,
- **AWS Lambda** for backend logic (Level test, Quiz, Vocabulary),
- **Amazon DynamoDB** for application data,
- **Amazon SES** for sending email notifications and alerts to learners (Account Verification),
- **Amazon CloudWatch** for logs, metrics,
- **AWS WAF** for basic web application protection,
- and **IAM Roles & Policies** to control access between all components.

#### Workshop Objectives
By the end of this workshop, a reader should be able to:

1. Understand the **overall architecture** of the English Journey web app on AWS.
2. Explain the role of **Amplify** and how it orchestrates Cognito, Lambda, DynamoDB and S3.
3. Describe how the **level-test feature** connects frontend, Lambda and DynamoDB.
4. Understand how **notifications** and **system alerts** are delivered via email using Amazon SES.
5. Recognise the importance of **CloudWatch** and **IAM** for monitoring and security.

#### Workshop Overview

This project leverages AWS services to build and deploy the application:

    AWS Amplify: A full-stack hosting service that enables quick and easy deployment of applications.

    AWS Lambda: Handles application tasks and logic without the need to manage servers, saving costs and resources.

    Amazon DynamoDB: A NoSQL database used to store user data, vocabulary, and learning results.

    Amazon S3: Stores learning materials (videos, audio, images) to support the learning process.

    Amazon CloudWatch: Monitors the performance and operation of the application, providing logs and alerts in case of issues.


!<img src="/images/5-Workshop/5.1-Workshop-overview/architecture2.png" alt="Overview" width="600">
