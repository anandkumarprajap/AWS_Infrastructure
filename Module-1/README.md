# ☁️ AWS Cloud Computing Fundamentals

![Image 1](1.png)

A beginner-friendly guide to **Cloud Computing, IaaS, PaaS, SaaS, and core AWS services**.

This README covers the foundational AWS concepts you should understand before moving into topics such as **VPC design, High Availability, Auto Scaling, Kubernetes, CI/CD, and Production Architecture**.

---

# 📚 Table of Contents

* [1. Cloud Computing](#1-cloud-computing)
* [2. IaaS, PaaS and SaaS](#2-iaas-paas-and-saas)
* [3. PaaS — Platform as a Service](#3-paas--platform-as-a-service)
* [4. SaaS — Software as a Service](#4-saas--software-as-a-service)
* [5. IaaS vs PaaS vs SaaS](#5-iaas-vs-paas-vs-saas)
* [6. Why Choose AWS?](#6-why-choose-aws)
* [7. AWS Core Services](#7-aws-core-services)
* [8. Compute — EC2 and Lambda](#8-compute--ec2-and-lambda)
* [9. EC2 vs Lambda](#9-ec2-vs-lambda)
* [10. Storage — S3 and EBS](#10-storage--s3-and-ebs)
* [11. EBS — Elastic Block Store](#11-ebs--elastic-block-store)
* [12. Database — RDS and DynamoDB](#12-database--rds-and-dynamodb)
* [13. Networking — VPC and Route 53](#13-networking--vpc-and-route-53)
* [14. Route 53](#14-route-53)
* [15. Security — IAM and KMS](#15-security--iam-and-kms)
* [16. KMS](#16-kms)
* [17. Monitoring — CloudWatch](#17-monitoring--cloudwatch)
* [18. AWS Billing and Cost Management](#18-aws-billing-and-cost-management)
* [19. Six AWS Categories — Easy Revision](#19-six-aws-categories--easy-revision)
* [20. Real-World AWS Architecture Example](#20-real-world-aws-architecture-example)
* [21. Quick Revision](#21-quick-revision)

---

# 1. ☁️ Cloud Computing

**Cloud computing** means using computing resources over the internet instead of buying and maintaining your own physical servers.

## Traditional Approach

In the traditional approach, a company needs to purchase and maintain its own infrastructure.

```text
Company
   ↓
Buy physical server
   ↓
Install hardware
   ↓
Install OS
   ↓
Configure network
   ↓
Maintain hardware
```

This requires:

* 💰 High initial investment
* 🖥️ Physical hardware
* 🔧 Hardware maintenance
* 🌐 Network configuration
* 👨‍💻 Infrastructure administration

---

## Cloud Approach

With cloud computing, you can rent computing resources from a cloud provider.

```text
Company
   ↓
AWS / Azure / Google Cloud
   ↓
Select required resources
   ↓
Use resources through Internet
   ↓
Pay for what you use
```

### Example

Suppose you want to run a website.

Instead of buying a physical server for ₹5 lakh, you can use an **AWS EC2 instance** and pay according to your usage.

### Main Benefits of Cloud Computing

* ✅ No need to purchase physical servers
* ✅ Lower upfront infrastructure cost
* ✅ Scalability
* ✅ Global availability
* ✅ Faster deployment
* ✅ Managed services
* ✅ Pay-as-you-go pricing

---

# 2. 🏗️ IaaS, PaaS and SaaS

The easiest way to remember:

> **IaaS = I manage more**
>
> **PaaS = Provider manages more**
>
> **SaaS = I simply use the software**

---

# 3. 🖥️ IaaS — Infrastructure as a Service

In **IaaS**, the cloud provider gives you infrastructure such as:

* Virtual machines
* CPU
* RAM
* Storage
* Networking

You manage things such as:

* Operating system
* Applications
* Software
* Security configuration
* Updates
* Server configuration

## AWS EC2 = IaaS Example

Suppose you launch an **EC2** instance.

You can choose:

```text
Instance Type → CPU / RAM
OS             → Ubuntu / Amazon Linux / Windows
Storage        → EBS
Network        → VPC
Security       → Security Group
```

You can customize the server according to your requirements.

### Example

You need a Linux server for your application.

You launch:

```text
EC2
 ├── Ubuntu
 ├── 4 CPU
 ├── 16 GB RAM
 └── 100 GB Storage
```

Then you install:

```text
Python
Nginx
Docker
Your Application
```

So:

> **EC2 gives you the infrastructure, but you manage the server.**

---

# 4. ⚙️ PaaS — Platform as a Service

**PaaS** provides a platform for running applications without requiring you to manage the underlying infrastructure.

You generally focus on:

```text
Application
    ↓
Platform
    ↓
Cloud Provider manages infrastructure
```

For example, managed application platforms can handle:

* Servers
* Operating systems
* Infrastructure
* Scaling
* Runtime environments
* Deployment infrastructure

---

## AWS Lambda and Serverless

**AWS Lambda** is technically **FaaS (Function as a Service)** and is part of the broader serverless computing model rather than traditional PaaS.

With Lambda, you mainly provide your function/code and configuration.

```text
Your Code
   ↓
AWS Lambda
   ↓
AWS manages:
   ├── Server
   ├── OS
   ├── Hardware
   ├── Infrastructure
   └── Scaling
```

### Example

Suppose a user uploads an image.

```text
User uploads image
        ↓
       S3
        ↓
     Lambda
        ↓
   Resize image
        ↓
    Save result
```

You don't need to manually launch an EC2 server for this function.

### Important

> **Lambda = Serverless / FaaS**
>
> It should not be described as traditional PaaS, although it demonstrates the broader idea of the cloud provider managing infrastructure.

---

# 5. 📦 SaaS — Software as a Service

In **SaaS**, you simply use the application.

You don't manage:

* Server
* Operating system
* Hardware
* Application installation
* Most infrastructure maintenance

## Examples

* Gmail
* Jira
* GitHub
* Salesforce
* Zoom
* AWS QuickSight

### Example: Gmail

When you use Gmail:

```text
You
 ↓
Open Gmail
 ↓
Login
 ↓
Send Email
```

You don't need to worry about:

```text
Which server?
Which CPU?
Which OS?
Which database?
How many servers?
How is Gmail deployed?
```

The provider manages those details.

---

# 6. ⚖️ IaaS vs PaaS vs SaaS

| Feature             | IaaS                | PaaS / Managed Platform       | SaaS             |
| ------------------- | ------------------- | ----------------------------- | ---------------- |
| Example             | EC2                 | Managed application platforms | Gmail, Jira      |
| Hardware            | Provider manages    | Provider manages              | Provider manages |
| OS                  | **You manage**      | Provider manages              | Provider manages |
| Application         | **You manage**      | You provide code/application  | Provider manages |
| Customization       | High                | Medium                        | Low              |
| Management Required | High                | Medium/Low                    | Very Low         |
| Main Idea           | Rent infrastructure | Run your application/code     | Use software     |

## 🧠 Easy Memory Trick

```text
IaaS
 ↓
"I manage the server"

PaaS
 ↓
"I provide my application/code"

SaaS
 ↓
"I simply use the software"
```

---

# 7. 🚀 Why Choose AWS?

There are many cloud providers:

* AWS
* Microsoft Azure
* Google Cloud
* Oracle Cloud
* IBM Cloud

So why choose AWS?

---

## 7.1 Large Number of Services

AWS provides services for:

```text
Compute
Storage
Database
Networking
Security
Monitoring
AI/ML
Containers
Serverless
DevOps
```

---

## 7.2 Global Infrastructure

AWS provides infrastructure organized into:

* Regions
* Availability Zones
* Edge locations

You can deploy applications closer to your users and design systems for high availability.

---

## 7.3 Scalability

Suppose your application normally has:

```text
1,000 users/day
```

Suddenly it receives:

```text
100,000 users/day
```

AWS provides services that can help you scale your infrastructure and applications.

Examples include:

* Auto Scaling
* Elastic Load Balancing
* Serverless services
* Managed databases

---

## 7.4 Pay-as-You-Go

You generally pay based on the resources and services you consume instead of purchasing all infrastructure upfront.

---

## 7.5 Security

AWS provides services and features such as:

* IAM
* KMS
* WAF
* Security Groups
* CloudTrail
* GuardDuty

---

## 7.6 High Availability

You can design applications across multiple Availability Zones.

```text
                 User
                   ↓
             Load Balancer
              ↙          ↘
           AZ-1          AZ-2
             ↓             ↓
           Server        Server
```

If one server or Availability Zone has a problem, another can continue serving traffic depending on the architecture.

---

# 8. 🧩 AWS Core Services

You can remember the major categories like this:

```text
01 → Compute
02 → Storage
03 → Database
04 → Networking
05 → Security
06 → Monitoring & Cost
```

---

# 9. 💻 Compute — EC2 and Lambda

## 9.1 EC2

**EC2 = Elastic Compute Cloud**

EC2 provides virtual servers in the AWS cloud.

Example:

```text
User
 ↓
EC2
 ↓
Ubuntu
 ↓
Nginx
 ↓
Application
```

You can configure:

* CPU
* RAM
* Operating System
* Storage
* Network
* Security settings

### When to Use EC2?

Use EC2 when you need:

* Full server control
* Custom software
* Custom OS configuration
* Long-running applications
* Special server configurations

### Simple Definition

> **EC2 = Virtual server that you manage.**

---

## 9.2 Lambda

AWS Lambda runs code without requiring you to manage the underlying server.

Example:

```text
S3
 ↓
Image uploaded
 ↓
Lambda
 ↓
Resize image
```

You don't need to manually launch an EC2 server for this function.

### When to Use Lambda?

Lambda is useful for:

* Event-driven applications
* APIs
* Automation
* File processing
* Scheduled jobs
* Short-running functions

### Simple Definition

> **Lambda = Run code without managing servers.**

---

# 10. ⚔️ EC2 vs Lambda

This is a very important AWS concept.

| Feature                   | EC2                  | Lambda                             |
| ------------------------- | -------------------- | ---------------------------------- |
| Type                      | Virtual server       | Function execution                 |
| OS Management             | You manage           | AWS manages                        |
| Server Size               | You choose           | AWS manages underlying compute     |
| Execution                 | Can run continuously | Runs when invoked                  |
| Customization             | High                 | Lower infrastructure customization |
| Infrastructure Management | More                 | Less                               |
| Best For                  | Servers/applications | Event-driven functions             |

### Simple Example

**EC2:**

> "Give me a server. I will configure it."

**Lambda:**

> "Here is my function. Run it whenever needed."

---

# 11. 🗄️ Storage — S3 and EBS

## 11.1 S3

**S3 = Simple Storage Service**

S3 is primarily **object storage**.

You can store:

* Images
* Videos
* Documents
* Backups
* Logs
* Application files

Example:

```text
User
 ↓
Upload photo
 ↓
S3 Bucket
 ↓
photo.jpg
```

### Think

> **S3 = Object storage for files and data.**

---

# 12. 💾 EBS — Elastic Block Store

**EBS = Elastic Block Store**

EBS provides **block storage** that can be attached to EC2.

Example:

```text
EC2
 |
 └── EBS
      |
      └── Application files
```

Think of EBS like a:

> **Virtual hard disk for an EC2 server.**

---

## S3 vs EBS

| Feature      | S3                    | EBS                           |
| ------------ | --------------------- | ----------------------------- |
| Storage Type | Object storage        | Block storage                 |
| Data         | Files / objects       | Disk blocks                   |
| Access       | S3 APIs               | Attached to EC2               |
| Common Use   | Images, backups, logs | OS/application/server storage |
| Main Concept | Bucket                | Volume                        |

### Easy Example

If you want to store:

```text
photo.jpg
video.mp4
backup.zip
```

→ **S3**

If your Ubuntu EC2 needs a disk for:

```text
/root
/application
/database files
```

→ **EBS**

---

# 13. 🗃️ Database — RDS and DynamoDB

## 13.1 RDS

**RDS = Relational Database Service**

Amazon RDS supports relational database engines such as:

* MySQL
* PostgreSQL
* MariaDB
* Microsoft SQL Server
* Oracle

Example:

```text
Application
     ↓
    RDS
     ↓
   MySQL
```

Use RDS when your application needs a traditional relational SQL database.

### Simple Definition

> **RDS = Managed relational database service.**

---

# 14. ⚡ DynamoDB

**DynamoDB** is a fully managed **NoSQL database**.

It supports key-value and document data models and is designed for applications requiring highly scalable, low-latency database access.

Example:

```text
Mobile App
    ↓
   API
    ↓
DynamoDB
```

---

## RDS vs DynamoDB

| Feature       | RDS                               | DynamoDB                        |
| ------------- | --------------------------------- | ------------------------------- |
| Database Type | Relational                        | NoSQL                           |
| Data Model    | Tables / rows / columns           | Key-value / document            |
| Query Model   | SQL                               | DynamoDB APIs / expressions     |
| Schema        | Structured                        | Flexible                        |
| Relationships | Supports relational relationships | Designed around access patterns |
| Typical Use   | Traditional SQL applications      | Highly scalable NoSQL workloads |

### 🧠 Easy Memory

> **RDS = SQL**
>
> **DynamoDB = NoSQL**

---

# 15. 🌐 Networking — VPC and Route 53

## 15.1 VPC

**VPC = Virtual Private Cloud**

A VPC is your logically isolated network environment inside AWS.

You can design a network such as:

```text
VPC
 |
 +-- Public Subnet
 |      |
 |   Load Balancer
 |
 +-- Private Subnet
 |      |
 |     EC2
 |
 +-- Database Subnet
        |
       RDS
```

You can configure things such as:

* IP address ranges
* Subnets
* Route tables
* Security Groups
* Network ACLs
* Internet connectivity

### Think

> **VPC = Your private network environment in AWS.**

---

# 16. 🌍 Route 53

**Amazon Route 53** is an AWS DNS service.

Suppose you type:

```text
www.example.com
```

DNS helps translate the domain name into the appropriate destination.

Simple flow:

```text
User
 ↓
www.example.com
 ↓
Route 53
 ↓
Application / Load Balancer
```

### Think

> **Route 53 = DNS**

---

# 17. 🔐 Security — IAM and KMS

## 17.1 IAM

**IAM = Identity and Access Management**

IAM controls:

> **Who can access what?**

Example:

```text
Developer
   ↓
  IAM
   ↓
Can access S3
Cannot delete production database
```

IAM includes:

* Users
* Groups
* Roles
* Policies
* Permissions

### Think

> **IAM = Identity and permissions**

IAM is primarily about **authentication and authorization**, with authorization being the core focus of permissions.

---

# 18. 🔑 KMS

**KMS = Key Management Service**

AWS KMS helps create and manage cryptographic keys used to protect data.

Example:

```text
Sensitive Data
      ↓
   Encryption
      ↓
   KMS Key
```

### Think

> **IAM controls access.**
>
> **KMS helps manage encryption keys.**

---

# 19. 📊 Monitoring — CloudWatch

**Amazon CloudWatch** is used for AWS monitoring and observability.

You can monitor:

* CPU
* Network
* Logs
* Metrics
* Alarms
* Application behavior

### Important

EC2 memory usage is **not normally available as a default basic metric**. You generally need the CloudWatch agent or custom metrics to monitor memory.

---

## Example

Suppose your EC2 CPU reaches 90%.

```text
EC2
 ↓
CloudWatch
 ↓
CPU = 90%
 ↓
Alarm
 ↓
Notification / Automation
```

CloudWatch can help you identify performance problems and trigger automated actions.

---

# 20. 💰 AWS Billing and Cost Management

AWS provides tools to understand, monitor, and optimize cloud spending.

Example:

```text
EC2       → ₹5,000
RDS       → ₹3,000
S3        → ₹500
NAT       → ₹1,000
Other     → ₹500
----------------
Total     → ₹10,000
```

As a cloud architect, you should continuously ask:

> **Why are we spending this money? Can we optimize it?**

---

## Cost Optimization Techniques

Examples:

* Right-size EC2 instances
* Remove unused resources
* Optimize storage
* Review NAT Gateway usage
* Use Savings Plans where appropriate
* Monitor data-transfer costs
* Review idle resources
* Choose appropriate database/storage classes

---

# 21. 🧠 Six AWS Categories — Easy Revision

```text
┌──────────────────────────────────┐
│        AWS CORE SERVICES         │
├──────────────────────────────────┤
│                                  │
│ 1. Compute                       │
│    EC2, Lambda                   │
│                                  │
│ 2. Storage                       │
│    S3, EBS                       │
│                                  │
│ 3. Database                      │
│    RDS, DynamoDB                 │
│                                  │
│ 4. Networking                    │
│    VPC, Route 53                 │
│                                  │
│ 5. Security                      │
│    IAM, KMS                      │
│                                  │
│ 6. Monitoring & Cost             │
│    CloudWatch, Billing           │
│                                  │
└──────────────────────────────────┘
```

---

# 22. 🛒 Real-World AWS Architecture Example

Imagine you're building an **e-commerce website**.

A simplified architecture could look like this:

```text
                         USERS
                           │
                           ▼
                       Route 53
                           │
                           ▼
                    Load Balancer
                           │
                    ┌──────┴──────┐
                    ▼             ▼
                  EC2           EC2
                    │             │
                    └──────┬──────┘
                           │
                    ┌──────┴──────┐
                    ▼             ▼
                   S3            RDS
              Images/Files    SQL Database
```

Additional AWS services can provide supporting capabilities:

```text
VPC
 │
 └── Network isolation and routing

IAM
 │
 └── Permissions and access control

KMS
 │
 └── Encryption key management

CloudWatch
 │
 └── Monitoring and alarms

Billing
 │
 └── Cost monitoring and optimization
```

---

# 23. 🗺️ Complete AWS Foundation Map

```text
                         AWS
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
        ▼                 ▼                 ▼
     Compute           Storage           Database
        │                 │                 │
   ┌────┴────┐       ┌────┴────┐       ┌────┴────┐
   │         │       │         │       │         │
  EC2     Lambda     S3       EBS     RDS   DynamoDB
   │
   │
   ▼
Application
```

```text
                         AWS
                          │
          ┌───────────────┼────────────────┐
          │               │                │
          ▼               ▼                ▼
      Networking       Security        Monitoring
          │               │                │
      ┌───┴───┐       ┌───┴───┐            │
      │       │       │       │            │
     VPC   Route53   IAM     KMS       CloudWatch
```

---

# 24. ⚡ Quick Revision

## Cloud Computing

> Use computing resources over the internet instead of owning and maintaining physical infrastructure.

## IaaS

> **Rent infrastructure and manage more of the server.**

Example:

```text
EC2
```

## PaaS

> **Use a managed application platform while the provider manages more infrastructure.**

## FaaS / Serverless

> **Provide functions/code and let the provider manage the underlying infrastructure.**

Example:

```text
Lambda
```

## SaaS

> **Use a complete software application.**

Examples:

```text
Gmail
Jira
Salesforce
```

---

# 25. 🧩 AWS Service Memory Map

```text
Compute
├── EC2
└── Lambda

Storage
├── S3
└── EBS

Database
├── RDS
└── DynamoDB

Networking
├── VPC
└── Route 53

Security
├── IAM
└── KMS

Monitoring & Cost
├── CloudWatch
└── Billing / Cost Management
```

---

# 26. 🧠 One-Line Memory Trick

Remember the AWS foundation as:

> **Compute → Store → Database → Connect → Secure → Monitor**

And remember the individual services:

```text
EC2 / Lambda
      ↓
S3 / EBS
      ↓
RDS / DynamoDB
      ↓
VPC / Route 53
      ↓
IAM / KMS
      ↓
CloudWatch / Billing
```

---

# 27. 🚀 What to Learn Next

Once these AWS fundamentals are clear, the next topics should be:

```text
AWS Fundamentals
       ↓
IAM
       ↓
VPC
       ↓
EC2
       ↓
Security Groups
       ↓
Load Balancer
       ↓
Auto Scaling
       ↓
RDS
       ↓
S3
       ↓
CloudWatch
       ↓
High Availability
       ↓
Fault Tolerance
       ↓
Docker
       ↓
Kubernetes
       ↓
CI/CD
       ↓
Production Architecture
```

---

# 🎯 Final Summary

The most important concepts to remember are:

| Category      | AWS Services              | Main Purpose                      |
| ------------- | ------------------------- | --------------------------------- |
| ☁️ Cloud      | AWS                       | Cloud infrastructure and services |
| 💻 Compute    | EC2, Lambda               | Run applications and code         |
| 🗄️ Storage   | S3, EBS                   | Store files and block data        |
| 🗃️ Database  | RDS, DynamoDB             | Store application data            |
| 🌐 Networking | VPC, Route 53             | Network and DNS                   |
| 🔐 Security   | IAM, KMS                  | Access control and encryption     |
| 📊 Monitoring | CloudWatch                | Metrics, logs, alarms             |
| 💰 Cost       | Billing / Cost Management | Monitor and optimize spending     |

### The Core Idea

```text
                AWS
                 │
     ┌───────────┼───────────┐
     │           │           │
   Compute     Storage     Database
     │           │           │
  EC2/Lambda   S3/EBS    RDS/DynamoDB
     │           │           │
     └───────────┼───────────┘
                 │
          Networking
          VPC / Route 53
                 │
             Security
             IAM / KMS
                 │
           Monitoring
           CloudWatch
                 │
              Cost
             Billing
```

> **This is the basic AWS foundation you should understand before moving into architecture topics such as VPC design, High Availability, Auto Scaling, Kubernetes, CI/CD, and production architecture.**
