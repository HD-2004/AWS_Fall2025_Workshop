---
title : "Configure Amazon Route 53"
date: 2025-09-10
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---



## 5.8 Configure Amazon Route 53 (Custom Domain)

In this step we connect the Amplify-hosted **English Journey** frontend to a custom
domain managed by **Amazon Route 53**.

> 🔗 **Domain used in this workshop**
>
> For the demo environment we use the domain  
> **englishjourney.xyz** – the final site is available at:  
> `https://www.englishjourney.xyz/`
>


---

### 5.8.1 Create / verify the hosted zone

1. Open the **Route 53** console → *Hosted zones* → **Create hosted zone**.
2. Enter your domain name, e.g. **englishjourney.xyz**, and keep type = *Public hosted zone*.
3. Route 53 creates a set of **NS** and **SOA** records for the zone.
4. If the domain is registered elsewhere, copy the Route 53 **NS** records to your registrar so that DNS is delegated to Route 53.

---

### 5.8.2 Connect the domain in AWS Amplify

1. Go to the **AWS Amplify** console → select your **English Journey** app.
2. In the left menu choose **Domain management** → **Add domain**.
3. Select the hosted zone **englishjourney.xyz**.
4. Map the root and sub-paths, for example:

   - `englishjourney.xyz` → main branch (production)
   - `www.englishjourney.xyz` → redirect to root

5. Amplify automatically creates the required **A / AAAA** and **CNAME** records in Route 53.

---

### 5.8.3 Test the site

1. Wait for DNS and SSL provisioning to complete (a few minutes).
2. Open a browser and navigate to:

   - `https://www.englishjourney.xyz/`

3. Verify that the English Journey homepage is served correctly over HTTPS.
4. Note this URL in your report / slides as the public entry point of the workshop application.

