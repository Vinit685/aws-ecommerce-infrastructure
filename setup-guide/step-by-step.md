# Step-by-Step Setup Guide

This documents the exact sequence used to build this infrastructure.

---

## Phase 1 — Networking

1. Created a custom **VPC** with CIDR `10.0.0.0/16`
2. Created 2 **public subnets** (for EC2/ALB) and 2 **private subnets** (for RDS) across 2 AZs
3. Attached an **Internet Gateway** to the VPC
4. Configured **Route Tables** — public subnets route `0.0.0.0/0` to IGW; private subnets have no internet route
5. Created **Security Groups**:
   - ALB SG: inbound 80/443 from internet
   - EC2 SG: inbound 80 from ALB SG only
   - RDS SG: inbound 3306 from EC2 SG only

---

## Phase 2 — Compute & Load Balancing

6. Created a **Launch Template** with Amazon Linux 2, user data script to install app
7. Created an **Auto Scaling Group** using the Launch Template across both public subnets
8. Set scaling policy: **add 1 instance when CPU > 70% for 2 minutes**
9. Created an **ALB** in both public subnets
10. Created a **Target Group** (HTTP:80) and registered the ASG
11. Added an ALB **Listener** on port 80 forwarding to the target group

---

## Phase 3 — Database

12. Created an **RDS MySQL** instance in the private subnet group
13. Used a **DB Subnet Group** spanning both private subnets
14. Stored DB password in **Secrets Manager** — named `prod/ecommerce/db`
15. Attached an **IAM role** to EC2 with `secretsmanager:GetSecretValue` permission

---

## Phase 4 — Monitoring & Alerting

16. Created a **CloudWatch Dashboard** with widgets: EC2 CPU, NetworkIn/Out, ALB RequestCount
17. Created a **CloudWatch Alarm** — triggers when EC2 CPU > 70% for 5 minutes
18. Created an **SNS Topic** and subscribed my email — confirmed the subscription
19. Created an **SQS Queue** subscribed to the same SNS topic
20. Wired the CloudWatch Alarm to the SNS topic

---

## Phase 5 — Security & Audit

21. Enabled **CloudTrail** for the region — all API calls logged to S3
22. Verified **IAM roles** — EC2 role has only: `ssm:*`, `secretsmanager:GetSecretValue`, `s3:GetObject`
23. Confirmed **no IAM user access keys** were used — all access via roles

---

## Teardown

All resources deleted in reverse order: ASG → ALB → EC2 → RDS → Secrets → VPC → CloudTrail
