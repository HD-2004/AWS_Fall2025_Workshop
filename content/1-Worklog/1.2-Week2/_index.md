---
title: "Week 2 Worklog"
date: 2025-09-16
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---
### Week 2 Objectives:

* Studied AWS Networking Services.
* Learn the theory and practice of basic EC2.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Study the basic EC2 theory: <br>&emsp; + Instance types <br>&emsp; + AMI <br>&emsp; + EBS <br> - remote SSH into EC2. <br> - Study Elastic IP. <br> - **Practice:** <br>&emsp; + Create EC2 instance <br>&emsp; + Connect SSH <br> emsp;                                                                                                   | 09/14/2025 | 09/14/2025      | <https://000004.awsstudygroup.com/vi>
| 3   | - Studied AWS Networking Services <br>&emsp; + Amazon Virtual Private Cloud ( VPC ); + VPC Peering & Transit Gateway <br>&emsp; + VPN & Direct Connect <br>&emsp; + Elastic Load Balancing <br>&emsp                                              | 09/15/2025 | 09/17/2025      | <https://www.youtube.com/watch?v=O9Ac_vGHquM&list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i&index=25> |
| 4   | - Studied AWS Networking Services <br>&emsp; + Amazon Virtual Private Cloud ( VPC ); + VPC Peering & Transit Gateway <br>&emsp; + VPN & Direct Connect <br>&emsp; + Elastic Load Balancing <br>&emsp | 09/15/2025 | 09/17/2025      | <https://000003.awsstudygroup.com/vi> |
| 5   | - Learn basic EC2: <br>&emsp; + Instance types <br>&emsp; + AMI <br>&emsp; + EBS <br>&emsp; + ... <br> - SSH connection methods to EC2 <br> - Learn about Elastic IP   <br>                            | 09/16/2025 | 09/17/2025      | <https://000003.awsstudygroup.com/vi/1-introduce> |
| 6   | - Group meeting about project ideas and writing worklog <br>&emsp;                                                                                    | 09/18/2025 | 09/18/2025      |


### Week 2 Achievements:

* Study the basic EC2 theory:: 
  * Instance types: different virtual hardware configurations provided by AWS to run EC2 instances, each optimized for specific use cases.
  * AMI: a template to launch EC2 instances, including the operating system, software, and volume configuration.
  * Ways to remote SSH into EC2: using an SSH Client with a key pair, EC2 Instance Connect via browser, Session Manager without opening port 22, and PuTTY on Windows with a .ppk file. 
  * Elastic IP: a static, public IPv4 address allocated to an AWS account and attachable/detachable to EC2 instances, ensuring a fixed public IP for stable external access.

* Successfully created and connected to EC2 via SSH.

* Study AWS Networking Services:
  * Amazon Virtual Private Cloud (VPC): a customizable virtual network in AWS Cloud that allows you to create an isolated and secure networking environment.
  * VPC Peering & Transit Gateway: VPC Peering directly connects two VPCs via private IPs (simple but no transitive routing), while Transit Gateway acts as a central hub to connect multiple VPCs and on-premises networks, supporting transitive routing, scalability, and centralized management.
  * VPN & Direct Connect: VPN connects on-premises networks to AWS over the Internet via encrypted tunnels (easy to deploy but Internet-dependent), while Direct Connect provides a dedicated connection with higher stability, bandwidth, and lower latency, though costlier and slower to set up.
  * Elastic Load Balancing (ELB): automatically distributes traffic across multiple resources to improve availability, scalability, and fault tolerance, with ALB (HTTP/HTTPS), NLB (high-performance TCP/UDP), and GWLB (virtual appliances).