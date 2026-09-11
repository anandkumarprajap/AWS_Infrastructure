# AWS IAM – Users, Groups & Permissions

## 📌 Overview

In this demo, I created IAM **Groups, Users, and Permissions** for three different teams:

* **Finance Team**
* **Monitoring Team**
* **DevOps Team**

The main goal is to manage AWS permissions using **IAM Groups** instead of attaching policies individually to every user.

---

## 🏗️ IAM Architecture

```text
                    AWS IAM
                       |
        +--------------+--------------+
        |              |              |
   Finance Group   Monitoring Group  DevOps Group
        |              |              |
   Finance User    Monitoring User   DevOps User
        |              |              |
 Billing Read-Only CloudWatch Access  DevOps Permissions
```

---

## 1. Create IAM Groups

First, I created three IAM groups:

```text
Finance
Monitoring
DevOps
```

Each group is responsible for managing permissions for a specific team.

---

## 2. Finance Team

### Group

```text
Finance
```

### Permission

The Finance group receives **AWS Billing read-only access**.

This allows Finance users to:

* View AWS billing information
* View cost and usage information
* Monitor AWS expenses

They should not be able to modify AWS resources or billing settings unless additional permissions are specifically required.

### User

```text
Finance User
      ↓
Finance Group
      ↓
Billing Read-Only Permission
```

---

## 3. Monitoring Team

### Group

```text
Monitoring
```

### Permission

The Monitoring group receives **CloudWatch access**.

This allows the monitoring team to work with AWS monitoring resources such as:

* CloudWatch Metrics
* CloudWatch Dashboards
* CloudWatch Alarms
* Monitoring data
* Logs, where permitted by the assigned policy

### User

```text
Monitoring User
       ↓
Monitoring Group
       ↓
CloudWatch Permission
```

> **Note:** `CloudWatchFullAccess` is a broad permission. In a production environment, I would prefer a least-privilege policy containing only the permissions the monitoring team actually needs.

---

## 4. DevOps Team

### Group

```text
DevOps
```

### Permission

The DevOps group receives the permissions required to manage AWS infrastructure and DevOps-related resources.

For a demonstration, broad permissions may be used, but in a production environment I would follow the **Principle of Least Privilege**.

The DevOps team should receive only the permissions required for their responsibilities.

### User

```text
DevOps User
      ↓
DevOps Group
      ↓
Required DevOps Permissions
```

---

# 5. Create IAM Users

After creating the groups, I created three IAM users:

```text
1. Finance User
2. Monitoring User
3. DevOps User
```

Each user was added to the appropriate IAM group.

| User            | Group      | Permission                  |
| --------------- | ---------- | --------------------------- |
| Finance User    | Finance    | Billing Read-Only           |
| Monitoring User | Monitoring | CloudWatch Access           |
| DevOps User     | DevOps     | Required DevOps Permissions |

---

# 6. Configure Console Access

For users who require AWS Management Console access, I configured console sign-in credentials.

The users can then sign in to the AWS Console and access only the resources allowed by their assigned permissions.

For better security in a real environment:

* Enable MFA
* Follow least privilege
* Avoid sharing passwords
* Use strong authentication
* Review permissions regularly
* Remove unused users and permissions

---

# 7. Permission Management Approach

Instead of attaching policies directly to every user, I manage permissions through groups.

### Recommended Structure

```text
IAM Policy
     ↓
IAM Group
     ↓
IAM User
```

For example:

```text
Billing Read-Only Policy
          ↓
    Finance Group
          ↓
     Finance User
```

This makes permission management easier when the organization grows.

If another Finance employee joins the company, I can simply create the user and add them to the **Finance group**.

They automatically inherit the group's permissions.

---

# 8. Existing IAM User

I already have an existing IAM user in the AWS account.

I will not delete or recreate this user because policies and permissions have already been attached directly to it for demonstration purposes.

Instead, I created new users for this IAM group-based demonstration.

This also helps demonstrate the difference between:

### Direct User Permissions

```text
User
 ↓
Policy
```

and:

### Group-Based Permissions

```text
Policy
 ↓
Group
 ↓
User
```

Group-based permission management is generally easier to maintain and scale.

---

# 9. Principle of Least Privilege

The **Principle of Least Privilege** means giving users only the permissions they actually need to perform their job.

For example:

```text
Finance Team
    ↓
Billing Read-Only

Monitoring Team
    ↓
CloudWatch Permissions

DevOps Team
    ↓
Required Infrastructure Permissions
```

Avoid giving unrestricted administrator access unless it is genuinely required.

---

# 10. Final IAM Structure

```text
                         AWS IAM
                            |
             +--------------+--------------+
             |              |              |
        Finance Group  Monitoring Group  DevOps Group
             |              |              |
      Finance User    Monitoring User    DevOps User
             |              |              |
      Billing Access   CloudWatch Access  DevOps Access
```

---

## 🎯 Key Takeaways

* Created IAM groups for different teams.
* Created separate IAM users for each team.
* Added users to their respective groups.
* Managed permissions through IAM groups.
* Used billing read-only access for Finance.
* Used CloudWatch access for Monitoring.
* Assigned required AWS permissions to DevOps.
* Avoided recreating the existing IAM user.
* Followed the concept of **group-based permission management**.
* Applied the **Principle of Least Privilege** for production environments.

---

## 🔑 Important IAM Concept

> **Users should generally receive permissions through groups whenever possible, rather than managing individual user policies separately.**

This approach makes AWS IAM **more organized, scalable, and easier to manage**.
