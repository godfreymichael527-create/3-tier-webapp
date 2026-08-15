# Secure 3-Tier Web Application Architecture on AWS

## Project Overview

This project demonstrates the design and deployment of a secure 3-tier web application architecture using Amazon Web Services (AWS).

The objective of this project was to build a secure, scalable, and highly available cloud environment by separating the infrastructure into three layers:

1. Presentation Layer - Application Load Balancer (ALB)
2. Application Layer - Amazon EC2 Web Servers
3. Database Layer - Amazon RDS MySQL Database

The project focuses on AWS networking, security groups, private resource isolation, and least privilege access.

---

# Architecture Overview

The architecture follows this traffic flow:
                INTERNET
                   |wc -l README.md
                   |
      Application Load Balancer
          (Public Subnets)
                   |
                   |
      -------------------------
      |                       |
      |                       |
 EC2 Web Server 1       EC2 Web Server 2
 Private Subnet         Private Subnet
 us-east-1a             us-east-1b
      |                       |
      -------------------------
                   |
                   |
          Amazon RDS MySQL
          Private Subnets
          us-east-1a/b


---

# AWS Services Used

| AWS Service | Purpose |
|-------------|---------|
| Amazon VPC | Created an isolated cloud network |
| Subnets | Separated public and private resources |
| Internet Gateway | Provided internet access to public resources |
| NAT Gateway | Allowed private resources outbound internet access |
| Route Tables | Controlled network traffic flow |
| Application Load Balancer | Distributed incoming application traffic |
| EC2 | Hosted web application servers |
| Target Groups | Registered EC2 instances with the ALB |
| Amazon RDS MySQL | Hosted the database layer |
| Security Groups | Controlled inbound and outbound traffic |

---

# Network Architecture

## VPC Configuration

VPC Name: Project VPC


CIDR Block: 10.0.0.0/16


Availability Zones:  us-east-1a
us-east-1b


Purpose:

The public subnets host the Application Load Balancer.

Public Subnet 1
CIDR: 10.0.0.0/20
Availability Zone: us-east-1a
Public Subnet 2
CIDR: 10.0.16.0/20
Availability Zone: us-east-1b

---

## Private Application Subnets

Purpose:

The private application subnets host EC2 web servers.

Private Subnet 1
CIDR: 10.0.128.0/20
Availability Zone: us-east-1a
Private Subnet 2
CIDR: 10.0.144.0/20
Availability Zone: us-east-1b

---

## Private Database Subnets

Purpose:

The private database subnets host the Amazon RDS database.

Private Subnet 3
CIDR: 10.0.160.0/20
Availability Zone: us-east-1a
Private Subnet 4
CIDR: 10.0.176.0/20
Availability Zone: us-east-1b

---

# Security Architecture

Security was implemented using separate Security Groups for each layer.

---

# Application Load Balancer Security Group

Security Group Name:

alb-sg

Inbound Rule:

| Protocol | Port | Source |
|----------|------|--------|
| HTTP | 80 | 0.0.0.0/0 |

Purpose:

Allows users from the internet to access the application through the Load Balancer.

Traffic:

Internet → ALB

---

# EC2 Security Group

Security Group Name:

ec2-sg

Inbound Rules:

| Protocol | Port | Source |
|----------|------|--------|
| HTTP | 80 | alb-sg |
| SSH | 22 | Administrator IP |

Purpose:

Allows only the Application Load Balancer to communicate with the EC2 instances.

Traffic:

ALB → EC2

---

# RDS Security Group

Security Group Name:

rds-sg

Inbound Rule:

| Protocol | Port | Source |
|----------|------|--------|
| MySQL | 3306 | ec2-sg |

Purpose:

Allows only EC2 instances to communicate with the database.

Traffic:

EC2 → RDS

---

# Security Model

The final traffic flow is:

Internet
    |
    | HTTP :80
    ↓
Application Load Balancer
    |
    | HTTP :80
    ↓
EC2 Web Servers
    |
    | MySQL :3306
    ↓
Amazon RDS Database

Security restrictions:

- Internet cannot directly access EC2 instances
- Internet cannot access RDS
- ALB cannot access RDS
- Only EC2 instances can access the database

---

# Compute Layer Deployment

Two EC2 instances were deployed:

web-server-1
Private Subnet
us-east-1a
web-server-2
Private Subnet
us-east-1b

Configuration:

- Amazon Linux 2023 AMI
- Apache Web Server installed
- No public IP addresses assigned
- Protected by EC2 Security Group

---

# Load Balancer Configuration

Created:

- Application Load Balancer
- Target Group
- HTTP Listener

Configuration:

ALB Listener:
HTTP Port 80
Target Group:
web-target-group

The ALB distributes traffic between:

EC2 Instance 1
        |
        |
EC2 Instance 2

Both instances successfully passed health checks.

---

# Database Layer Deployment

Created:

- Amazon RDS MySQL Database
- Private DB Subnet Group
- Database Security Group

Configuration:

Database Engine:

MySQL

Database Location:

Private Subnets

Public Access:

Disabled

Database Access:

Only EC2 instances can connect

---

# High Availability Design

The architecture uses multiple Availability Zones.

Resources are distributed across:

us-east-1a
us-east-1b

Benefits:

- Improved fault tolerance
- Reduced single points of failure
- Better availability

---

# Testing and Validation

## Application Load Balancer Test

Result:

Successful

The ALB DNS endpoint successfully served the web application.

---

## Target Group Health Check

Result:

2 Healthy Targets

Both EC2 instances successfully passed ALB health checks.

---

## Security Validation

| Test | Result |
|------|--------|
| Internet → ALB | Allowed |
| Internet → EC2 | Blocked |
| Internet → RDS | Blocked |
| ALB → EC2 | Allowed |
| EC2 → RDS | Allowed |

---

# Security Concepts Demonstrated

## Network Segmentation

Resources were separated into:

- Public layer
- Application layer
- Database layer

---

## Least Privilege Access

Each resource only receives the permissions required:

- ALB accepts internet traffic
- EC2 accepts traffic only from ALB
- RDS accepts traffic only from EC2

---

## Defense in Depth

Multiple security controls were applied:

- VPC isolation
- Private subnets
- Security Groups
- Restricted database access
- No public database exposure

---

# Future Security Improvements

Future enhancements include:

- Enable AWS CloudTrail logging
- Enable VPC Flow Logs
- Configure Amazon GuardDuty
- Add AWS Security Hub
- Implement AWS WAF
- Add CloudWatch monitoring
- Enable AWS KMS encryption
- Implement IAM least privilege roles

---

# Conclusion

This project demonstrates the design and deployment of a secure 3-tier web application architecture on AWS.

The project provided practical experience with:

- AWS networking
- VPC design
- EC2 deployment
- Application Load Balancing
- Database deployment
- Security Group configuration
- Cloud security architecture

This architecture represents a strong foundation for Cloud Security Engineering practices.

