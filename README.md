# Scalable & Secure E-Commerce Web Application on AWS

> A production-grade, 3-tier cloud infrastructure deployed end-to-end on AWS. Built with a security-first approach — no hardcoded credentials, least-privilege IAM, private subnets for the database layer, and full observability via CloudWatch and CloudTrail.

<br>

## AWS Services Used

| Layer | Service | Role |
|---|---|---|
| Networking | VPC, Subnets, Route Tables, IGW, Security Groups | Isolated, segmented network |
| Compute | EC2, Auto Scaling Group, Launch Template | Scalable app servers |
| Load Balancing | Application Load Balancer (ALB) | Traffic distribution + health checks |
| Database | RDS MySQL | Managed relational DB in private subnet |
| Storage | S3 | Static assets and backups |
| Monitoring | CloudWatch, CloudTrail | Metrics, dashboards, API audit trail |
| Alerting | SNS, SQS | Threshold-based email/SMS alerts |
| Security | IAM Roles & Policies, Secrets Manager | Zero hardcoded secrets, least-privilege access |

<br>

## What Was Built

- Custom **VPC** with public and private subnets across two availability zones
- **EC2** instances in an **Auto Scaling Group** with a Launch Template — scales up when CPU > 70%
- **ALB** routing traffic to healthy EC2 targets via target group health checks
- **RDS MySQL** deployed in a private subnet — never exposed to the internet
- All DB credentials stored in **Secrets Manager** — EC2 fetches them at runtime via IAM role
- **CloudWatch** dashboards monitoring CPU, network in/out, and ALB request count
- **CloudTrail** enabled for full API audit logging across the account
- **SNS topic + SQS queue** wired to CloudWatch alarms — email alert fires when CPU crosses threshold
- **IAM roles** scoped to minimum required permissions (no `AdministratorAccess` anywhere)

<br>

## Screenshots

| VPC & Subnets | ALB & Target Group |
|---|---|
| ![VPC](docs/screenshots/vpc.png) | ![ALB](docs/screenshots/alb.png) |

| Auto Scaling Group | RDS Instance |
|---|---|
| ![ASG](docs/screenshots/asg.png) | ![RDS](docs/screenshots/rds.png) |

| CloudWatch Dashboard | SNS Alert Received |
|---|---|
| ![CloudWatch](docs/screenshots/cloudwatch.png) | ![SNS](docs/screenshots/sns.png) |

| Secrets Manager |
|---|
| ![Secrets](docs/screenshots/secrets-manager.png) |

<br>

## Key Learnings

- Designed VPC networking from scratch — understood CIDR blocks, subnet tiers, and route table logic
- Configured ALB listener rules and target group health checks for zero-downtime routing
- Set Auto Scaling policies tied to CloudWatch CPU alarms — understood scale-out vs scale-in cooldown
- Kept the database in a **private subnet** — EC2 connects via internal DNS, never a public IP
- Used **Secrets Manager** to eliminate hardcoded passwords — EC2 fetches the secret via IAM role at runtime
- Understood the difference between **CloudWatch** (metrics/logs) and **CloudTrail** (API activity audit)
- Applied **least-privilege IAM** — each resource had only the permissions it actually needed

<br>

## Cost Note

> All resources were **fully deployed, tested end-to-end, and then deleted** to avoid charges.
> Estimated cost for the entire session: **under ₹100 (< $1.50 USD)** using Free Tier-eligible tiers where available.

<br>

## What I'd Do Differently

- Enable **RDS Multi-AZ** for automatic failover
- Add a **WAF** in front of the ALB to block common web attacks
- Use **S3 + CloudFront** for static asset delivery with edge caching
- Set up **VPC Flow Logs** for network-level traffic visibility

<br>

## Note

> This project was intentionally built through the **AWS Management Console** to deeply understand each service before abstracting it.
