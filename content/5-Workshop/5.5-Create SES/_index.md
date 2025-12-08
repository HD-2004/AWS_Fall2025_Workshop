---
title : "Configure Amazon SES for email"
date: 2025-09-10
weight : 5 
chapter : false
pre : " <b> 5.5. </b> "
---

In this section, you will replace the notification channel of the **English Journey** application from Amazon SNS to **Amazon Simple Email Service (SES)**.

SES will be used to send transactional emails to learners, for example:

- Welcome email after first sign-in  
- Daily / weekly learning reminders  
- Level-test results or new-lesson notifications  

> **Note:** SES is a regional service. Make sure you are working in the same AWS Region that you used for Amplify, Lambda and DynamoDB.

---

## 5.5.1 – Open Amazon SES console

1. In the AWS Management Console, search for **“SES”**.  
2. Choose **Amazon Simple Email Service**.  
3. At the top-right corner, verify the **Region** (for example: `ap-southeast-1`).  
4. If SES opens in another Region, switch back to the Region of your workshop environment.

---

## 5.5.2 – Verify an email identity

For this workshop we will verify a **single email address** and send emails from that address (for example: your personal Gmail).

1. In the SES left navigation menu, choose **Verified identities**.  
2. Click **Create identity**.  
3. Select **Email address**.  
4. In **Email address**, enter the address you will use as the sender, for example:  
   `your-name+english-journey@gmail.com`.
5. Leave the other options as default and choose **Create identity**.
6. SES sends a verification email to that address.  
7. Open your inbox, find the email from **Amazon Web Services**, and click the **verification link**.
8. Return to the SES console and refresh. The identity status should become **Verified**.

From now on, SES will only allow sending emails **from** and **to** verified identities (SES Sandbox mode). This is enough for a classroom / workshop environment.

---

## 5.5.3 – (Optional) Request production access

If you want to send emails to unverified recipients (for example, real learners), you must move your account out of the **SES sandbox**:

1. In the SES console, choose **Account dashboard**.  
2. Under **Your account details**, check the **Account status**.  
3. If it is still **Sandbox**, click **Request production access** and follow the wizard.  

> For this workshop you can stay in sandbox mode as long as you send mail only between verified email addresses.

---

## 5.5.4 – Create a configuration set (optional but recommended)

A configuration set groups all emails from the English Journey application and enables future observability (CloudWatch metrics, event publishing, etc.).

1. In the SES navigation menu, choose **Configuration sets**.  
2. Click **Create configuration set**.  
3. Enter a name, for example: `english-journey-config`.  
4. Leave all other settings as default and click **Create configuration set**.

We will use this configuration set later when sending emails from Lambda functions.

---

## 5.5.5 – Allow Lambda to send email with SES

Lambda functions such as **Daily Check-in** or **Test Level result** will send emails through SES.  
Lambda needs permission to call the SES APIs.

You will attach this permission in **Section 5.7 – Create IAM Roles & Policies**, but we prepare the policy here for reference:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ses:SendEmail",
        "ses:SendRawEmail"
      ],
      "Resource": "*"
    }
  ]
}
