---
title: "Event 4"
date: 2025-11-17
weight: 1
chapter: false
pre: " <b> 4.4. </b> "
---

# Summary Report: “DevOps on AWS” Event

## Event Objectives

- Provide an overview of AI/ML concepts and their applications within the DevOps environment.

- Introduce and clarify the DevOps culture and core principles that enhance team collaboration and automation.

- Understand key DevOps performance metrics such as DORA metrics, MTTR, and deployment frequency to evaluate and improve processes.

- Support interns in acquiring essential knowledge and effectively applying it to real projects to complete tasks on schedule and with quality.

## Key Highlights

### 1. Recap AI/ML Session

- Summarize basic AI/ML concepts and practical applications in software development and DevOps.

- Explore AI/ML tools and services that support automation and data analytics in DevOps workflows.

### 2. DevOps Culture and Principles

- Introduce DevOps culture: cross-team collaboration, automation, and continuous improvement.

- Core DevOps principles that accelerate development speed and enhance software reliability.

### 3. Benefits and Key Metrics

- Importance of measuring DevOps performance through key metrics:

  - **DORA metrics**: Lead Time, Deployment Frequency, Change Failure Rate, Mean Time to Recovery (MTTR).

  - The meaning of these metrics and how to improve them for optimized development and operations processes.

### 4. Practical Applications for Interns

- Guide on applying DevOps and AI/ML knowledge to projects.

- Tips and best practices to help interns complete projects efficiently.

- Enhance teamwork and communication skills in the DevOps environment.

#### Pillar 1 — AWS DevOps Services - CI/CD Pipeline

- Present modern IAM architecture including users, roles, policies, emphasizing avoiding long-term credentials to reduce risk.

- Introduce IAM Identity Center with Single Sign-On (SSO) and permission sets for unified access management across multiple AWS accounts.

- Explain the roles of Service Control Policies (SCP) and permission boundaries in controlling permissions in multi-account environments to enhance security.

- Present use of Multi-Factor Authentication (MFA), credential rotation, and Access Analyzer to secure and accurately manage access.

- Demo illustrating IAM policy validation and access simulation for practical understanding of IAM in AWS.

#### Pillar 2 — Infrastructure as Code (IaC)

- Introduce monitoring and security tools like CloudTrail (API history logging), GuardDuty (threat detection), and Security Hub (aggregated security alerts).

- Practice detailed logging across multiple layers such as VPC Flow Logs, ALB/S3 logs to ensure comprehensive monitoring and early incident detection.

- Guide on using EventBridge to set up automated alerts and trigger response actions upon security events.

- Apply Detection-as-Code: build automated detection rules through code to increase automation and efficiency in security management.

#### Pillar 3 — Container Services on AWS

- Learn to protect networks and workloads on AWS via VPC segmentation and separation of private/public traffic to minimize public network risks.

- Compare and apply Security Groups and Network ACLs (NACLs) appropriately for traffic control.

- Introduce AWS services protecting application and network layers like AWS WAF (Web Application Firewall), Shield (DDoS protection), and Network Firewall.

- Present best security practices for EC2, ECS, and EKS workloads to maintain safe containerized and virtual server environments.

#### Pillar 4 — Monitoring & Observability

- Introduce AWS Key Management Service (KMS) for encryption key management, including policies, permissions, and key rotation to protect data.

- Present encryption techniques for data at rest (S3, EBS, RDS) and in transit (e.g., DynamoDB, other AWS services).

- Guide sensitive data management via Secrets Manager and Parameter Store with automated rotation to reduce risk.

- Discuss data classification and access control measures to ensure data is accessed only by authorized users and systems.

#### Pillar 5 — DevOps Best Practices & Case Studies

- Introduce Incident Response (IR) implementation following AWS best practices combined with automation for fast security incident handling.

- Present IR playbooks for common scenarios such as leaked IAM keys, public S3 buckets, or malware-infected EC2 instances, including detailed remediation steps.

- Demonstrate AWS Lambda and Step Functions usage for automated incident response to reduce detection and recovery times.

#### Summary & Q&A

- Recap the 5 main AWS security pillars and their practical applications to help enterprises build secure, efficient cloud systems.

- Discuss common security mistakes and challenges in Vietnamese enterprises and propose AWS-based solutions.

- Introduce learning paths and certifications in security such as AWS Security Specialty and Solutions Architect – Professional (SA Pro) for skill advancement.

### What I Learned

#### DevOps Culture and Principles
- Deepened understanding of DevOps culture and principles that foster cross-team collaboration, automation, and continuous improvement.
- Grasped the importance of measuring DevOps effectiveness using key metrics like DORA metrics and MTTR to improve project quality.

#### AI/ML in DevOps
- Identified AI/ML applications that enable automation and data analysis in DevOps pipelines.
- Learned how AI/ML supports pipeline optimization and rapid incident detection.

#### AWS DevOps Services and Security
- Studied modern IAM architecture and secure access management with IAM Identity Center, SCPs, and permission boundaries.
- Practiced MFA, credential rotation, and Access Analyzer to enhance account and service security.
- Participated in demos for policy validation and access simulation within AWS IAM.

#### Continuous Monitoring and Detection
- Applied AWS tools like CloudTrail, GuardDuty, and Security Hub for comprehensive monitoring and security incident detection.
- Configured multi-layer logging and automated alerting with EventBridge for rapid incident response.

#### Network and Container Management
- Learned VPC segmentation, and effective use of Security Groups and NACLs to safeguard EC2, ECS, and EKS workloads.
- Used AWS WAF, Shield, and Network Firewall for layered network and application protection.

#### Data Protection and Encryption
- Utilized AWS KMS for encryption key management, securing data at rest and in transit.
- Managed secrets and automated rotation with Secrets Manager and Parameter Store.
- Applied data classification and strict access controls.

#### Automated Incident Response
- Built Incident Response playbooks covering detection, analysis, containment, and recovery steps.
- Automated incident handling using AWS Lambda and Step Functions to minimize damage.
- Learned to manage typical cases like IAM key leaks, public S3 buckets, and infected EC2 instances.

---

### Practical Applications

- Applied IAM and access management knowledge in real project contexts.
- Established monitoring and automated alert systems to enhance AWS environment security.
- Integrated automated incident response to save time and reduce risks.
- Implemented data encryption and secret management following best security practices.

---

### Event Experience

- Learned directly from AWS experts and engaged in practical demos on security and DevOps.
- Connected and shared knowledge with the DevOps and security community, expanding professional expertise.
- Received practical advice and guidance tailored for personal work and projects.

---

### Conclusion

The “DevOps on AWS” event gave me a comprehensive understanding of DevOps culture, AI/ML applications, and AWS security best practices. Knowledge of IAM, monitoring, data protection, and incident response forms a critical foundation that I will apply in projects, especially supporting interns to complete work efficiently and securely.
