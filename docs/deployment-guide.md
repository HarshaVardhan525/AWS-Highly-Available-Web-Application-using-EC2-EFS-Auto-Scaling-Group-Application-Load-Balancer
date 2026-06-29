# Deployment Guide

## Prerequisites

Before starting, ensure you have:

- AWS Account
- EC2 Key Pair
- Basic Linux knowledge
- Amazon Linux 2 AMI
- SSH Client
- Web Browser

---

# Step 1 - Create a VPC

- Create a custom VPC.
- CIDR Block: `10.0.0.0/16`

---

# Step 2 - Create Subnets

Create four subnets across two Availability Zones.

### Public Subnets

- Public Subnet A
- Public Subnet B

### Private Subnets

- Private Subnet A
- Private Subnet B

---

# Step 3 - Configure Internet Gateway

- Create an Internet Gateway.
- Attach it to the VPC.

---

# Step 4 - Configure Route Tables

### Public Route Table

- Route: `0.0.0.0/0 → Internet Gateway`

Associate it with:

- Public Subnet A
- Public Subnet B

### Private Route Table

Associate it with:

- Private Subnet A
- Private Subnet B

---

# Step 5 - Launch EC2 Instance

Launch an EC2 instance in the public subnet.

Install Apache:

```bash
sudo yum update -y
sudo yum install httpd -y
sudo systemctl enable httpd
sudo systemctl start httpd
```

---

# Step 6 - Configure Security Group

Allow:

- SSH (22)
- HTTP (80)
- NFS (2049)

---

# Step 7 - Create Amazon EFS

Create an EFS file system with mount targets in both Availability Zones.

---

# Step 8 - Mount Amazon EFS

Install EFS utilities:

```bash
sudo yum install amazon-efs-utils -y
```

Mount the EFS file system.

Verify using:

```bash
df -h
```

---

# Step 9 - Deploy Website

Mount EFS to:

```text
/var/www/html
```

Create an `index.html` file inside the mounted directory.

---

# Step 10 - Create an AMI

Create an Amazon Machine Image from the configured EC2 instance.

---

# Step 11 - Create a Launch Template

Include:

- AMI
- Instance Type
- Security Group
- Key Pair

---

# Step 12 - Create Target Group

Protocol: HTTP

Port: 80

Health Check Path:

```
/
```

---

# Step 13 - Create Application Load Balancer

Configure:

- Internet Facing
- HTTP Listener
- Public Subnets
- Target Group

---

# Step 14 - Create Auto Scaling Group

Use the Launch Template.

Deploy instances across:

- Private Subnet A
- Private Subnet B

Attach the Target Group.

---

# Step 15 - Mount EFS on Auto Scaling Instances

Mount the same Amazon EFS file system on all EC2 instances launched by the Auto Scaling Group.

---

# Step 16 - Verify Deployment

Access the website using the Application Load Balancer DNS.

Verify:

- Healthy targets
- Shared website content
- Successful load balancing