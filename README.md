# 🔁 Okta → AWS Just-In-Time (JIT) Provisioning Lab

## Overview

This lab demonstrates **Just-In-Time (JIT) user provisioning** between Okta (Identity Provider) and AWS IAM Identity Center (Service Provider).

Unlike SCIM pre-provisioning, JIT provisioning creates the AWS user account **at the moment of first successful SAML authentication**, using attributes sent in the SAML assertion.

This lab validates that:

- The user does NOT exist in AWS before login
- The user exists in Okta and is assigned the AWS app
- Login via AWS access portal triggers SAML
- AWS auto-creates the user from SAML attributes
- Permission sets are applied after creation

---

## Identity Model Comparison

| Provisioning Method | User Created Before Login | User Created At Login | Created By |
|---------------------|---------------------------|------------------------|------------|
| SCIM                | ✅ Yes                    | ❌ No                 | SCIM       |
| JIT (SAML)         | ❌ No                     | ✅ Yes                | External IdP / SAML |

---

## Architecture Flow

1. User navigates to AWS Access Portal (SP-initiated login)
2. AWS redirects to Okta for authentication
3. Okta issues SAML assertion containing identity attributes
4. AWS IAM Identity Center:
   - Detects user does not exist
   - Creates user from SAML attributes (JIT)
5. Group → Permission Set mapping applies
6. User gains AWS account access

---

## Configuration Steps

### 1️⃣ Confirm SCIM Disabled (for pure JIT demonstration)

AWS → IAM Identity Center → Settings → Automatic Provisioning  
SCIM disabled to ensure users are not pre-provisioned.

📸 Screenshot:
`images/scim-disabled.png`

---

### 2️⃣ Verify Okta SAML Attribute Statements

Okta → Applications → AWS IAM Identity Center → Sign On → SAML Settings

Required attributes:

- NameID → user.email
- email → user.email
- firstName → user.firstName
- lastName → user.lastName

📸 Screenshot:
`images/okta-saml-attributes.png`

---

### 3️⃣ Validate User Does Not Exist in AWS

AWS → IAM Identity Center → Users

Confirmed test user is not present prior to login.

📸 Screenshot:
`images/pre-login-no-user.png`

---

### 4️⃣ Perform SP-Initiated Login

Used AWS Access Portal URL:
