---
title: "Week 3 Worklog"
date: 2025-09-16
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---
### Week 3 Objectives:

* Learn about Compute VM services on AWS.
* Practice with Compute VM services on AWS.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Learn about Compute VM services on AWS. <br>&emsp; + Amazon Elastic Compute Cloud (EC2) <br>&emsp; + Amazon Lightsail <br>&emsp; + Amazon EFS / FSX <br>&emsp; + AWS Application Migration Service (MGN)regulations                                                                                                   | 09/15/2025 | 09/16/2025      | <https://www.youtube.com/watch?v=-t5h4N6vfBs&list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i&index=72>
| 3   | - Learn about Compute VM services on AWS. <br>&emsp; + Amazon Elastic Compute Cloud (EC2) <br>&emsp; + Amazon Lightsail <br>&emsp; + Amazon EFS / FSX <br>&emsp; + AWS Application Migration Service (MGN)regulations                                              | 09/15/2025 | 09/16/2025      | <https://www.youtube.com/playlist?list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i> |
| 4   | - **Practice:** <br>&emsp; + Deploy AWS Backup to the System <br>&emsp; + Using AWS File Storage Gateway <br>&emsp; + Create SH3 Bucket <br>&emsp; + Using Autoscaling Group | 09/17/2025 | 09/19/2025      | <https://000013.awsstudygroup.com/vi> |
| 5   |  - **Practice:** <br>&emsp; + Deploy AWS Backup to the System <br>&emsp; + Using AWS File Storage Gateway <br>&emsp; + Create SH3 Bucket <br>&emsp; + Using Autoscaling Group                            | 09/17/2025 | 09/19/2025      | <https://000024.awsstudygroup.com> |
| 6   |  - **Practice:** <br>&emsp; + Deploy AWS Backup to the System <br>&emsp; + Using AWS File Storage Gateway <br>&emsp; + Create SH3 Bucket <br>&emsp; + Using Autoscaling Group                                                                                     | 09/17/2025 | 09/19/2025      | <https://000024.awsstudygroup.com> |


### Week 3 Achievements:

* Understood AWS Compute VM services: 
  * Amazon Elastic Compute Cloud (EC2): Amazon EC2 is similar to a virtual or traditional physical server. It provides fast provisioning, strong resource scalability, and flexibility.
  * Amazon Lightsail: a low-cost compute service (pricing starts at $3.5/month). Each Lightsail instance includes a data transfer allocation (cheaper than EC2), suitable for light workloads, dev/test environments, and applications that do not require high CPU usage continuously for more than 2 hours per day.
  * Amazon EFS / FSx:
  + EFS (Elastic File System): allows creation of NFSv4 network volumes that can be mounted to multiple EC2 instances simultaneously, with storage scaling up to petabytes. EFS only supports Linux and charges based on used storage. It can be mounted to on-premises environments via Direct Connect or VPN.
  + FSx: allows creation of NTFS volumes mountable to multiple EC2 instances using the SMB protocol, supports Windows and Linux, and charges based on used storage. 
  * AWS Application Migration Service: used to migrate and replicate servers for building Disaster Recovery sites, continuously copying source physical or virtual servers to EC2 instances in AWS (asynchronously or synchronously).

* Successfully deployed AWS Backup, File Storage Gateway, and Auto Scaling Group..