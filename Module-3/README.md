# ☁️ AWS Account Setup — Beginner to DevOps Ready

A practical step-by-step guide for setting up, securing, and exploring an **AWS account** for Cloud and DevOps learning.

---

# 📚 Table of Contents

* [1. Create AWS Account](#1-create-aws-account)
* [2. Understand the Root User](#2-understand-the-root-user)
* [3. Secure the Root Account](#3-secure-the-root-account)
* [4. Enable MFA](#4-enable-mfa)
* [5. Create Billing Alerts](#5-create-billing-alerts)
* [6. Explore AWS Management Console](#6-explore-aws-management-console)
* [7. Understand AWS Regions](#7-understand-aws-regions)
* [8. Explore IAM](#8-explore-iam)
* [9. Explore EC2](#9-explore-ec2)
* [10. Explore S3](#10-explore-s3)
* [11. Explore VPC](#11-explore-vpc)
* [12. Explore CloudWatch](#12-explore-cloudwatch)
* [13. Explore CloudTrail](#13-explore-cloudtrail)
* [14. AWS Shared Responsibility Model](#14-aws-shared-responsibility-model)
* [15. AWS vs Customer Responsibility](#15-aws-vs-customer-responsibility)
* [16. EC2 Shared Responsibility Example](#16-ec2-shared-responsibility-example)
* [17. S3 Shared Responsibility Example](#17-s3-shared-responsibility-example)
* [18. Interview Explanation](#18-interview-explanation)
* [19. Complete AWS Setup Checklist](#19-complete-aws-setup-checklist)
* [20. Recommended Learning Path](#20-recommended-learning-path)

---

# 1. Create AWS Account

AWS provides cloud services such as:

* EC2
* S3
* IAM
* VPC
* CloudWatch
* CloudTrail
* ECR
* EKS
* RDS
* Lambda

Open the AWS Management Console:

**https://console.aws.amazon.com/**

## Step 1 — Create an Account

If you don't already have an AWS account:

1. Click **Create an AWS Account**
2. Enter your:

   * Email address
   * AWS account name
   * Root-user password
3. Verify your email address
4. Enter contact information
5. Select **Personal** if the account is for learning
6. Add a payment method
7. Complete phone verification
8. Select a support plan
9. Complete account creation

> ⚠️ **Important:** AWS may require payment information even when using Free Tier services. Always monitor your usage and billing.

---

# 2. Understand the Root User

When an AWS account is created, the initial identity is the:

**AWS Account Root User**

The root user has extremely powerful permissions and should be protected carefully.

Think of it as:

```text
AWS Account
     │
     ▼
 Root User
     │
     ├── Account management
     ├── Billing
     ├── Security settings
     └── Emergency operations
```

## ❌ Do NOT use Root for everyday work

Avoid using the root account for:

```text
EC2 deployment
S3 management
Kubernetes
Terraform
AWS CLI
Daily administration
Application deployment
```

Instead, use an appropriate administrative identity for normal AWS operations.

---

# 3. Secure the Root Account

After logging in as the root user, review your account/security settings.

Check:

```text
AWS Root Account
       │
       ├── Strong unique password
       ├── MFA enabled
       ├── No root access keys
       ├── Recovery information
       ├── Contact information
       └── Alternate contacts
```

## Root Account Security Checklist

* [ ] Strong unique password
* [ ] MFA enabled
* [ ] No root access keys
* [ ] Recovery information verified
* [ ] Contact information updated
* [ ] Avoid everyday root usage

> 🔐 **Best Practice:** Never create long-lived access keys for the root user.

---

# 4. Enable MFA

## What is MFA?

**MFA = Multi-Factor Authentication**

MFA adds an additional authentication factor beyond your password.

Instead of:

```text
Email
  ↓
Password
  ↓
AWS Console
```

You use:

```text
Email
  ↓
Password
  ↓
MFA
  ↓
AWS Console
```

AWS supports multiple MFA options, including phishing-resistant options such as passkeys/security keys and virtual authenticator applications.

---

## Option A — Authenticator App

An authenticator application can generate a temporary **6-digit TOTP code**.

Examples include applications that support time-based one-time passwords.

---

## Steps to Enable MFA

1. Sign in as the **Root User**
2. Open the account/security credentials area
3. Find **Multi-Factor Authentication (MFA)**
4. Click **Add MFA / Assign MFA device**
5. Select **Authenticator App / Virtual MFA**
6. Give the MFA device a name
7. AWS displays a QR code
8. Open your authenticator application
9. Scan the QR code
10. Enter the generated MFA code
11. Follow AWS instructions if an additional code is requested
12. Click **Add/Enable MFA**

---

## Verify MFA

Log out and sign in again.

```text
Root Email
     │
     ▼
Password
     │
     ▼
MFA Code
     │
     ▼
AWS Console
```

If MFA is required during login, it has been successfully enabled.

> 🔐 **Best Practice:** Keep a secure recovery method for your MFA device. AWS supports multiple MFA devices for root users, which can improve recovery resilience.

---

# 5. Create Billing Alerts

For a learning account, create billing budgets/alerts to detect unexpected spending.

Example:

```text
$5  → Warning
$10 → Warning
```

> ⚠️ A budget alert is **not a hard spending limit**.

For example:

```text
$5 Budget
    │
    ▼
Alert
    │
    ▼
Does NOT automatically stop
EC2 / S3 / RDS / etc.
```

You must manually stop or delete unused resources unless you have configured additional automation.

---

# 5.1 Open Billing

AWS Console:

```text
Billing and Cost Management
        │
        ▼
     Budgets
        │
        ▼
  Create Budget
```

---

# 5.2 Create $5 Budget

Choose:

```text
Create Budget
```

Example configuration:

```text
Budget Name:
AWS-$5-Budget

Budget Amount:
$5

Period:
Monthly

Threshold:
100%
```

Add your email address for notifications.

Then click:

```text
Create Budget
```

---

# 5.3 Create $10 Budget

Repeat the same process.

```text
Budget Name:
AWS-$10-Budget

Budget Amount:
$10

Period:
Monthly

Threshold:
100%
```

Add your email address.

Then:

```text
Create Budget
```

---

## Recommended Billing Setup

```text
                 AWS Account
                      │
              ┌───────┴───────┐
              │               │
          $5 Budget       $10 Budget
              │               │
              ▼               ▼
       Email Alert      Email Alert
```

---

# 6. Explore AWS Management Console

Open:

```text
https://console.aws.amazon.com/
```

The AWS Management Console provides access to AWS services and account resources.

---

## Console Home

Explore:

* Recently visited services
* Favorites
* Service search
* AWS Regions
* Account information
* Billing information
* Resource information

---

## Service Search

Search for the following services:

```text
EC2
S3
IAM
VPC
CloudWatch
CloudTrail
EKS
Lambda
RDS
DynamoDB
ECR
Route 53
```

These services are especially useful for Cloud and DevOps learning.

---

# 7. Understand AWS Regions

An AWS **Region** is a geographic area containing multiple AWS Availability Zones.

Examples:

```text
Mumbai
   ↓
ap-south-1

Singapore
   ↓
ap-southeast-1

N. Virginia
   ↓
us-east-1
```

---

## Why Regions Matter

Many AWS resources are regional.

For example:

```text
Create EC2
    │
    ▼
ap-south-1
```

If you switch to:

```text
us-east-1
```

you may not see the same EC2 instance because you're now viewing a different Region.

> ⚠️ **Always check the selected Region before creating or searching for AWS resources.**

---

# 8. Explore IAM

Open:

```text
IAM → Dashboard
```

IAM stands for:

**Identity and Access Management**

IAM controls access to AWS resources.

---

## IAM Structure

```text
IAM
│
├── Users
├── Groups
├── Roles
├── Policies
└── Identity Providers
```

---

## IAM User

A user represents an identity that can interact with AWS.

```text
Developer
    │
    ▼
IAM User
```

---

## IAM Group

A group is a collection of users.

```text
Developers Group
   │
   ├── User A
   ├── User B
   └── User C
```

---

## IAM Policy

A policy defines permissions.

Example concept:

```text
Allow
   │
   ▼
Read S3 Objects
```

Policies determine what an identity can or cannot do.

---

## IAM Role

A role is an identity that can be assumed to obtain temporary credentials.

Example:

```text
Developer
    │
    ▼
Assume Role
    │
    ▼
Temporary Credentials
    │
    ▼
AWS Services
```

> 🔐 **Best Practice:** Prefer temporary credentials and federation/IAM Identity Center where appropriate instead of relying on long-lived credentials.

---

# 9. Explore EC2

Open:

```text
EC2 → Dashboard
```

**EC2 = Elastic Compute Cloud**

EC2 provides virtual servers in AWS.

For initial exploration, you don't need to launch an instance.

Understand:

```text
Instances
AMIs
Instance Types
Key Pairs
Security Groups
Elastic IPs
EBS Volumes
Load Balancers
Auto Scaling
```

---

## Basic EC2 Architecture

```text
Internet
    │
    ▼
Load Balancer
    │
    ▼
EC2 Instance
    │
    ▼
EBS Volume
```

---

# 10. Explore S3

Open:

```text
S3
```

**S3 = Simple Storage Service**

S3 provides object storage.

---

## S3 Structure

```text
S3
│
├── Bucket
│     ├── Object
│     ├── Object
│     └── Object
│
├── Versioning
├── Encryption
├── Lifecycle
└── Access Control
```

### Important

**S3 bucket names are globally unique.**

For example:

```text
my-project-files-2026
```

Another AWS account cannot create the exact same bucket name while it is in use.

---

# 11. Explore VPC

Open:

```text
VPC → Your VPCs
```

**VPC = Virtual Private Cloud**

A VPC provides an isolated virtual network for your AWS resources.

---

## Basic VPC Architecture

```text
AWS Region
    │
    ▼
   VPC
    │
 ┌──┴────────────────┐
 │                   │
 ▼                   ▼
Public Subnet     Private Subnet
 │                   │
 ▼                   ▼
EC2 / Load         Application
Balancer              │
                      ▼
                   Database
```

---

## Important VPC Concepts

Learn:

```text
VPC
Subnet
Route Table
Internet Gateway
NAT Gateway
Security Group
Network ACL
CIDR
```

These are essential concepts for Cloud and DevOps roles.

---

# 12. Explore CloudWatch

Open:

```text
CloudWatch
```

CloudWatch is used for monitoring, metrics, logs, alarms, and operational visibility.

---

## CloudWatch Components

```text
CloudWatch
│
├── Metrics
├── Logs
├── Alarms
├── Dashboards
└── Events / Automation
```

---

## Example

```text
EC2
 │
 ▼
CPU Utilization
 │
 ▼
CloudWatch Metric
 │
 ▼
CloudWatch Alarm
 │
 ▼
Notification / Action
```

---

# 13. Explore CloudTrail

Open:

```text
CloudTrail
```

**AWS CloudTrail** records AWS account/API activity.

Example:

```text
User
 │
 ▼
AWS Console
 │
 ▼
Create EC2
 │
 ▼
CloudTrail
 │
 ▼
API Activity Recorded
```

CloudTrail is useful for:

* Auditing
* Security investigations
* Tracking API activity
* Understanding account changes

---

# 14. AWS Shared Responsibility Model

The **AWS Shared Responsibility Model** is one of the most important AWS concepts.

AWS describes security as a shared responsibility between:

```text
AWS
 +
Customer
```

Easy way to remember:

> ☁️ **AWS = Security OF the Cloud**

> 👨‍💻 **Customer = Security IN the Cloud**

The exact division of responsibility depends on the AWS service being used.

---

# 15. AWS vs Customer Responsibility

## AWS Responsibility

AWS manages the underlying infrastructure.

```text
AWS
│
├── Physical Data Centers
├── Physical Security
├── Hardware
├── Networking Infrastructure
├── Host Infrastructure
├── Virtualization Layer
└── AWS-Managed Infrastructure
```

AWS protects the infrastructure that runs AWS services.

---

## Customer Responsibility

Customers are responsible for what they configure and deploy.

```text
Customer
│
├── IAM Permissions
├── Passwords
├── MFA
├── Data
├── Encryption Configuration
├── Security Groups
├── Operating System Patching
├── Application Security
└── Network Configuration
```

The customer's responsibilities vary depending on the service.

---

# 16. EC2 Shared Responsibility Example

For EC2, AWS manages the underlying infrastructure while the customer manages many aspects of the guest environment.

```text
                    EC2
                     │
             ┌───────┴───────┐
             │               │
            AWS           Customer
             │               │
       Physical Hardware     OS
       Data Center           Patching
       Hypervisor            Application
       Infrastructure       Security Groups
                             Data
```

### AWS

Responsible for things such as:

```text
Physical Hardware
Data Centers
Underlying Infrastructure
Hypervisor
```

### Customer

Responsible for things such as:

```text
Operating System
OS Patching
Applications
Security Groups
Data
Application Security
```

---

# 17. S3 Shared Responsibility Example

For S3, AWS manages the underlying service infrastructure.

The customer is responsible for appropriate configuration of resources and data.

Examples:

```text
Customer
│
├── Bucket Policies
├── Access Permissions
├── Data Protection
├── Encryption Configuration
└── Public/Private Access Settings
```

---

# 18. Interview Explanation

### ⭐ AWS Shared Responsibility Model — Interview Answer

> AWS follows a shared responsibility model for security. AWS is responsible for security **of the cloud**, which includes the physical infrastructure, hardware, networking, and virtualization layer. The customer is responsible for security **in the cloud**, including IAM permissions, data, operating-system patching where applicable, application security, and service configuration. The exact responsibilities depend on the AWS service being used.

---

# 19. Complete AWS Setup Checklist

Use this checklist while setting up your learning account.

## 🆕 Account Setup

```text
☐ Create AWS account
☐ Verify email
☐ Verify phone
☐ Add payment method
☐ Complete account setup
☐ Sign in as Root user
```

---

## 🔐 Security

```text
☐ Set strong root password
☐ Enable Root MFA
☐ Verify MFA login
☐ Do NOT create root access keys
☐ Verify recovery information
☐ Review account security settings
```

---

## 💰 Billing

```text
☐ Open Billing & Cost Management
☐ Create $5 budget/alert
☐ Create $10 budget/alert
☐ Add notification email
☐ Verify billing alerts
☐ Monitor AWS usage regularly
```

---

## 🖥️ AWS Console

```text
☐ Explore AWS Console
☐ Explore Service Search
☐ Explore Dashboard
☐ Understand Regions
☐ Explore Account Settings
```

---

## ☁️ AWS Services

```text
☐ IAM
☐ EC2
☐ S3
☐ VPC
☐ CloudWatch
☐ CloudTrail
☐ ECR
☐ EKS
☐ RDS
☐ Lambda
☐ DynamoDB
☐ Route 53
```

---

## 🔒 Security Concepts

```text
☐ Shared Responsibility Model
☐ AWS Responsibility
☐ Customer Responsibility
☐ IAM
☐ MFA
☐ Least Privilege
☐ Temporary Credentials
☐ Security Groups
☐ Network Security
```

---

## 🧹 Final Account Check

```text
☐ Log out of Root user
☐ Use administrative identity for daily work
☐ Monitor billing
☐ Stop unused EC2 instances
☐ Delete unused resources
☐ Review AWS resources regularly
```

---

# 20. Recommended Learning Path

After completing the account setup, follow this sequence:

```text
AWS Account Setup
       │
       ▼
IAM
       │
       ▼
Administrative Access
       │
       ▼
AWS CLI
       │
       ▼
VPC
       │
       ▼
EC2
       │
       ▼
Security Groups
       │
       ▼
SSH
       │
       ▼
S3
       │
       ▼
CloudWatch
       │
       ▼
CloudTrail
       │
       ▼
ECR
       │
       ▼
Docker + ECR
       │
       ▼
EKS
       │
       ▼
Terraform
       │
       ▼
CI/CD
       │
       ▼
Cloud/DevOps Projects
```

---

# 🎯 DevOps Engineer Foundation

The goal is not just to memorize AWS services.

Build a strong understanding of:

```text
                 CLOUD / DEVOPS
                       │
       ┌───────────────┼───────────────┐
       │               │               │
      IAM             VPC             EC2
       │               │               │
       └───────────────┼───────────────┘
                       │
                      S3
                       │
                  CloudWatch
                       │
                  CloudTrail
                       │
                     ECR
                       │
                     EKS
                       │
                   Terraform
                       │
                    CI/CD
```

---

# 🧠 Quick Revision

### Root User

```text
Emergency / Account Management
```

### MFA

```text
Password + Second Authentication Factor
```

### IAM

```text
Identity + Access Management
```

### EC2

```text
Virtual Server / Compute
```

### S3

```text
Object Storage
```

### VPC

```text
Virtual Network
```

### CloudWatch

```text
Monitoring + Metrics + Logs + Alarms
```

### CloudTrail

```text
AWS API / Account Activity Recording
```

### Shared Responsibility

```text
AWS → Security OF the Cloud
Customer → Security IN the Cloud
```

---

# ⚠️ Important AWS Safety Rules

```text
1. Never share AWS passwords.
2. Never commit AWS access keys to GitHub.
3. Never create root-user access keys for normal work.
4. Enable MFA.
5. Use least-privilege permissions.
6. Monitor billing.
7. Stop unused EC2 instances.
8. Delete unused resources.
9. Check the AWS Region before creating resources.
10. Never assume Free Tier means unlimited usage.
```

---

# 🚀 Final Goal

After completing this setup, you should be comfortable with:

```text
AWS Account
     ↓
Security
     ↓
IAM
     ↓
Regions
     ↓
VPC
     ↓
EC2
     ↓
S3
     ↓
Monitoring
     ↓
Logging
     ↓
AWS CLI
     ↓
Docker
     ↓
ECR
     ↓
EKS
     ↓
Terraform
     ↓
CI/CD
```

This provides a strong foundation for **AWS Cloud Engineer and DevOps Engineer** preparation.
