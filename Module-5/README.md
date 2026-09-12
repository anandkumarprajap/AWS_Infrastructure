# AWS IAM – Access Management Hands-On Lab

## 📌 Overview

This hands-on lab demonstrates how IAM access management can be designed in a real company environment.

We will create different teams, users, groups, policies, and roles and then test what each user can and cannot access.

### Teams in this Lab

```text
Company AWS Account
        |
        +---------------- Finance Team
        |
        +---------------- Monitoring Team
        |
        +---------------- DevOps Team
        |
        +---------------- Developer Team
```

The main objective is to understand:

* AWS Root User
* IAM Users
* IAM Groups
* IAM Policies
* IAM Roles
* JSON Policies
* EC2-specific permissions
* S3 access
* AWS CLI authentication
* MFA
* Least Privilege
* Permission testing

---

# 1. IAM Access Management

The main IAM areas we will use are:

```text
IAM
 |
 +-- Roles
 |
 +-- Policies
 |
 +-- Users
 |
 +-- User Groups
 |
 +-- Identity Providers
```

### AWS Console Navigation

```text
AWS Console
   ↓
IAM
   ↓
Access Management
   ├── Users
   ├── User groups
   ├── Roles
   ├── Policies
   └── Identity providers
```

---

# 2. Important AWS IAM Architecture

The basic permission model is:

```text
                    IAM Policy
                         |
                         ↓
                    IAM Group
                         |
                         ↓
                     IAM User
```

For AWS services that need temporary permissions:

```text
IAM Role
    ↓
AWS Service / User
    ↓
Temporary Credentials
    ↓
AWS Resource
```

---

# 3. Step 1 – Secure the Root User

When an AWS account is created, the first login is the **Root User**.

The Root User has extremely powerful permissions.

### Root User Best Practice

Use the Root User only for account-level tasks that specifically require root access.

Do not use the Root User for normal daily AWS administration.

### Root User Setup

1. Open the AWS Management Console.
2. Sign in using the Root User.
3. Open the account menu.
4. Go to **Security credentials**.
5. Enable **MFA**.
6. Configure the authenticator application.
7. Sign out from the Root User.

### Do NOT do this

```text
Root User
     ↓
Daily AWS Administration
```

### Recommended

```text
Root User
     ↓
Account-level configuration only

IAM User / IAM Identity Center
     ↓
Daily AWS Administration
```

---

# 4. Step 2 – Create IAM Groups

Go to:

```text
AWS Console
   ↓
IAM
   ↓
User groups
   ↓
Create group
```

Create these groups:

```text
Finance
Monitoring
DevOps
Developer
```

---

# 5. Step 3 – Finance Group

The Finance team needs to view AWS billing and cost information.

### Group

```text
Finance
```

### Permission

For this lab, assign an AWS-managed billing read-only policy appropriate to your account's current IAM policy catalog.

The Finance team should be able to:

```text
View billing
View costs
View usage
```

They should NOT be able to:

```text
Create EC2 instances
Delete S3 buckets
Modify VPCs
Change IAM permissions
```

### Architecture

```text
Billing Read-Only Policy
          ↓
     Finance Group
          ↓
     Finance User
```

---

# 6. Step 4 – Monitoring Group

The Monitoring team manages AWS monitoring resources.

### Group

```text
Monitoring
```

### Permission

For the hands-on lab, use:

```text
CloudWatch access
```

The Monitoring user can work with:

* CloudWatch Metrics
* CloudWatch Dashboards
* CloudWatch Alarms
* CloudWatch Logs, according to the policy assigned

### Architecture

```text
CloudWatch Policy
       ↓
Monitoring Group
       ↓
Monitoring User
```

> **Production recommendation:** Avoid broad `FullAccess` policies when possible. Create a least-privilege policy containing only the CloudWatch actions the monitoring team actually needs.

---

# 7. Step 5 – DevOps Group

The DevOps team manages infrastructure and deployment resources.

### Group

```text
DevOps
```

For a learning/demo environment, you may temporarily use broader permissions.

However, in production:

```text
Do NOT automatically give AdministratorAccess
```

Instead, define permissions based on the DevOps responsibilities.

Example:

```text
EC2
VPC
S3
CloudWatch
IAM
EKS
CloudFormation
Terraform-related AWS resources
```

Only the required actions should be allowed.

---

# 8. Step 6 – Developer Group

Create another group:

```text
Developer
```

The Developer group should receive application-specific permissions.

For example:

```text
S3 application bucket
CloudWatch logs
Specific EC2 resources
```

The Developer should not automatically receive:

```text
IAM Administrator
Billing modification
VPC administration
Full AWS account access
```

---

# 9. Step 7 – Create IAM Users

Create users according to the teams.

```text
Finance User
Monitoring User
DevOps User
Developer User
```

AWS Console:

```text
IAM
 ↓
Users
 ↓
Create user
```

Configure console access only when required.

Then add each user to the appropriate group.

| User            | Group      |
| --------------- | ---------- |
| Finance User    | Finance    |
| Monitoring User | Monitoring |
| DevOps User     | DevOps     |
| Developer User  | Developer  |

---

# 10. Important Permission Concept

Do not normally attach the same policy individually to every user.

### Avoid

```text
Finance User → Billing Policy
Monitoring User → CloudWatch Policy
Developer User → S3 Policy
```

### Prefer

```text
Billing Policy
      ↓
Finance Group
      ↓
Finance User

CloudWatch Policy
      ↓
Monitoring Group
      ↓
Monitoring User
```

This makes IAM easier to maintain.

---

# 11. Hands-On Scenario 1 – Finance Billing Access

## Objective

Allow the Finance user to view billing information but prevent infrastructure management.

### Configuration

```text
Finance User
     ↓
Finance Group
     ↓
Billing Read-Only Policy
```

### Test

Sign in as the Finance user and verify:

```text
Billing → Allowed
EC2 → Not allowed
IAM → Not allowed
VPC → Not allowed
```

The exact billing-console behavior can depend on the account's billing-access settings, so verify the effective permissions in your account.

---

# 12. Hands-On Scenario 2 – Monitoring CloudWatch Access

## Objective

Allow the Monitoring user to manage monitoring resources.

```text
Monitoring User
       ↓
Monitoring Group
       ↓
CloudWatch Policy
```

### Test

Try:

```text
CloudWatch → Allowed
EC2 creation → Not allowed
IAM user creation → Not allowed
S3 bucket deletion → Not allowed
```

This demonstrates how permissions are restricted according to the team's responsibility.

---

# 13. Hands-On Scenario 3 – EC2 Specific Instance Access

This is an important **least-privilege** example.

Suppose the company has:

```text
EC2 Instance
Name = dev-server
Instance ID = i-xxxxxxxxxxxxxxxxx
```

The Developer should be allowed to manage **only this specific EC2 instance**, rather than every EC2 instance.

## Custom IAM Policy

Create:

```text
IAM
 ↓
Policies
 ↓
Create policy
 ↓
JSON
```

Example policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ManageSpecificEC2Instance",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:DescribeInstanceStatus",
        "ec2:StartInstances",
        "ec2:StopInstances",
        "ec2:RebootInstances"
      ],
      "Resource": "arn:aws:ec2:REGION:ACCOUNT_ID:instance/INSTANCE_ID"
    }
  ]
}
```

Replace:

```text
REGION
ACCOUNT_ID
INSTANCE_ID
```

with your actual values.

For example:

```text
INSTANCE_ID
    ↓
i-xxxxxxxxxxxxxxxxx
```

### Important

`DescribeInstances` and related describe APIs can have different resource-level authorization behavior. If AWS requires `"Resource": "*"`, keep that action in a separate statement.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DescribeEC2",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:DescribeInstanceStatus"
      ],
      "Resource": "*"
    },
    {
      "Sid": "ManageSpecificInstance",
      "Effect": "Allow",
      "Action": [
        "ec2:StartInstances",
        "ec2:StopInstances",
        "ec2:RebootInstances"
      ],
      "Resource": "arn:aws:ec2:REGION:ACCOUNT_ID:instance/INSTANCE_ID"
    }
  ]
}
```

### Architecture

```text
Custom EC2 Policy
       ↓
Developer Group
       ↓
Developer User
       ↓
Specific EC2 Instance
```

This is a practical example of **resource-level access control**.

---

# 14. Hands-On Scenario 4 – S3 Access Through AWS CLI

Suppose the application team needs access to one S3 bucket.

Example:

```text
company-devops-demo-bucket
```

The user should be able to:

```text
List bucket
Upload files
Download files
```

but should not be able to delete the entire bucket.

## Custom S3 Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListSpecificBucket",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::company-devops-demo-bucket"
    },
    {
      "Sid": "ReadWriteObjects",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::company-devops-demo-bucket/*"
    }
  ]
}
```

Attach this policy to the appropriate group.

---

# 15. Configure AWS CLI

Install and configure the AWS CLI on your local machine.

Check installation:

```bash
aws --version
```

Configure credentials:

```bash
aws configure
```

AWS CLI will ask for:

```text
AWS Access Key ID
AWS Secret Access Key
Default region name
Default output format
```

Example:

```text
AWS Access Key ID: ********
AWS Secret Access Key: ********
Default region name: us-east-1
Default output format: json
```

### Verify Identity

```bash
aws sts get-caller-identity
```

This should return information about the IAM identity currently being used.

---

# 16. S3 CLI Hands-On

List buckets:

```bash
aws s3 ls
```

List a specific bucket:

```bash
aws s3 ls s3://company-devops-demo-bucket
```

Upload a file:

```bash
aws s3 cp test.txt s3://company-devops-demo-bucket/
```

Download a file:

```bash
aws s3 cp s3://company-devops-demo-bucket/test.txt .
```

Verify:

```bash
aws s3 ls s3://company-devops-demo-bucket
```

Try an unauthorized operation such as deleting the bucket:

```bash
aws s3 rb s3://company-devops-demo-bucket
```

The operation should be denied if the policy does not grant bucket deletion.

---

# 17. Important – Access Keys and Tokens

For local CLI access, an IAM principal may use credentials such as:

```text
Access Key ID
Secret Access Key
```

Temporary credentials can also include:

```text
Access Key ID
Secret Access Key
Session Token
```

When temporary credentials are used, configure all required values.

Never put credentials directly inside source code.

### Never commit:

```text
AWS Access Key
AWS Secret Key
Session Token
```

to GitHub.

---

# 18. Better Approach – IAM Role for EC2

If an application running on EC2 needs S3 access, **do not create an IAM user just for the EC2 server**.

Instead:

```text
EC2 Instance
     ↓
IAM Role
     ↓
S3 Permissions
```

Create an IAM role for EC2 and attach the required S3 policy.

The EC2 instance can then obtain temporary credentials automatically.

This is safer than storing long-term access keys on the server.

---

# 19. Hands-On Scenario 5 – IAM Role for EC2

## Scenario

Our application runs on:

```text
EC2
```

The application needs to upload files to:

```text
S3 Bucket
```

### Solution

Create:

```text
IAM Role
    ↓
EC2
    ↓
S3
```

### Steps

```text
IAM
 ↓
Roles
 ↓
Create role
 ↓
Trusted entity
 ↓
AWS service
 ↓
EC2
 ↓
Attach required S3 policy
 ↓
Create role
```

Then:

```text
EC2
 ↓
Select Instance
 ↓
Actions
 ↓
Security
 ↓
Modify IAM role
 ↓
Select Role
```

Now the application can access S3 using temporary credentials provided through the instance role.

---

# 20. Test IAM Permissions

Always test permissions after creating them.

### Check current identity

```bash
aws sts get-caller-identity
```

### Test S3

```bash
aws s3 ls
```

### Test specific bucket

```bash
aws s3 ls s3://company-devops-demo-bucket
```

### Test EC2

```bash
aws ec2 describe-instances
```

If the user does not have permission:

```text
AccessDenied
```

This is expected when the operation is outside the user's assigned permissions.

---

# 21. Policy Evaluation – Basic Concept

AWS evaluates IAM policies to determine whether an action is allowed.

A simplified model:

```text
Request
   ↓
Authentication
   ↓
Policy Evaluation
   ↓
Allow / Deny
```

An explicit deny takes precedence over an allow.

```text
Allow + No Explicit Deny
        ↓
      ALLOW

Allow + Explicit Deny
        ↓
      DENY
```

---

# 22. JSON Policy Structure

A typical IAM policy contains:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "service:action",
      "Resource": "resource"
    }
  ]
}
```

### Main Components

| Component   | Meaning                       |
| ----------- | ----------------------------- |
| `Version`   | Policy language version       |
| `Statement` | Permission statement          |
| `Effect`    | Allow or Deny                 |
| `Action`    | AWS API operation             |
| `Resource`  | Resource receiving permission |
| `Condition` | Optional conditions           |

---

# 23. Example – Custom Policy for a User

Suppose:

```text
User = aws-03-demo-user
```

Instead of giving the user unrestricted permissions, create a custom policy based on the exact requirement.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowReadOnlyEC2",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:DescribeInstanceStatus"
      ],
      "Resource": "*"
    }
  ]
}
```

Attach the policy to the appropriate group whenever possible.

```text
Custom Policy
      ↓
DevOps / Developer Group
      ↓
IAM User
```

---

# 24. IAM User Login Flow

For a normal IAM console user:

```text
AWS Account
     ↓
IAM User
     ↓
Username + Password
     ↓
MFA
     ↓
AWS Management Console
     ↓
Allowed AWS Services
```

For CLI access:

```text
IAM User / Role
       ↓
AWS Credentials
       ↓
AWS CLI
       ↓
AWS STS
       ↓
AWS Service
```

For EC2:

```text
EC2 Instance
       ↓
IAM Role
       ↓
Temporary Credentials
       ↓
AWS Service
```

---

# 25. Recommended Company Structure

A practical company structure could look like this:

```text
                         AWS Account
                              |
                             IAM
                              |
        +---------------------+---------------------+
        |                     |                     |
     Finance              Monitoring             DevOps
        |                     |                     |
     Group                  Group                 Group
        |                     |                     |
      Users                  Users                 Users
        |                     |                     |
   Billing             CloudWatch             EC2/VPC/S3
   Read-Only             Access               Required Access
```

---

# 26. Access Management Best Practices

### 1. Protect Root User

```text
Root MFA = Enabled
```

### 2. Avoid Daily Root Usage

Use IAM users or, preferably for workforce access, IAM Identity Center where appropriate.

### 3. Use Groups

```text
Policy → Group → User
```

### 4. Follow Least Privilege

Give users only the permissions they require.

### 5. Prefer Roles for AWS Workloads

```text
EC2 → IAM Role → S3
```

instead of:

```text
EC2 → Hard-coded Access Key → S3
```

### 6. Enable MFA

Protect console access with MFA.

### 7. Never Share Credentials

Every person should have their own identity.

### 8. Never Commit Secrets

Do not commit:

```text
Access Keys
Secret Keys
Session Tokens
Passwords
```

to Git repositories.

---

# 27. Final Hands-On Practice Checklist

## Practice 1 – Finance

```text
[ ] Create Finance group
[ ] Add billing read-only permission
[ ] Create Finance user
[ ] Add user to Finance group
[ ] Enable console access if required
[ ] Test billing access
```

## Practice 2 – Monitoring

```text
[ ] Create Monitoring group
[ ] Add CloudWatch permissions
[ ] Create Monitoring user
[ ] Add user to Monitoring group
[ ] Test CloudWatch access
[ ] Test unauthorized EC2 operation
```

## Practice 3 – EC2 Specific Access

```text
[ ] Create Developer group
[ ] Create custom EC2 policy
[ ] Select specific EC2 instance ARN
[ ] Allow start/stop/reboot
[ ] Attach policy to group
[ ] Test with Developer user
```

## Practice 4 – S3 CLI

```text
[ ] Create S3 bucket
[ ] Create S3 access policy
[ ] Attach policy to group/user as appropriate
[ ] Configure AWS CLI
[ ] Run aws sts get-caller-identity
[ ] Upload file
[ ] Download file
[ ] Test unauthorized operation
```

## Practice 5 – EC2 IAM Role

```text
[ ] Create IAM role for EC2
[ ] Attach S3 policy
[ ] Attach role to EC2
[ ] Connect to EC2
[ ] Test S3 access
[ ] Verify no hard-coded credentials are required
```

---

# 28. Final Architecture

```text
                           AWS ACCOUNT
                                |
                         ROOT USER + MFA
                                |
                               IAM
                                |
          +---------------------+---------------------+
          |                     |                     |
      IAM GROUPS            IAM ROLES             POLICIES
          |                     |                     |
   +------+------+              |              +------+------+
   |      |      |              |              |      |      |
Finance Monitoring DevOps    EC2 Role        S3     EC2   CloudWatch
   |      |      |              |              |
 User   User   User             EC2           Bucket
   |
Billing
Read-Only
```

---

# 🎯 What I Learned

Through this hands-on lab, I learned how to:

* Secure the AWS Root User with MFA.
* Create IAM users.
* Create IAM groups.
* Assign policies to groups.
* Create custom JSON IAM policies.
* Apply least-privilege permissions.
* Provide billing access to Finance.
* Provide CloudWatch access to Monitoring.
* Restrict EC2 permissions to a specific instance.
* Provide S3 access through AWS CLI.
* Configure and verify AWS CLI authentication.
* Understand temporary credentials and session tokens.
* Use IAM Roles for EC2 workloads.
* Test allowed and denied AWS operations.
* Understand the difference between IAM Users, Groups, Policies, and Roles.

---

# 🔑 Key Interview Statement

> **In a company environment, I would first secure the Root User with MFA and avoid using it for daily operations. Then I would create IAM groups based on team responsibilities, attach least-privilege policies to those groups, create individual IAM users, and add them to the appropriate groups. For AWS workloads such as EC2, I would prefer IAM Roles with temporary credentials instead of storing long-term access keys. Finally, I would test both allowed and denied actions to verify that permissions are working as expected.**
