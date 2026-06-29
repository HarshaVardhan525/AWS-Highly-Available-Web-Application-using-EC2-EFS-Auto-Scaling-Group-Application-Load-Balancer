# AWS Architecture Explanation

## Overview

This project implements a highly available web application architecture on AWS using services such as Amazon VPC, EC2, Amazon EFS, Auto Scaling Group, and Application Load Balancer.

The architecture is designed to improve availability, scalability, and fault tolerance by distributing resources across multiple Availability Zones.

---

# Architecture Components

## 1. Amazon VPC

A custom Virtual Private Cloud (VPC) provides an isolated networking environment for all AWS resources.

CIDR Block:

```
10.0.0.0/16
```

---

## 2. Public Subnets

Two public subnets are created across two Availability Zones.

These subnets host:

- Application Load Balancer
- Initial EC2 Instance

Public subnets have a route to the Internet Gateway.

---

## 3. Private Subnets

Two private subnets are deployed across different Availability Zones.

These subnets host the EC2 instances launched by the Auto Scaling Group.

The instances are not directly accessible from the internet.

---

## 4. Internet Gateway

The Internet Gateway enables internet access for resources in the public subnets.

---

## 5. Security Group

The security group allows:

| Port | Purpose |
|------|----------|
|22|SSH|
|80|HTTP|
|2049|NFS|

The NFS port enables communication between EC2 instances and Amazon EFS.

> In production, NFS access should be restricted to the EC2 security group rather than open access.

---

## 6. Amazon EC2

An EC2 instance was initially launched to:

- Install Apache HTTP Server
- Mount Amazon EFS
- Deploy the website
- Create an Amazon Machine Image (AMI)

---

## 7. Amazon EFS

Amazon Elastic File System provides centralized shared storage.

The EFS is mounted at:

```
/var/www/html
```

This ensures that every EC2 instance serves the same website content.

---

## 8. Amazon Machine Image (AMI)

Once the EC2 instance was configured, an Amazon Machine Image (AMI) was created.

The AMI contains:

- Apache configuration
- Website files
- Mounted EFS configuration

---

## 9. Launch Template

The Launch Template defines:

- AMI
- Instance Type
- Security Group
- Key Pair

The Auto Scaling Group uses this template to launch new instances.

---

## 10. Auto Scaling Group

The Auto Scaling Group automatically launches and replaces EC2 instances.

Benefits include:

- High Availability
- Automatic Recovery
- Scalability

---

## 11. Application Load Balancer

The Application Load Balancer distributes incoming HTTP traffic across healthy EC2 instances.

Health checks ensure traffic is only sent to healthy targets.

---

# High Availability

The architecture spans two Availability Zones.

If one Availability Zone becomes unavailable, the Load Balancer continues routing traffic to healthy instances in the remaining Availability Zone.

---

# Shared Storage

Amazon EFS acts as centralized storage.

Advantages:

- Single copy of website files
- Simplified content management
- Consistent application across all EC2 instances

---

# Traffic Flow

```
Internet
      │
      ▼
Application Load Balancer
      │
      ▼
Auto Scaling Group
      │
 ┌──────────────┐
 │              │
 ▼              ▼
EC2 Instance  EC2 Instance
      │              │
      └──────┬───────┘
             │
             ▼
        Amazon EFS
```

---

# Benefits of This Architecture

- High Availability
- Multi-AZ Deployment
- Shared Storage
- Fault Tolerance
- Automatic Scaling
- Improved Reliability
- Centralized Website Management