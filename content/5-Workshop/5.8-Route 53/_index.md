---
title : "Configure Amazon Route 53"
date: 2025-09-10
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---

## Goal

This section explains how **Amazon Route 53** is used in the English Journey architecture
to provide a **custom domain** for the web application and to route traffic to the
Amplify-hosted frontend protected by AWS WAF.

---

## 5.8.1 Role of Route 53 in the architecture

In the high-level diagram, Route 53 sits between the **end user** and the **web frontend**:

- The app is hosted by **AWS Amplify** (which internally uses an S3 bucket + CloudFront).
- We register or use an existing **domain name**, such as `englishjourney.example.com`.
- **Route 53** manages DNS for this domain and creates an **Alias record** that points to
  the Amplify application (or its underlying CloudFront distribution).
- Optionally, the same domain is associated with **AWS WAF** to filter malicious requests
  before they reach the app.

Thanks to Route 53, users access the site through a friendly URL instead of the default
Amplify URL.

---

## 5.8.2 Domain and hosted zone

To use Route 53, we first need a **hosted zone** for the domain.

There are two options:

1. **Register a new domain in Route 53**
   - e.g. `englishjourney.workshop.com`.
   - Route 53 automatically creates a public hosted zone with NS and SOA records.

2. **Use an existing domain from another registrar**
   - Create a **public hosted zone** in Route 53 with the same domain name.
   - Update the domain’s name servers at the registrar to point to the Route 53 NS records.

In both cases, English Journey uses this hosted zone to create DNS records for the app.

---

## 5.8.3 Linking the Amplify app to the custom domain

Once the domain/hosted zone is ready:

1. Open the **AWS Amplify console** for the English Journey app.
2. Choose **Domain management → Add domain**.
3. Enter the domain name managed by Route 53  
   (e.g. `englishjourney.example.com`).
4. Amplify suggests one or more **subdomains**:
   - `englishjourney.example.com` → main production branch,
   - optionally `dev.englishjourney.example.com` → development branch.
5. Choose the branches you want to map and confirm.

Amplify then:

- creates the necessary **A/AAAA Alias records** in Route 53,
- requests or attaches an **AWS Certificate Manager (ACM)** certificate for HTTPS,
- associates the domain with the underlying CloudFront distribution.

From this point, users can access the site via the custom domain instead of the Amplify URL.

---

## 5.8.4 Example DNS records in Route 53

In the hosted zone you will typically see records like:

- `A` (Alias) – `englishjourney.example.com` → CloudFront / Amplify domain
- `AAAA` (Alias) – IPv6 equivalent (optional)
- `CNAME` – `www.englishjourney.example.com` → `englishjourney.example.com` (optional redirect)
- The default `NS` and `SOA` records created with the hosted zone.

These records are managed automatically by Amplify in the workshop,  
but they appear in Route 53 so that DNS resolution is fully controlled by our account.

---

## 5.8.5 Integration with AWS WAF

If the application uses **AWS WAF** (as shown in the architecture):

- WAF is attached to the **CloudFront distribution** behind Amplify.
- Because Route 53 routes traffic to that distribution via Alias records,
  all traffic to the custom domain automatically passes through WAF.
- WAF can block common attacks (SQL injection, XSS, bad bots) before requests reach the Amplify app.

Route 53 itself does not inspect traffic, but it is the entry point that connects
the custom domain → CloudFront/WAF → Amplify.

---

## 5.8.6 Summary

Route 53 provides:

- **Human-readable URLs** for the English Journey site,
- **DNS control** within the same AWS account as the rest of the architecture,
- seamless integration with **Amplify, CloudFront, ACM and WAF**.

Together with Amplify, this allows the workshop to present a realistic,
production-like configuration for a modern web application.
