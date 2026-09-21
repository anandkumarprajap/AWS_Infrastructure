# AWS IAM Role + S3 + EC2 Hands-On Lab

## 1. Lab Objective

This lab demonstrates how to:

- Create an S3 bucket.
- Upload files such as `Dockerfile` and `.gitignore` to S3.
- Create an S3 read-only IAM policy.
- Create an IAM role for an EC2 instance.
- Attach the role to an EC2 instance through an instance profile.
- Connect to EC2 using SSH.
- Use AWS CLI without running `aws configure` on the EC2 server.
- Verify temporary credentials through AWS STS.
- Download an object from S3.
- Confirm that S3 upload is denied because the role is read-only.
- Practice cross-account role assumption.
- Understand temporary credentials and STS.
- Document role-based access architecture.

---

# 2. Architecture

```mermaid
flowchart TD
    A[Developer / Admin] --> B[AWS IAM]
    B --> C[S3 ReadOnly Policy]
    C --> D[EC2 Instance Role<br/>ec2-instance-profile-role]
    D --> E[EC2 Instance Profile]
    E --> F[EC2 Instance]

    F --> G[AWS CLI]
    G --> H[AWS STS<br/>GetCallerIdentity]
    G --> I[S3 API]

    I --> J[(S3 Bucket<br/>babu-iam-demo)]
    J --> K[Dockerfile]
    J --> L[.gitignore]
    J --> M[Other uploaded files]

    I --> N[GetObject / ListBucket]
    I --> O[PutObject]
    N --> P[ALLOWED]
    O --> Q[DENIED<br/>Read-only role]

    R[Account A] --> S[AssumeRole]
    S --> T[Account B Role]
    T --> U[Temporary STS Credentials]
    U --> J
```

## Simple Flow

```text
EC2
 |
 | Instance Profile
 v
ec2-instance-profile-role
 |
 | IAM Policy
 v
s3-readonly-policy
 |
 v
S3 Bucket: babu-iam-demo
 |
 +-- Dockerfile
 +-- .gitignore
 +-- upload.txt

EC2 can:
  s3 ls
  s3 cp s3://babu-iam-demo/file .

EC2 cannot:
  s3 cp upload.txt s3://babu-iam-demo/
```

---

# 3. Prerequisites

- AWS account
- IAM permissions to create IAM policies and roles
- EC2 instance
- S3 bucket
- AWS CLI installed on EC2
- SSH access to EC2

**Security note:** Do not put AWS access keys or secret keys inside `Dockerfile`, `.gitignore`, source code, AMIs, or Git repositories.

---

# 4. Create S3 Bucket

Bucket name:

```text
babu-iam-demo
```

AWS CLI:

```bash
aws s3 mb s3://babu-iam-demo
```

Verify:

```bash
aws s3 ls
```

Verify the specific bucket:

```bash
aws s3 ls s3://babu-iam-demo
```

> S3 bucket names are globally unique. If this exact name is already taken, add a unique suffix.

---

# 5. Create Files Locally

Example `Dockerfile`:

```dockerfile
FROM ubuntu:24.04

RUN apt-get update && \
    apt-get install -y nginx && \
    rm -rf /var/lib/apt/lists/*

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

Example `.gitignore`:

```gitignore
.env
*.pem
*.key
.aws/
terraform.tfstate
terraform.tfstate.*
__pycache__/
node_modules/
```

Create a test file:

```bash
echo "Hello from S3 IAM lab" > upload.txt
```

Check:

```bash
ls -la
```

---

# 6. Upload Files to S3

Upload Dockerfile:

```bash
aws s3 cp Dockerfile s3://babu-iam-demo/
```

Upload `.gitignore`:

```bash
aws s3 cp .gitignore s3://babu-iam-demo/
```

Upload another file:

```bash
aws s3 cp upload.txt s3://babu-iam-demo/
```

List objects:

```bash
aws s3 ls s3://babu-iam-demo/
```

---

# 7. Create S3 Read-Only IAM Policy

Policy name:

```text
s3-readonly-policy
```

Recommended policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListBucket",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::babu-iam-demo"
    },
    {
      "Sid": "ReadObjects",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::babu-iam-demo/*"
    }
  ]
}
```

### Why two resources?

S3 uses:

```text
arn:aws:s3:::babu-iam-demo
```

for the bucket itself.

Objects use:

```text
arn:aws:s3:::babu-iam-demo/*
```

Therefore:

- `s3:ListBucket` → bucket ARN
- `s3:GetObject` → object ARN

---

# 8. Create the Policy

AWS Console:

```text
IAM
  → Policies
  → Create policy
  → JSON
  → Paste policy
  → Next
  → Policy name:
      s3-readonly-policy
  → Create policy
```

CLI alternative:

```bash
aws iam create-policy \
  --policy-name s3-readonly-policy \
  --policy-document file://s3-readonly-policy.json
```

---

# 9. Create EC2 IAM Role

Role name:

```text
ec2-instance-profile-role
```

AWS Console:

```text
IAM
  → Roles
  → Create role
  → Trusted entity type: AWS service
  → Use case: EC2
  → Next
```

Attach:

```text
s3-readonly-policy
```

Then create:

```text
ec2-instance-profile-role
```

The EC2 service needs this trust relationship:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

This means EC2 is allowed to assume the role.

---

# 10. Role vs Instance Profile

Important distinction:

```text
IAM Role
    |
    | attached to
    v
Instance Profile
    |
    | attached to
    v
EC2 Instance
```

For EC2, the role is supplied through an **instance profile**.

The instance profile normally has the same or a related name:

```text
ec2-instance-profile-role
```

Do not confuse:

```text
IAM Policy
IAM Role
Instance Profile
EC2 Instance
```

They are different AWS objects.

---

# 11. Launch EC2

Create an EC2 instance.

Example:

```text
AMI: Ubuntu
Instance type: t3.micro
```

Configure SSH access using your key pair.

After launching:

```text
EC2
 → Instances
 → Select instance
 → Security
 → Modify IAM role
 → IAM role:
      ec2-instance-profile-role
 → Update IAM role
```

The role is now attached to the running EC2 instance.

---

# 12. Connect to EC2

Example:

```bash
ssh -i my-key.pem ubuntu@EC2_PUBLIC_IP
```

Verify the server:

```bash
whoami
```

```bash
hostname
```

---

# 13. Update Ubuntu

```bash
sudo apt update
```

Optional upgrade:

```bash
sudo apt upgrade -y
```

---

# 14. Install AWS CLI

On Ubuntu:

```bash
sudo apt install -y awscli
```

Check:

```bash
aws --version
```

Example output:

```text
aws-cli/2.x.x ...
```

The exact version may differ.

---

# 15. Check AWS CLI Configuration

Run:

```bash
aws configure list
```

For an EC2 instance using an IAM role, you should not need to configure long-term access keys manually.

You may see credentials coming from:

```text
iam-role
```

or an EC2 metadata credential provider.

Check the identity:

```bash
aws sts get-caller-identity
```

Expected concept:

```json
{
    "UserId": "...",
    "Account": "123456789012",
    "Arn": "arn:aws:sts::123456789012:assumed-role/ec2-instance-profile-role/..."
}
```

The important part is:

```text
assumed-role/ec2-instance-profile-role
```

This proves the EC2 instance is using the IAM role.

---

# 16. What is AWS STS?

STS means:

```text
AWS Security Token Service
```

STS provides temporary security credentials.

An EC2 instance using an IAM role receives temporary credentials automatically.

Conceptually:

```text
EC2
 |
 v
Instance Metadata Service
 |
 v
Temporary Credentials
 |
 v
AWS STS / AWS APIs
```

Check the current identity:

```bash
aws sts get-caller-identity
```

This does not mean you manually created access keys.

The credentials are provided through the attached IAM role.

---

# 17. Test S3 Read Access

List the bucket:

```bash
aws s3 ls s3://babu-iam-demo
```

Expected:

```text
Dockerfile
.gitignore
upload.txt
```

Download Dockerfile:

```bash
aws s3 cp s3://babu-iam-demo/Dockerfile .
```

Check:

```bash
ls -l
```

Read it:

```bash
cat Dockerfile
```

---

# 18. Test Read-Only Restriction

Create a file on EC2:

```bash
echo "Testing upload from EC2" > upload-from-ec2.txt
```

Try to upload:

```bash
aws s3 cp upload-from-ec2.txt s3://babu-iam-demo/
```

Expected result:

```text
AccessDenied
```

Why?

The role has:

```text
s3:ListBucket
s3:GetObject
```

But it does not have:

```text
s3:PutObject
```

Therefore:

```text
Download     → ALLOWED
List bucket  → ALLOWED
Upload       → DENIED
Delete       → DENIED
```

This is the main IAM role-based access-control test.

---

# 19. Test Object Download

```bash
aws s3 cp s3://babu-iam-demo/upload.txt .
```

Then:

```bash
cat upload.txt
```

This should work because the role has:

```text
s3:GetObject
```

---

# 20. Why `aws configure` Is Not Required

On a normal local machine, you might run:

```bash
aws configure
```

and provide:

```text
AWS Access Key ID
AWS Secret Access Key
AWS Region
```

For EC2 IAM roles, avoid storing long-term access keys on the server.

Instead:

```text
EC2
 ↓
IAM Instance Profile
 ↓
Temporary Credentials
 ↓
AWS CLI
 ↓
S3
```

This is safer and is the standard pattern for AWS workloads.

---

# 21. Role-Based Access Test

| Action | Permission | Result |
|---|---|---|
| `aws s3 ls s3://babu-iam-demo` | `s3:ListBucket` | ALLOWED |
| Download object | `s3:GetObject` | ALLOWED |
| Upload object | `s3:PutObject` | DENIED |
| Delete object | `s3:DeleteObject` | DENIED |
| `aws sts get-caller-identity` | STS identity operation | ALLOWED |

---

# 22. Cross-Account Role Assumption

Example architecture:

```text
AWS Account A
Developer / EC2
      |
      | sts:AssumeRole
      v
AWS Account B
      |
      v
CrossAccountS3ReadRole
      |
      v
S3 resources
```

## Account B: Create Trust Relationship

The role in Account B must trust an identity from Account A.

Conceptual trust policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::ACCOUNT_A_ID:root"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

For production, use the narrowest appropriate principal rather than trusting an entire account when possible.

---

# 23. Account A: Allow AssumeRole

The caller also needs permission to assume the target role.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::ACCOUNT_B_ID:role/CrossAccountS3ReadRole"
    }
  ]
}
```

Both sides matter:

```text
Account A identity
      |
      | Allow sts:AssumeRole
      v
Account B role
      |
      | Trust Account A
      v
Temporary credentials
```

---

# 24. Assume the Cross-Account Role

From Account A:

```bash
aws sts assume-role \
  --role-arn arn:aws:iam::ACCOUNT_B_ID:role/CrossAccountS3ReadRole \
  --role-session-name cross-account-demo
```

AWS STS returns temporary credentials:

```text
AccessKeyId
SecretAccessKey
SessionToken
Expiration
```

These credentials are temporary and should be handled securely.

---

# 25. Test the Assumed Identity

After configuring the temporary credentials in a safe test environment:

```bash
aws sts get-caller-identity
```

Expected identity concept:

```text
arn:aws:sts::ACCOUNT_B_ID:assumed-role/CrossAccountS3ReadRole/cross-account-demo
```

This demonstrates cross-account role assumption.

---

# 26. Important IAM Security Rules

### Do not do this

```bash
aws configure
```

with permanent IAM access keys on an EC2 server when an IAM role can be used.

Do not commit:

```text
.aws/credentials
.env
*.pem
*.key
secret.txt
access-key.txt
```

to Git.

Example `.gitignore`:

```gitignore
.env
.aws/
*.pem
*.key
credentials
secret.txt
terraform.tfstate
terraform.tfstate.*
```

### If an access key is accidentally committed

Deleting the file is not enough.

Immediately:

1. Disable/delete the exposed access key.
2. Create a replacement credential only if needed.
3. Remove the secret from Git history.
4. Check CloudTrail for suspicious usage.
5. Rotate related credentials.
6. Scan the repository for additional secrets.

---

# 27. Useful Verification Commands

Check AWS identity:

```bash
aws sts get-caller-identity
```

List buckets:

```bash
aws s3 ls
```

List objects:

```bash
aws s3 ls s3://babu-iam-demo
```

Download:

```bash
aws s3 cp s3://babu-iam-demo/Dockerfile .
```

Try upload:

```bash
aws s3 cp upload-from-ec2.txt s3://babu-iam-demo/
```

Expected:

```text
AccessDenied
```

Check CLI configuration:

```bash
aws configure list
```

Check attached IAM role through AWS identity:

```bash
aws sts get-caller-identity
```

---

# 28. Troubleshooting

## Problem: `aws: command not found`

Install:

```bash
sudo apt update
sudo apt install -y awscli
```

Verify:

```bash
aws --version
```

---

## Problem: `AccessDenied` for `s3:ListBucket`

Check that the policy contains:

```json
"Action": "s3:ListBucket"
```

and the resource is the bucket ARN:

```text
arn:aws:s3:::babu-iam-demo
```

---

## Problem: `AccessDenied` for downloading an object

Check:

```json
"Action": "s3:GetObject"
```

and:

```text
arn:aws:s3:::babu-iam-demo/*
```

---

## Problem: Upload fails

If the role is intentionally read-only, this is expected.

The policy does not contain:

```text
s3:PutObject
```

That is a successful IAM security test.

---

## Problem: `get-caller-identity` shows the wrong identity

Check:

```bash
aws configure list
```

Then inspect environment variables:

```bash
env | grep '^AWS_'
```

If manually configured credentials exist, they can take precedence over the intended role credentials.

Remove only credentials that you intentionally configured for this test.

---

# 29. Complete Lab Flow

```text
1. Create S3 bucket
       |
       v
2. Upload Dockerfile / .gitignore / upload.txt
       |
       v
3. Create s3-readonly-policy
       |
       v
4. Create ec2-instance-profile-role
       |
       v
5. Attach s3-readonly-policy
       |
       v
6. Launch EC2
       |
       v
7. Attach IAM role through Modify IAM role
       |
       v
8. SSH into EC2
       |
       v
9. Install AWS CLI
       |
       v
10. aws sts get-caller-identity
       |
       v
11. aws s3 ls
       |
       v
12. Download S3 object
       |
       v
13. Try upload
       |
       v
14. AccessDenied = expected
       |
       v
15. Practice STS AssumeRole
       |
       v
16. Practice cross-account access
```

---

# 30. Interview Explanation

### Question: Why use an IAM role with EC2?

**Answer:**

> I use an IAM role with an EC2 instance so the application can access AWS services without storing permanent access keys on the server. EC2 receives temporary credentials through the instance profile, and AWS STS manages the temporary identity.

### Question: Why did S3 download work but upload fail?

**Answer:**

> I attached an S3 read-only policy to the EC2 role. The policy allows `s3:ListBucket` and `s3:GetObject`, but it does not allow `s3:PutObject`. Therefore, downloading works and uploading returns AccessDenied.

### Question: What does `aws sts get-caller-identity` do?

**Answer:**

> It shows the AWS identity currently being used by the AWS CLI, including the account and ARN. On EC2, it can confirm that the CLI is using the attached IAM role.

### Question: What is an instance profile?

**Answer:**

> An instance profile is the container used to pass an IAM role to an EC2 instance. EC2 uses the role through the instance profile.

### Question: What is cross-account role assumption?

**Answer:**

> Cross-account role assumption allows an identity in one AWS account to assume an IAM role in another AWS account using AWS STS. The target role must trust the source identity, and the source identity must have permission to call `sts:AssumeRole`.

---

# 31. Final Architecture Summary

```text
                         AWS ACCOUNT
                              |
              +---------------+---------------+
              |                               |
             IAM                              S3
              |                               |
      +-------+--------+                babu-iam-demo
      |                |                      |
   Policy             Role              +-----+------+
      |                |                |            |
s3-readonly-policy     |            Dockerfile    .gitignore
                       |
          ec2-instance-profile-role
                       |
                       v
                    EC2
                       |
                       v
                  AWS CLI
                       |
             +---------+---------+
             |                   |
            STS                 S3
             |                   |
   get-caller-identity     GetObject/ListBucket
             |                   |
             +---------+---------+
                       |
                  PutObject
                       |
                    DENIED
```

---

# 32. Lab Outcome

After completing this lab, you should be able to explain and demonstrate:

- S3 bucket and object access.
- IAM policies.
- IAM roles.
- EC2 instance profiles.
- EC2 role attachment.
- Temporary credentials.
- AWS STS.
- `aws sts get-caller-identity`.
- S3 read-only permissions.
- Why `PutObject` is denied.
- Cross-account `AssumeRole`.
- Role-based access patterns.
- Basic IAM security practices.


# AWS IAM Role + S3 + EC2 Hands-On Lab


![Image 1](1.png)
![Image 2](2.png)
![Image 3](3.png)
![Image 4](4.png)
![Image 5](5.png)
![Image 6](6.png)
![Image 7](7.png)
![Image 8](8.png)
![Image 9](9.png)
![Image 10](10.png)
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
