---
title: "Blog 2"
date: 2025-09-10
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
# Visualize and gain insights into your VPC with Amazon Q in Amazon QuickSight

Authors: Diego Hernandez, Opeoluwa Victor Babasanmi, and Rashmiman Ray | February 27, 2025 | in Amazon Q, Amazon QuickSight, Amazon VPC, Analytics, Networking & Content Delivery

### Introduction

AWS services generate a wealth of data in the form of logs and metrics, enabling you to build comprehensive dashboards to extract valuable insights, including the ability to observe detailed connection patterns in a Virtual Private Cloud (VPC). This article illustrates how Amazon QuickSight and Amazon Q in QuickSight allow you to visualize data from any source. We will focus on visualizing connection patterns in a VPC, highlighting the benefits of QuickSight for users at various levels of technical expertise.

Amazon QuickSight is a fully managed, scalable cloud data analytics and business intelligence (BI) service that enables users to create and share interactive dashboards, analyze data, and gain insights through visualizations. On April 30, 2024, AWS announced the general availability (GA) of Amazon Q in QuickSight, a feature that extends QuickSight by integrating generative AI to visualize data via natural language queries.

In the following sections, we will explore two use cases — where data is collected from different sources, processed and enriched, and then visualized using QuickSight. These examples demonstrate how Amazon Q can quickly generate visual charts from natural language queries, showcasing the convenience and efficiency of using QuickSight and Q to build exploratory and analytical dashboards.

### Overview

This article introduces two use cases that illustrate how to visualize VPC Flow Logs data with Amazon Q in QuickSight. These examples are flexible and can be applied regardless of the data source or solution used to collect and enrich the data. While these cases are tailored for the article, you can refer to and customize them for other practical scenarios in your system.

#### Use Case #1: VPC Flow Logs enriched with Security Group and Cost Tag information

VPC Flow Logs generate records with predefined fields. AWS Lambda can augment these logs by adding additional fields, creating a custom view of VPC traffic flows.
In the architecture shown in Figure 1, VPC Flow Logs are created and sent to Amazon Data Firehose. The logs are then forwarded to Lambda for enrichment. Lambda adds attributes to the flow, such as the security group and cost tags for Amazon Elastic Compute Cloud (Amazon EC2) instances in the VPC Flow Log, before delivering them to Amazon S3. The AWS Glue Catalog stores table definitions for the enriched VPC Flow Logs, allowing Amazon Athena to query the data efficiently. Finally, QuickSight visualizes the data from Athena. The data flow is as follows:

Detailed Data Flow:

* Enable VPC Flow Logs on the existing VPC via an AWS CloudFormation template.
* Log records are sent to Amazon Data Firehose.
* Data Firehose is configured to forward metrics and logs to Amazon CloudWatch for troubleshooting.
* Data Firehose triggers Lambda, which enriches the Flow Logs with attributes such as security groups and cost tags for Amazon EC2.
* Lambda is also configured to send metrics and logs to CloudWatch for monitoring and troubleshooting.
* Enriched VPC Flow Logs data is stored in an S3 bucket.
* AWS Glue scans the S3 bucket to create a data catalog for querying.
* Amazon Athena queries the cataloged data from AWS Glue.
* Amazon QuickSight uses Athena and Amazon S3 as the data source for the dataset. A blank analysis is pre-configured to start visualizing the Flow Logs.
* Amazon Q is enabled in the analysis through a Q topic. Q topic allows QuickSight to interpret natural language queries, making it easier to auto-generate charts and visualize the data.

#### Use Case #2: VPC Flow Logs and Amazon Route 53 Resolver Logs

VPC Flow Logs and Amazon Route 53 Resolver Logs provide complementary information about network traffic within your VPC.

In the architecture shown in Figure 2, VPC Flow Logs capture detailed information about traffic between different IP addresses within the VPC but do not include domain names. When combined with Route 53 Resolver Logs, you can gain a more comprehensive view of network traffic within the VPC, including the IP addresses and the domain names that servers or applications are contacting.

* VPC Flow Logs are enabled on the existing VPC via CloudFormation template.
* VPC Flow Logs are sent to Amazon S3 for storage.
* Route 53 Resolver Logs are also created and linked to the corresponding VPC where VPC Flow Logs are active.
* Route 53 Resolver Logs are sent to Amazon S3.
* AWS Lambda is used to run Athena queries, combining data from VPC Flow Logs and Route 53 Resolver Logs.
* Both types of logs are merged based on IP addresses on a daily basis.
* This process links network traffic in VPC Flow Logs to domain names. The analysis is updated daily, and any changes in the mapping between domain and IP are automatically reflected.
* Amazon QuickSight uses Athena and Amazon S3 as the data source for the dataset.
* A blank analysis is pre-configured to start visualizing the VPC Flow Logs data.
* Amazon Q is enabled in the analysis through a Q topic.
  Q topic allows QuickSight to interpret and process natural language queries, making it easier to generate charts and visualize the data.

This article provides a detailed walkthrough for the first use case. To learn more about both use cases, including the deployment steps and related CloudFormation templates, you can access the AWS Samples repository on GitHub.

### Prerequisites:

Before starting, follow these steps:

* Deploy one of the CloudFormation templates provided in the aws-samples GitHub repository. Each example in this repository has its own detailed documentation stored directly on the aws-samples GitHub.
* Ensure that your QuickSight user account has a PRO role to access the Amazon Q features demonstrated in this article.
  This article provides two demonstration examples. In the future, AWS may add additional examples to the aws-samples repository. The walkthrough steps in this article can be applied to all examples provided.

### Detailed Guide: Extract insights from your data with Q in QuickSight

1. Review the Q topics configured in the deployed CloudFormation template. Q topics simplify data visualization queries by setting up synonyms for fields in natural language queries.
2. Query Q in QuickSight to explore insights from your VPC Flow Log data.

### Review Q Topics in QuickSight

In this section, we will review the Q topic automatically deployed through the CloudFormation template.

1. Open the AWS Console and navigate to Amazon QuickSight.
2. In the left navigation pane, select **Topics**.
3. Select the corresponding topic name.

Select the tabs at the top, navigate to **Data**, then to **DATA FIELDS**. QuickSight automatically populates the data fields from the dataset into the data fields. It will also populate synonyms for corresponding fields where possible. In this example, synonyms are defined in the CloudFormation template. Synonyms help Amazon Q map your queries to fields in the dataset. You can further customize these data fields to match your organization’s terminology, which will provide more meaningful and accurate responses from Amazon Q. Refresh the Q topic indexes to reflect these changes in future analyses.

Another useful customization is **Field Value Synonyms**. This feature allows you to add synonyms for values in your VPC Flow Logs data. In the following example, synonyms were added for IP protocol numbers. This enables Q to interpret VPC Flow Logs queries using terms like icmp, tcp, udp.

### Query Q for insights from VPC Flow Log data

We ask Q in QuickSight a question related to the VPC Flow Log data. Select "Ask a question" at the top center of the QuickSight screen.

As shown in Figure 7 below, we asked Amazon Q: "Which cost tags have the most megabytes in October 2024 by hour?" Amazon Q processed our question and, using the defined synonyms, interpreted the question as "Total megabytes per hour Starting Date and Cost Tag for Starting Date in October 2024." Amazon Q provided an answer to the question, supported by detailed visual data to illustrate the answer.

#### Guide: Fill QuickSight Analysis with Amazon Q

Link the Q topic to QuickSight Analysis
Fill QuickSight Analysis by adding visuals generated by Amazon Q. This article provides a few example queries.

1. Link QuickSight Analysis to the Q topic

After reviewing the Q topics, we will explore the Analysis. Analysis is a collection of visuals, interactive tables, and insights into data created and organized across one or more sheets to effectively explore and present the data.

We create the first visuals in Analysis. The CloudFormation template has created a blank analysis linked to the dataset containing VPC Flow Logs data from Athena. Select the name of the analysis to get started, as shown in Figure 8.

To run a query on Amazon Q, link the Q topic to the analysis. In the top center toolbar, select the three vertical dots (⋮) next to **Build visual** and select **Link Topic** from the menu. Enable the **Link Topic to Build Visual** and **Ask & Answer** options, then choose the topic deployed by CloudFormation from the dropdown menu. Select **APPLY CHANGES**, as illustrated in Figure 9.

2. Create content for QuickSight Analysis

In the top center toolbar, select **Build visual**. The **Build a visual** panel will appear on the right-hand side. Start by typing your first natural language query and select **BUILD**. We’ve provided four sample questions to get you started for the first use case. QuickSight documentation offers the types of questions that Q supports along with more sample queries. QuickSight Enterprise Edition is a prerequisite to ask questions using Amazon Q.

Sample questions:

* Display top source and destination IPs by gigabyte
* Display top source IPs and internet gateway paths by gigabyte
* Display top security groups by megabyte
* Display megabytes for the day starting in May, by hour

The second use case has its own set of sample questions recorded in the GitHub repository.

Amazon Q interprets the questions and generates queries based on fields from the dataset and synonyms defined in the linked topic. Amazon Q also selects a visualization type, which can be changed. In the top-right corner of the visual, select the bar chart image and choose the type of visual that represents the data most effectively for you. Select **ADD TO ANALYSIS**, as shown in Figure 10.

The following information table is populated with visuals from the four sample questions, as shown in Figure 11.

### Conclusion

In this article, you learned how to use Amazon QuickSight to visualize data from multiple data sources using natural language queries. Amazon Q in QuickSight helps democratize data access, empowering everyone in your organization to make data-driven decisions. Organizing data into visual topics and enabling natural language querying allows Amazon Q users to better understand VPC flows. To help your team get started with QuickSight, we recommend reviewing the following guides: *What is Amazon QuickSight Q* and *Best Practices for QuickSight Q Authors*.

### About the Authors:

#### Rashmiman Ray

Rashmiman is a Technical Account Manager at AWS, based in New Jersey. He works with AWS Enterprise customers, providing technical guidance and best practice recommendations to help them succeed on the cloud platform. Outside of work, he enjoys hiking, playing cricket, and cooking delicious Indian dishes.

#### Opeoluwa Victor Babasanmi

Victor is a Senior Network Specialist Solutions Architect at AWS. He focuses on providing customers with technical guidance on planning and building solutions based on best practices, while also proactively maintaining stable operations for their AWS environments. When he’s not supporting customers, you can find him playing soccer, working out, or looking for a new adventure somewhere.

#### Diego Hernandez

Diego Hernandez is a Technical Account Manager at AWS, based in Canada. Diego's passion is anything related to networking and relationships. In his free time, Diego enjoys spending time with his family and skiing.
