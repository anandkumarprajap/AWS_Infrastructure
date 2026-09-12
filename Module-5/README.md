# AWS IAM – Access Management Hands-On Lab

## MFA
![Image 1](1.png)
![Image 2](2.png)
![Image 3](3.png)

---
AWS Root User , IAM Users, IAM Groups, IAM Policies, IAM Roles, JSON Policies, EC2-specific permissions, S3 access, 
AWS CLI authentication, Least Privilege, Permission testing .
---
![Image 4](4.png)
![Image 5](5.png)
![Image 6](6.png)
![Image 7](7.png)
![Image 8](8.png)
![Image 9](9.png)
![Image 11](11.png)
![Image 12](12.png)
![Image 13](13.png)
![Image 14](14.png)
![Image 15](15.png)
![Image 16](16.png)
![Image 17](17.png)
![Image 18](18.png)
![Image 19](19.png)
![Image 20](20.png)
![Image 21](21.png)
![Image 22](22.png)
![Image 23](23.png)
![Image 24](24.png)
![Image 25](25.png)
![Image 26](26.png)
![Image 27](27.png)
![Image 28](28.png)
![Image 29](29.png)
![Image 30](30.png)
![Image 31](31.png)
![Image 32](32.png)
![Image 33](33.png)
![Image 34](34.png)
![Image 35](35.png)
![Image 36](36.png)
![Image 37](37.png)
![Image 38](38.png)
![Image 39](39.png)
![Image 40](40.png)
![Image 41](41.png)
![Image 42](42.png)
![Image 43](43.png)
![Image 44](44.png)
![Image 45](45.png)
![Image 46](46.png)
![Image 47](47.png)
![Image 48](48.png)
![Image 49](49.png)
![Image 50](50.png)
![Image 51](51.png)
![Image 52](52.png)
![Image 53](53.png)
![Image 54](54.png)
![Image 55](55.png)
![Image 56](56.png)
![Image 57](57.png)
![Image 58](58.png)
![Image 59](59.png)
![Image 60](60.png)


# AWS IAM — Users, Groups, Policies, Roles & Access Management

This document contains my beginner-level AWS IAM hands-on practice, including IAM users, groups, permissions, policies, EC2 access, S3 access, IAM roles, AWS CLI testing, and common doubts/mistakes.

---

# 1. What is IAM?

**IAM = Identity and Access Management**

AWS IAM controls:

* Who can access AWS
* What they can access
* What actions they can perform
* Which AWS resources they can access

Simple example:

```text
User
  ↓
Group
  ↓
Policy
  ↓
AWS Resource
```

Example:

```text
Developer_User
      ↓
Developer_Team
      ↓
S3 Policy
      ↓
S3 Bucket
```

---

# 2. IAM Basic Components

## 2.1 User

An IAM User represents a person or application identity that needs AWS access.

Example:

```text
Finance_User
Monitoring_User
DevOps_User
Developer_User
```

---

## 2.2 Group

A Group is a collection of IAM users.

Instead of assigning the same policy to every user individually, we can attach the policy to a group.

Example:

```text
Developer_Team
      ↓
Developer_User
```

If a policy is attached to `Developer_Team`, the user receives that permission through the group.

### Best practice

Prefer:

```text
User → Group → Policy
```

instead of attaching many policies directly to individual users.

---

# 3. IAM Policy

A policy is a JSON document that defines permissions.

A policy answers:

```text
Who can do what on which resource?
```

Basic structure:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "service:Action",
      "Resource": "resource"
    }
  ]
}
```

Important fields:

| Field     | Meaning                 |
| --------- | ----------------------- |
| Version   | Policy language version |
| Statement | Permission rules        |
| Effect    | Allow or Deny           |
| Action    | AWS API operation       |
| Resource  | AWS resource            |

---

# 4. IAM Teams Used in This Lab

I created four teams:

```text
Finance_Team
    ↓
Finance_User
    ↓
Billing Read Only


Monitoring_Team
    ↓
Monitoring_User
    ↓
CloudWatch


DevOps_Team
    ↓
DevOps_User
    ↓
AdministratorAccess


Developer_Team
    ↓
Developer_User
    ↓
EC2 + S3 + Logs
```

---

# 5. Finance Team

## User

```text
Finance_User
```

## Group

```text
Finance_Team
```

## Permission

For billing read-only access:

```text
AWSBillingReadOnlyAccess
```

The Finance user should be able to view billing information but should not have administrative permissions.

---

# 6. Monitoring Team

## User

```text
Monitoring_User
```

## Group

```text
Monitoring_Team
```

## Permission

For a lab/demo:

```text
CloudWatchFullAccess
```

This allows the user to work with CloudWatch.

For production environments, use a more restricted custom policy whenever possible.

---

# 7. DevOps Team

## User

```text
DevOps_User
```

## Group

```text
DevOps_Team
```

## Permission

For this learning lab:

```text
AdministratorAccess
```

### Important

`AdministratorAccess` gives very broad permissions.

It is useful for a lab but should **not normally be given to developers or users in production**.

Production environments should follow:

```text
Least Privilege
```

---

# 8. Direct Permission vs Group Permission

A common doubt:

> If a permission is already available through a group, should I also attach the same policy directly to the user?

Usually, **no**.

Example:

```text
Developer_User
      ↓
Developer_Team
      ↓
S3 Policy
```

If the same S3 policy is also directly attached to `Developer_User`, it is unnecessary duplication.

### Important exception

If the user has:

```text
AdministratorAccess
```

through the group and:

```text
IAMUserChangePassword
```

directly attached, that is not duplicate permission.

They are different policies.

So:

```text
AdministratorAccess → Group
IAMUserChangePassword → Direct
```

can both exist.

---

# 9. Developer Team

## User

```text
Developer_User
```

## Group

```text
Developer_Team
```

Developer permissions should be restricted.

For this lab, the Developer team gets:

```text
EC2 access
S3 access
CloudWatch Logs access
```

The developer should not automatically receive full AWS Administrator permissions.

---

# 10. EC2 Server

My EC2 instance:

```text
Name: server-a
Instance ID: i-002db9ef3862cc539
Region: us-west-2
Instance Type: t3.micro
```

The goal is:

> Developer should be able to control only `server-a`.

Allowed actions:

```text
Start
Stop
Reboot
```

---

# 11. Custom EC2 Policy

Policy name:

```text
Developer-ServerA-EC2-Control
```

Policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ControlSpecificEC2Instance",
      "Effect": "Allow",
      "Action": [
        "ec2:StartInstances",
        "ec2:StopInstances",
        "ec2:RebootInstances"
      ],
      "Resource": "arn:aws:ec2:us-west-2:544341920623:instance/i-002db9ef3862cc539"
    },
    {
      "Sid": "ViewEC2Instances",
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

Attach this policy to:

```text
Developer_Team
```

Not to:

```text
Finance_Team
Monitoring_Team
```

---

# 12. Why is Resource "*" used for Describe?

Some EC2 `Describe` APIs do not support resource-level permissions in the way actions such as `StartInstances` do.

Therefore:

```json
"Resource": "*"
```

is used for:

```text
ec2:DescribeInstances
ec2:DescribeInstanceStatus
```

But the control actions are restricted to one instance.

---

# 13. S3 Bucket

My S3 bucket:

```text
company-devops-demo-bucket-anand
```

Region:

```text
us-west-2
```

Bucket ARN:

```text
arn:aws:s3:::company-devops-demo-bucket-anand
```

Object ARN:

```text
arn:aws:s3:::company-devops-demo-bucket-anand/*
```

---

# 14. Developer S3 Requirement

The developer should be able to:

```text
List bucket       → Allow
Upload object     → Allow
Download object   → Allow
Delete object     → Deny
Delete bucket     → Deny
```

This is an example of:

```text
Least Privilege
```

---

# 15. Custom S3 Policy

Policy name:

```text
Developer-S3-Bucket-Access
```

Policy:

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
      "Resource": "arn:aws:s3:::company-devops-demo-bucket-anand"
    },
    {
      "Sid": "UploadDownloadObjects",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::company-devops-demo-bucket-anand/*"
    }
  ]
}
```

Attach it to:

```text
Developer_Team
```

---

# 16. Why Are There Two Different S3 ARNs?

This is important.

## Bucket-level ARN

```text
arn:aws:s3:::company-devops-demo-bucket-anand
```

Used for:

```text
s3:ListBucket
```

## Object-level ARN

```text
arn:aws:s3:::company-devops-demo-bucket-anand/*
```

Used for:

```text
s3:GetObject
s3:PutObject
s3:DeleteObject
```

Simple rule:

```text
Bucket operation
      ↓
Bucket ARN

Object operation
      ↓
Bucket/* ARN
```

---

# 17. Why Don't We Give s3:*?

Avoid:

```text
s3:*
```

because it provides much more access than required.

Also avoid unnecessarily giving:

```text
AmazonS3FullAccess
```

if the developer only needs:

```text
List
Upload
Download
```

Instead, create a custom least-privilege policy.

---

# 18. Expected S3 Permissions

| Operation       | Expected |
| --------------- | -------- |
| List bucket     | Allow    |
| Upload object   | Allow    |
| Download object | Allow    |
| Delete object   | Deny     |
| Delete bucket   | Deny     |

This is a good IAM testing scenario.

---

# 19. AWS CLI

AWS CLI allows us to interact with AWS services from the terminal.

Example:

```bash
aws sts get-caller-identity
```

This tells us which AWS identity is currently being used.

---

# 20. Check Current AWS Identity

Command:

```bash
aws sts get-caller-identity
```

Example result:

```json
{
  "UserId": "...",
  "Account": "...",
  "Arn": "arn:aws:iam::ACCOUNT_ID:user/Developer_User"
}
```

If the ARN contains:

```text
user/Developer_User
```

the CLI is using the IAM user.

---

# 21. Configure AWS CLI

Method 1
---
```
Developer_User → CLI → S3
```
This is for a human developer using AWS CLI.

Your architecture:
---
            Developer_User
                  ↓
           Developer_Team
                  ↓
       Developer-S3-Bucket-Access
                  ↓
    S3: company-devops-demo-bucket-anand
---
Step 1: 
```text
Create access key for Developer_User
```

Go to:
```text
IAM → Users → Developer_User → Security credentials → Access keys → Create access key
```

Then on your computer:

```bash
aws configure
```

Enter:
```text
AWS Access Key ID: YOUR_DEVELOPER_ACCESS_KEY
AWS Secret Access Key: YOUR_DEVELOPER_SECRET_KEY
Default region name: us-west-2
Default output format: json
```
Step 2: Verify the user
aws sts get-caller-identity

You should see:
```text
arn:aws:iam::544341920623:user/Developer_User
```
Step 3: Test your bucket
```text
aws s3 ls s3://company-devops-demo-bucket-anand
```
Method 2 
---
```
SSH into server-a → EC2 Role → S3
```
This is the IAM Role scenario you just created.

Architecture:
---
            Your Computer
                 │
                 │ SSH
                 ▼
            server-a EC2
                 │
                 ▼
         EC2-S3-Access-Role
                 │
                 ▼
                S3

---
```test
company-devops-demo-bucket-anand
```
Basic command:

```bash
aws configure
```

It asks for:

```text
AWS Access Key ID
AWS Secret Access Key
Default region name
Default output format
```

Example region:

```text
us-west-2
```

### Security rule

Never share:

```text
AWS Access Key ID
AWS Secret Access Key
```

in GitHub, screenshots, README files, chat messages, or public posts.

If credentials are accidentally exposed:

```text
Immediately deactivate/delete the access key
Create a replacement credential if required
```

---

# 22. Test S3 From EC2

After configuring AWS CLI:

```bash
aws s3 ls s3://company-devops-demo-bucket-anand
```

This lists objects in the bucket.

---

# 23. Create a Test File

```bash
echo "Hello from server-a EC2" > test.txt
```

Check the file:

```bash
ls
```

Read it:

```bash
cat test.txt
```

Expected:

```text
Hello from server-a EC2
```

---

# 24. Upload File to S3

```bash
aws s3 cp test.txt s3://company-devops-demo-bucket-anand/
```

Expected:

```text
upload: ./test.txt to s3://company-devops-demo-bucket-anand/test.txt
```

---

# 25. Verify Upload

```bash
aws s3 ls s3://company-devops-demo-bucket-anand
```

Example:

```text
2026-09-12 13:11:08  24 test.txt
```

---

# 26. Download File

First remove the local copy:

```bash
rm test.txt
```

Then download:

```bash
aws s3 cp s3://company-devops-demo-bucket-anand/test.txt .
```

Read it:

```bash
cat test.txt
```

Expected:

```text
Hello from server-a EC2
```

---

# 27. Test Delete Permission

Run:

```bash
aws s3 rm s3://company-devops-demo-bucket-anand/test.txt
```

Expected:

```text
AccessDenied
```

This is correct.

Why?

Because our policy contains:

```text
s3:GetObject
s3:PutObject
```

but does not contain:

```text
s3:DeleteObject
```

Therefore:

```text
Upload      → Allowed
Download    → Allowed
Delete      → Denied
```

This proves that least privilege is working.

---

# 28. IAM Role

An IAM Role is different from an IAM User.

A role is commonly used by:

* EC2
* Lambda
* ECS
* EKS workloads
* Other AWS services/applications

Simple example:

```text
EC2
 ↓
IAM Role
 ↓
S3 Permission
 ↓
S3 Bucket
```

---

# 29. EC2-S3-Access-Role

I created:

```text
EC2-S3-Access-Role
```

Trust entity:

```text
AWS Service → EC2
```

Trust policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "sts:AssumeRole"
      ],
      "Principal": {
        "Service": [
          "ec2.amazonaws.com"
        ]
      }
    }
  ]
}
```

This means:

```text
EC2 is allowed to assume this role.
```

---

# 30. Trust Policy vs Permission Policy

This is an important IAM concept.

## Trust Policy

Answers:

> Who can assume this role?

Example:

```text
EC2
```

## Permission Policy

Answers:

> What can the role do?

Example:

```text
List S3 bucket
Get S3 objects
Put S3 objects
```

Architecture:

```text
              IAM Role
                 │
       ┌─────────┴─────────┐
       │                   │
Trust Policy       Permission Policy
       │                   │
      EC2             S3 Access
```

---

# 31. S3 Permission for EC2 Role

The role should use a least-privilege policy:

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
      "Resource": "arn:aws:s3:::company-devops-demo-bucket-anand"
    },
    {
      "Sid": "UploadDownloadObjects",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::company-devops-demo-bucket-anand/*"
    }
  ]
}
```

Suggested inline policy name:

```text
EC2-S3-Application-Access
```

---

# 32. Important Difference: User vs Role

This was one of the main doubts.

## Human Developer

```text
Developer_User
      ↓
Developer_Team
      ↓
S3 Policy
      ↓
AWS CLI
      ↓
S3
```

## EC2 Application

```text
EC2
 ↓
EC2-S3-Access-Role
 ↓
S3 Policy
 ↓
S3
```

The role is **not another developer user**.

The role is for the EC2 workload.

---

# 33. Why Use an IAM Role for EC2?

Without a role, we might put AWS access keys on the EC2 server.

That is not a good practice.

With a role:

```text
EC2
 ↓
IAM Role
 ↓
Temporary AWS credentials
 ↓
S3
```

This avoids storing long-lived AWS credentials on the server.

### Interview Answer

> I use an IAM role when an AWS workload such as EC2 needs to access another AWS service such as S3. Instead of storing long-term access keys on the EC2 instance, I attach a least-privilege IAM role to the instance, and AWS provides temporary credentials to the workload.

---

# 34. Very Important: SSH vs IAM Role

SSH login and IAM permissions are different.

SSH:

```text
Laptop
   ↓
SSH Key Pair
   ↓
EC2
```

IAM role:

```text
EC2
 ↓
IAM Role
 ↓
AWS Services
```

SSH allows us to log in to the Linux server.

The IAM role allows the server/application to call AWS APIs.

---

# 35. Attaching the IAM Role to EC2

Console steps:

```text
AWS Console
   ↓
EC2
   ↓
Instances
   ↓
Select server-a
   ↓
Actions
   ↓
Security
   ↓
Modify IAM role
   ↓
Select EC2-S3-Access-Role
   ↓
Update IAM role
```

After this, the EC2 instance can obtain credentials through the attached role.

---

# 36. Testing the EC2 Role

After attaching the role, check:

```bash
aws sts get-caller-identity
```

If the role is being used, the ARN should look similar to:

```text
arn:aws:sts::ACCOUNT_ID:assumed-role/EC2-S3-Access-Role/...
```

This is different from:

```text
arn:aws:iam::ACCOUNT_ID:user/Developer_User
```

---

# 37. Important CLI Credential Problem

If AWS CLI has credentials configured for:

```text
Developer_User
```

then the CLI may continue using those credentials instead of the EC2 role.

Therefore, when testing the role, remove/unset the manually configured credentials from the EC2 machine.

Common AWS CLI credential locations include:

```text
~/.aws/credentials
~/.aws/config
```

Do not leave long-lived credentials on EC2 unnecessarily.

Then test:

```bash
aws sts get-caller-identity
```

---

# 38. Test EC2 Role With S3

List:

```bash
aws s3 ls s3://company-devops-demo-bucket-anand
```

Create:

```bash
echo "Hello from EC2 IAM Role" > role-test.txt
```

Upload:

```bash
aws s3 cp role-test.txt s3://company-devops-demo-bucket-anand/
```

Download:

```bash
aws s3 cp s3://company-devops-demo-bucket-anand/role-test.txt .
```

Delete:

```bash
aws s3 rm s3://company-devops-demo-bucket-anand/role-test.txt
```

Expected:

```text
List       → Allowed
Upload     → Allowed
Download   → Allowed
Delete     → AccessDenied
```

---

# 39. Common Doubts and Mistakes

## Doubt 1: Should I attach the same policy to both user and group?

Usually no.

Use:

```text
User → Group → Policy
```

Avoid duplicate direct permissions unless there is a specific reason.

---

## Doubt 2: Is AdministratorAccess good for Developer?

No, not for production.

It is being used here only as a **DevOps learning-lab example**.

Developers should normally receive only the permissions they need.

---

## Doubt 3: Why can't the developer delete the S3 object?

Because the policy does not contain:

```text
s3:DeleteObject
```

This is expected and demonstrates least privilege.

---

## Doubt 4: Why does S3 need two ARNs?

Because:

```text
ListBucket → bucket ARN
GetObject/PutObject → object ARN
```

---

## Doubt 5: Why does EC2 need an IAM role?

Because the application running on EC2 may need to access AWS services.

Example:

```text
EC2 application → S3
```

The role provides temporary credentials.

---

## Doubt 6: Should Developer_User be added to the EC2 role?

No.

The EC2 role is for:

```text
EC2 workload
```

The developer user is for:

```text
Human access
```

---

## Doubt 7: Does IAM role provide SSH access?

No.

SSH access uses the EC2 key pair and operating-system permissions.

IAM role controls AWS API access from the EC2 workload.

---

## Doubt 8: Does AmazonS3ReadOnlyAccess allow uploading?

No.

Read-only access does not provide:

```text
s3:PutObject
```

Therefore upload will fail.

---

# 40. Least Privilege

Least privilege means:

> Give only the permissions that are actually required.

Bad:

```text
Developer → AdministratorAccess
```

Better:

```text
Developer
   ↓
Specific EC2 permissions
   +
Specific S3 permissions
   +
Required CloudWatch permissions
```

Example:

```text
EC2:
Start
Stop
Reboot
View

S3:
List
Upload
Download

Delete:
Denied
```

---

# 41. IAM Permission Flow

Human user:

```text
Developer_User
       ↓
Developer_Team
       ↓
IAM Policy
       ↓
AWS API
       ↓
EC2 / S3 / CloudWatch
```

EC2 application:

```text
EC2
 ↓
IAM Role
 ↓
IAM Permission Policy
 ↓
AWS API
 ↓
S3
```

---

# 42. Complete Lab Architecture

```text
                         AWS ACCOUNT
                              |
                         IAM SERVICE
                              |
        ┌─────────────────────┼─────────────────────┐
        |                     |                     |
   Finance_Team          Monitoring_Team       DevOps_Team
        |                     |                     |
 Finance_User          Monitoring_User         DevOps_User
        |                     |                     |
 Billing Read Only      CloudWatch Access     AdministratorAccess
                                                   |
                                                   |
                                            LAB ONLY


                       Developer_Team
                              |
                       Developer_User
                              |
                ┌─────────────┼─────────────┐
                |             |             |
               EC2           S3        CloudWatch Logs
                |
            server-a
```

---

# 43. EC2 Workload Architecture

```text
                  EC2 server-a
                       |
                       |
              EC2-S3-Access-Role
                       |
                       |
              S3 Permission Policy
                       |
                       |
            company-devops-demo-bucket
```

---

# 44. Human vs Workload Access

```text
HUMAN
Developer_User
      |
Developer_Team
      |
Policy
      |
AWS CLI
      |
S3


WORKLOAD
EC2
 |
IAM Role
 |
Policy
 |
AWS SDK / CLI / Application
 |
S3
```

---

# 45. Useful IAM Commands

Check identity:

```bash
aws sts get-caller-identity
```

List S3 bucket:

```bash
aws s3 ls s3://company-devops-demo-bucket-anand
```

Upload:

```bash
aws s3 cp test.txt s3://company-devops-demo-bucket-anand/
```

Download:

```bash
aws s3 cp s3://company-devops-demo-bucket-anand/test.txt .
```

Delete:

```bash
aws s3 rm s3://company-devops-demo-bucket-anand/test.txt
```

Configure CLI:

```bash
aws configure
```

---

# 46. IAM Security Best Practices

## Root User

Do not use the root user for normal daily AWS work.

Enable:

```text
Root MFA
```

Use IAM identities for normal administration.

---

## MFA

Enable MFA for important users, especially privileged users.

For example:

```text
DevOps_User
```

---

## Access Keys

Do not expose access keys.

Never commit them to:

```text
GitHub
README.md
Source code
Screenshots
Chat
Public posts
```

If exposed:

```text
Deactivate/Delete
    ↓
Create replacement
    ↓
Update secure configuration
```

---

## Least Privilege

Give only required permissions.

---

## Groups

Use groups to manage permissions for humans.

---

## Roles

Use roles for AWS workloads such as EC2.

---

# 47. Simple IAM Rules to Remember

### Rule 1

```text
Human → IAM User/Group
```

### Rule 2

```text
AWS Workload → IAM Role
```

### Rule 3

```text
Permissions → IAM Policy
```

### Rule 4

```text
Group → Common permissions for users
```

### Rule 5

```text
Least Privilege → Minimum required access
```

### Rule 6

```text
SSH → EC2 login
```

### Rule 7

```text
IAM Role → AWS service access
```

---

# 48. Final Testing Checklist

## Finance

```text
[ ] Finance_User created
[ ] Finance_Team created
[ ] Billing read-only policy attached
```

## Monitoring

```text
[ ] Monitoring_User created
[ ] Monitoring_Team created
[ ] CloudWatch permission attached
```

## DevOps

```text
[ ] DevOps_User created
[ ] DevOps_Team created
[ ] AdministratorAccess attached
[ ] MFA enabled
```

## Developer

```text
[ ] Developer_User created
[ ] Developer_Team created
[ ] EC2 custom policy attached
[ ] S3 custom policy attached
[ ] CloudWatch Logs permission added if required
```

## EC2

```text
[ ] server-a created
[ ] EC2 policy tested
[ ] Start tested
[ ] Stop tested
[ ] Reboot tested
```

## S3

```text
[ ] Bucket created
[ ] List tested
[ ] Upload tested
[ ] Download tested
[ ] Delete denied and verified
```

## IAM Role

```text
[ ] EC2-S3-Access-Role created
[ ] EC2 trust policy configured
[ ] S3 permission policy attached
[ ] Role attached to server-a
[ ] aws sts get-caller-identity tested
[ ] EC2 → S3 access tested
```

---

# 49. Interview Questions

## What is IAM?

> IAM is an AWS service used to securely manage identities and permissions. It controls who can access AWS resources and what actions they can perform.

## What is an IAM Group?

> An IAM group is a collection of IAM users. We can attach common policies to the group instead of managing permissions individually for every user.

## What is an IAM Policy?

> An IAM policy is a JSON document that defines which actions are allowed or denied on specific AWS resources.

## What is an IAM Role?

> An IAM role provides temporary permissions to trusted entities such as EC2, Lambda, or other AWS workloads without requiring long-term access keys.

## Why use an IAM role with EC2?

> To allow applications running on EC2 to securely access AWS services such as S3 using temporary credentials instead of storing long-term access keys on the server.

## What is least privilege?

> Least privilege means giving a user or workload only the minimum permissions required to perform its job.

## Why can the developer upload to S3 but not delete?

> Because the custom policy allows `s3:PutObject` and `s3:GetObject`, but it does not allow `s3:DeleteObject`.

---

# 50. Final Summary

The complete IAM learning flow is:

```text
                    AWS IAM
                       |
        ┌──────────────┼──────────────┐
        |              |              |
      Users          Groups         Roles
        |              |              |
        |              |              |
        └────────── Policies ─────────┘
                       |
                 AWS Permissions
                       |
        ┌──────────────┼──────────────┐
        |              |              |
       EC2             S3         CloudWatch
```

The most important concepts learned are:

```text
IAM User
IAM Group
IAM Policy
IAM Role
Trust Policy
Permission Policy
Least Privilege
MFA
AWS CLI
EC2 IAM Role
S3 Bucket Permissions
Object Permissions
Access Testing
AccessDenied troubleshooting
```

The key architecture to remember:

```text
Human Developer
      ↓
Developer_User
      ↓
Developer_Team
      ↓
Least-Privilege Policies
      ↓
AWS Resources


EC2 Application
      ↓
EC2-S3-Access-Role
      ↓
Least-Privilege S3 Policy
      ↓
S3 Bucket
```

**Main lesson:**

> Users are generally for people, roles are generally for workloads, groups simplify human permission management, policies define permissions, and least privilege keeps AWS access secure.
