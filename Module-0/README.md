# AWS Platform Engineering Architect – Easy Notes

## 1. My Role as a Platform Engineering Architect

When I was working with **Volkswagen** as a Platform Engineering Architect, I was responsible for designing and deploying different applications and DevOps tools.

One important requirement was to deploy DevOps-related applications in the **AWS China region**.

Applications included:

* GitLab
* Jira
* Confluence
* Artifactory
* Other DevOps/platform applications

I also coordinated with:

* AWS team
* GitLab team
* Artifactory team
* Internal development teams
* Service vendors

---

# 2. First Understand the Business Requirement

Before designing anything, I first understand:

* What does the business need?
* Who will use the application?
* Where are the users located?
* How many users will access it?
* What type of application is it?
* What availability is required?
* What security is required?
* What is the expected traffic?
* What is the budget?

**Architect thinking:**

> Don't start with AWS services. First understand the requirement, then design the solution.

---

# 3. Choose the AWS Region

The first question is:

> **Where are my users located?**

For example:

If most users are in **Shanghai**, I should consider the AWS China region that provides the best connectivity/latency for those users.

The basic idea is:

```text
User Location
      ↓
Choose suitable AWS Region
      ↓
Lower latency
      ↓
Better user experience
```

As an architect, I always think about both:

**Business requirement + End-user experience**

---

# 4. Design the Application Architecture

After selecting the region, I decide how the application should be designed.

Questions I ask:

* Is it a 2-tier or 3-tier application?
* Do we have a frontend?
* Do we have backend APIs?
* Do we need a database?
* How many servers are required?
* Do we need Kubernetes?
* Can we use EC2?
* Do we need a load balancer?
* Do we need a CDN?
* Do we need WAF?
* How will users access the application?

---

# 5. Basic 3-Tier Architecture

A common architecture can look like:

```text
Users
  ↓
DNS
  ↓
CloudFront / CDN
  ↓
WAF
  ↓
Load Balancer
  ↓
Application Servers
  ↓
Database
```

The application servers and database can be placed in **private subnets**.

---

# 6. VPC Design

After understanding the application, I design the VPC.

I need to think about:

* VPC CIDR
* Public subnets
* Private subnets
* Availability Zones
* Route tables
* Internet Gateway
* NAT Gateway
* Load Balancer
* Bastion/secure administrative access
* Application servers
* Database subnet

Example:

```text
                 VPC
                  |
       +----------+----------+
       |                     |
 Public Subnet          Private Subnet
       |                     |
 Load Balancer          App Servers
       |                     |
       +----------+----------+
                  |
             Database
          Private Subnet
```

---

# 7. Database Design

I need to decide:

* Which database do we need?
* Do we need RDS?
* How much storage is required?
* How many users will access the database?
* Do we need Multi-AZ?
* What is the availability requirement?
* What backup and recovery strategy is required?

For a production application, high availability may require a **Multi-AZ database architecture**.

---

# 8. Traffic and Scalability

I also think about application traffic.

For example:

* How many users access the application every day?
* What is the normal traffic?
* When will traffic increase?
* Is there a sudden traffic spike?
* Do we need auto scaling?
* How many servers do we initially require?

The architecture should be able to handle expected traffic without unnecessarily over-provisioning resources.

---

# 9. DNS and Request Flow

I need to understand how the request will travel.

For example:

```text
User
 ↓
DNS
 ↓
CloudFront
 ↓
WAF
 ↓
Load Balancer
 ↓
Application Server
 ↓
Database
```

When designing the architecture, I ask:

* Do I need DNS?
* Do I need CloudFront?
* Do I need WAF?
* Do I need a Load Balancer?
* Where should the frontend be hosted?
* Where should the backend/API be hosted?
* Where should the database be hosted?

---

# 10. Security

Security is one of the most important parts of architecture.

I need to think about:

* WAF
* Security Groups
* Private Subnets
* IAM
* Encryption
* Secrets
* Network security
* Restricted access
* Logging and auditing

For example, WAF can help protect a web application against common web attacks by applying configured rules.

Basic flow:

```text
Internet
   ↓
WAF
   ↓
Load Balancer
   ↓
Application
   ↓
Database
```

---

# 11. EC2 vs Kubernetes

I also need to decide where the application should run.

Possible options:

* EC2
* Kubernetes/EKS
* Other managed services

I don't select Kubernetes just because it is popular.

I consider:

* Does the application support Kubernetes?
* Does the vendor support Kubernetes?
* What does the vendor recommend?
* What is the operational complexity?
* What is the existing company standard?
* What are the scalability requirements?
* What will be the cost?

---

# 12. Real Example – GitLab in China

When we were deploying **GitLab in the China region**, we checked the available deployment options.

At that time, the GitLab solution we were using did not support the EKS approach we wanted.

So, instead of forcing Kubernetes, we selected **EC2-based deployment**.

We also coordinated with the GitLab team to understand:

* Required infrastructure
* Recommended architecture
* Server requirements
* Storage requirements
* Networking requirements
* Supported deployment model

This is an important architect lesson:

> **Choose technology based on requirements and vendor support, not simply because the technology is popular.**

---

# 13. Monitoring

After deployment, I need to monitor the application.

I think about:

* CPU
* Memory
* Disk
* Network
* Application logs
* Error rates
* Availability
* Performance

AWS provides **CloudWatch** for monitoring.

But if the company already has **Grafana Cloud**, I should consider using the existing monitoring platform instead of introducing another unnecessary tool.

For example:

```text
AWS Resources
     ↓
CloudWatch
     ↓
Grafana / Existing Monitoring Platform
     ↓
Dashboards + Alerts
```

This can save:

* Cost
* Time
* Operational effort

---

# 14. Existing Licenses and Tools

Before introducing a new tool, I check what the company already has.

For example:

> If the company already has a Grafana Cloud license, I should use it instead of purchasing another monitoring solution unnecessarily.

I always consider:

**Existing tools + Existing licenses + Existing standards**

This helps avoid unnecessary cost and complexity.

---

# 15. Cost Optimization

As an architect, I also need to think about cost.

I compare:

* Different AWS regions
* EC2 instance types
* RDS configuration
* Load Balancer
* NAT Gateway
* Storage
* Data transfer
* CloudFront
* Monitoring costs
* Licensing costs

For example:

```text
Requirement
    ↓
Architecture A → Cost
Architecture B → Cost
Architecture C → Cost
    ↓
Compare
    ↓
Choose best solution
```

The cheapest architecture is not always the best.

The goal is:

> **Best balance between cost, performance, availability, security, and business requirements.**

---

# 16. Vendor Coordination

As an architect, I don't work only with AWS.

I coordinate with:

```text
Business Team
      ↓
Development Team
      ↓
Architecture / Platform Team
      ↓
AWS Team
      ↓
Product Vendors
      ↓
Operations Team
```

For my Volkswagen example, I coordinated with:

* AWS team
* GitLab team
* Artifactory team
* Internal development teams
* Other service vendors

I understand their requirements and convert those requirements into an infrastructure/architecture design.

---

# 17. My Architect Thought Process

Whenever I receive a new application requirement, I think in this order:

```text
1. Business Requirement
        ↓
2. User Location
        ↓
3. Region Selection
        ↓
4. Application Architecture
        ↓
5. VPC Design
        ↓
6. Network Design
        ↓
7. Compute Selection
        ↓
8. Database Design
        ↓
9. Load Balancer / DNS / CDN
        ↓
10. Security
        ↓
11. Monitoring
        ↓
12. High Availability
        ↓
13. Scalability
        ↓
14. Cost Optimization
        ↓
15. Vendor Support
        ↓
16. Deployment
        ↓
17. Operations & Maintenance
```

## Golden Rule for an Architect

> **First understand the business and user requirements. Then design the architecture. Finally select the technology and AWS services.**

An architect should always think about:

**Performance + Availability + Security + Scalability + Cost + Operations + User Experience**
