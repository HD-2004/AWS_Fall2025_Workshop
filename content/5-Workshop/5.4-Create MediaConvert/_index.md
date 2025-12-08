---
title : "Configure AWS MediaConvert"
date: 2025-09-10
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---


## Goal

English Journey includes listening and video content (for example short dialogues or explanation videos).  
To make sure these media files can be played smoothly on the web, we use **AWS Elemental MediaConvert** to transcode the original files into web-friendly formats and store them in a separate S3 bucket.

This section describes how MediaConvert was configured and how it is integrated into the architecture.

---

## 5.4.1 Role of MediaConvert in the architecture

In the architecture diagram, MediaConvert sits between two S3 buckets:

- **S3 (source)** – where raw media files are uploaded (for example, videos provided by teachers).
- **AWS MediaConvert** – service that converts these files into compressed MP4/HLS suitable for browsers and mobile devices.
- **S3 (destination)** – bucket that stores the converted outputs.  
  The frontend application later accesses these files through CloudFront / Amplify.

A Lambda function is responsible for creating MediaConvert jobs whenever a new source file appears.

---

## 5.4.2 Creating the MediaConvert service role

MediaConvert needs permission to read from the **source bucket** and write to the **destination bucket**.  
For that we created a dedicated **IAM service role** with:

- a **trust policy** that allows the `mediaconvert.amazonaws.com` service to assume the role,
- an **inline policy** granting:
  - `s3:GetObject` on the source bucket,
  - `s3:PutObject` on the destination bucket,
  - `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents` for CloudWatch Logs.

This role is referenced in all MediaConvert jobs.

*(The detailed JSON policy is documented later in section 5.11 – IAM Roles & Policies.)*

---

## 5.4.3 Creating a MediaConvert queue and job template

To simplify the workflow, we created:

1. A **standard queue** in MediaConvert where all transcoding jobs are submitted.
2. A **job template** defining:
   - input settings (H.264, AAC, input resolution),
   - output groups:
     - MP4 output for simple download / playback,
     - optional HLS output for adaptive streaming,
   - default output destination pointing to the **destination S3 bucket** with a path structure like:

     ```text
     s3://english-journey-media-output/{lessonId}/
     ```

The job template allows the Lambda function to submit jobs by only specifying the input file and a few high-level parameters (lesson ID, language, etc.).

---

## 5.4.4 Integration with Lambda

A backend **AWS Lambda** function (managed through Amplify) is responsible for orchestrating MediaConvert:

1. When a new media file is uploaded to the **source S3 bucket**, an **S3 event** triggers the Lambda.
2. The Lambda constructs a MediaConvert job request:
   - input: `s3://source-bucket/.../file.mp4`
   - output: destination path based on lesson or unit,
   - job template name,
   - role ARN of the MediaConvert service role.
3. The Lambda calls the **MediaConvert CreateJob API** using the regional endpoint.
4. Optionally, another Lambda (or the same function) can listen for job completion notifications (via CloudWatch Events or SNS) and update metadata in DynamoDB (for example marking the lesson as “ready”).

This automation means teachers only need to upload a raw video; the system takes care of transcoding and delivering optimized formats to learners.

---

## 5.4.5 Benefits

Using MediaConvert in English Journey provides:

- **Consistent playback** across browsers and devices thanks to standardized output formats.
- **Smaller file sizes** and better bandwidth usage for students.
- **Automated workflow**: no manual video conversion is required; everything is performed server-side after upload.
