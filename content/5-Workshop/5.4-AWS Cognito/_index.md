---
title: "Configure AWS Cognito"
date: 2025-09-10
weight: 4
chapter: false
pre: "  5.4.  "
---

## Goal

In this step, we describe how **Amazon Cognito** is used in the *English Journey* web application to manage user authentication.  
Students can sign in using:

- **Email & password**
- **Google (Gmail) account**

Cognito acts as the central identity provider that issues tokens for the frontend and backend of English Journey.

---

## 5.4.1 Role of Amazon Cognito in the architecture

In the architecture of English Journey:

- **Cognito User Pool** stores user identities (email, name, etc.).
- It handles **sign-up, sign-in, password reset and email verification**.
- It integrates with **Amplify** so the React frontend can easily call `signIn`, `signUp` and other auth functions.
- It also connects to **social identity providers**:  
  - **Google** → for users who want to log in with their Gmail 
  
All successful logins (email, Google) result in **Cognito tokens** that are used to protect our APIs and Lambda functions.

---

## 5.4.2 Creating the Cognito User Pool

The main configuration steps:

1. **Create a User Pool**
   - Sign in to the AWS Management Console → **Amazon Cognito** → **User pools** → **Create user pool**.
   - Choose **Email** as the primary sign-in identifier.
   - Allow **self-registration** so students can create their own accounts.

2. **Configure password policy & verification**
   - Set a basic password policy (minimum length, characters, etc.).
   - Enable **email verification** so students must confirm their email before using the app.
   - Customize email templates (optional) for sign-up and password reset.

3. **Create an app client**
   - Create a **public app client** for the web frontend.
   - Enable **Cognito User Pool** and **social IdPs** as allowed identity providers.
   - Configure allowed **callback URLs** and **sign-out URLs** (for example, the Amplify frontend domain of English Journey).

This user pool is later referenced by Amplify and the React frontend.

---

## 5.4.3 Enabling Google (Gmail)

To support social logins, we added **Google** as identity providers in Cognito.

### Google (Gmail) login

1. In **Google Cloud Console**, create an **OAuth 2.0 Client ID** for a web application.
2. Set the **authorized redirect URI** to the Cognito callback URL generated for the user pool.
3. Copy the **Client ID** and **Client Secret** from Google.
4. In **Cognito → User pool → Identity providers → Google**:
   - Paste the Client ID and Client Secret.
   - Map Google attributes (email, name) to Cognito standard attributes.

Now users can click **"Sign in with Google"** and authenticate using their Gmail account.

---

## 5.4.4 Integrating Cognito with the Amplify frontend

The **React** frontend of English Journey uses **AWS Amplify Auth** to communicate with Cognito.

Conceptually:

- For **email/password**:
  - `Auth.signUp()` is used to create a new user.
  - `Auth.signIn()` is used for normal login.
- For **Gmail** social login:
  - We call `Auth.federatedSignIn({ provider: 'Google' })`,
  - Amplify redirects the user to Google, then back to Cognito, then back to the web app with valid tokens.

---
**On the UI, the login page shows three main options:**

    Sign in with email & password

    Sign in with Google

All three methods still end up in the same Cognito User Pool.

---
## 5.4.5 Security and user management

**With Cognito, we can:**

- Require email verification before granting full access to the application.

- Restrict which domains are allowed to use the sign-in flow (via callback URLs).

- Manage users centrally:
  - Lock accounts
  - Reset passwords
  - Delete accounts

- Extend the solution later with:
  - MFA (Multi-Factor Authentication)
  - Additional social providers (GitHub, Facebook, …)

**Within the scope of this workshop, we focus on:**

- Sign-in with email & password  
- Application login via Gmail  
- Email verification (OTP)

---

## Summary

**In this step, we configure Amazon Cognito as the identity management service for English Journey:**

- Create a User Pool to manage accounts and authenticate users.
- Allow users to sign in with email and Google (Gmail).
- Use tokens issued by Cognito in the React + Amplify front end to securely access APIs and back-end services.