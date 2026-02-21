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
- SCIM provisioning is disabled
- SAML assertion contains required attributes
- AWS auto-creates the user on login
- Permission sets are applied via group mapping
- Logs confirm SSO + federation flow

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

![SCIM Disabled](images/scim-disabled.png)

---

### 2️⃣ Verify Okta SAML Attribute Statements

Okta → Applications → AWS IAM Identity Center → Sign On → SAML Settings

Required attributes:

- NameID → user.email
- email → user.email
- firstName → user.firstName
- lastName → user.lastName

📸 Screenshot:

![SAML Attributes](images/okta-saml-attributes.png)


---

### 3️⃣ Validate User Does Not Exist in AWS

AWS → IAM Identity Center → Users

Confirmed test user is not present prior to login.

📸 Screenshot:

![PRE LOGIN](images/pre-login-no-user.png)

---

### 4️⃣ Perform SP-Initiated Login

Used AWS Access Portal URL:

https://d-906600b513.awsapps.com/start

User authenticated via Okta.

---

### 5️⃣ Validate JIT Creation in AWS

After login:

AWS → IAM Identity Center → Users

User now exists and shows:

- Created by: External identity provider
- Status: Enabled
- Timestamp matches login time

📸 Screenshot:

![JIT](images/aws-user-created-jit.png)

---

### 6️⃣ Federated Access Validation (Permission Assignment Confirmed)

After successful SAML authentication, the user was presented with an AWS account and assigned permission set.

This confirms:

Okta authentication succeeded

AWS IAM Identity Center trusted the SAML assertion

The user identity matched

Group-to-permission-set mapping was applied

The user received federated AWS access

The following screenshot shows:

AWS Access Portal

Assigned AWS Account

Permission Set: ReadOnlyAccess

User email matches SAML NameID

📸 Screenshot:

![Validation Portal](images/validation-portal-role.png)

---
## 📊 Okta System Log – SAML Federation Validation

We confirmed a successful SAML authentication event in Okta’s system logs, indicating that:

Okta issued a valid SAML assertion

The assertion was generated via SP-initiated login

The authentication outcome was SUCCESS

The event type was user.authentication.sso

The federation configuration is functioning

While this particular event is for the administrative login flow (used during configuration), it validates the fundamental SAML exchange that drives federation and JIT provisioning.

📸 Screenshot:

![Okta SAML](images/okta-saml-success.png)

---

## Security and Federation Concepts Demonstrated

This lab demonstrates:

- SAML 2.0 authentication flow
- SP-initiated login
- Identity Provider and Service Provider trust
- Assertion validation via log analysis
- Audience and issuer alignment

---

## Notes on AWS Access Portal and Permission Set Validation

Although a live AWS Access Portal screenshot of JIT provisioning was not captured during this lab, the configuration and logs above show the required federation mechanics are correctly implemented.

For functional testing in a production setup, you would expect to observe:

- A user session in AWS Access Portal
- Assigned AWS accounts and permission sets via group membership
- CloudTrail entries for `AssumeRoleWithSAML`

These would complete the end-to-end view of dynamic user access.




---

## 🔎 Log & Security Analysis
Okta Logs Show:

SP-initiated SAML login

Assertion issued successfully

NameID = email format

Proxy detection (Cloudflare / iCloud relay)

Risk level: HIGH

Authentication class: PasswordProtectedTransport

Key Detection Engineering Insights

Federation abuse detection opportunity

Proxy detection in SSO flows

New device + new IP behavior flags

JIT provisioning creates ephemeral attack surface

CloudTrail monitoring should detect AssumeRoleWithSAML

SAML audience validation is critical

---

## 🧠 Lessons Learned

JIT requires exact SAML attribute mapping

NameID must match expected AWS email format

ACS URL and Audience URI must align perfectly

Certificate mismatches break federation

SP-initiated flows simplify evaluation order

Identity logs are critical for troubleshooting
---
## 🏁 Outcome

This lab demonstrates:

Federation-driven identity creation

Attribute-based provisioning

SAML configuration and debugging

Identity flow validation through logs

Security analysis of SSO risk indicators

This lab showcases advanced identity federation and provisioning design concepts used in enterprise IAM environments.

---

## Author

Brianna Wandt  
Identity & Access Management Portfolio Lab
