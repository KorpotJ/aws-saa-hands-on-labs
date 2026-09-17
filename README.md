# AWS SAA-C03 Hands-On Labs

Hands-on labs built while studying for the AWS Certified Solutions Architect –
Associate (SAA-C03) exam. Each folder is a self-contained lab: a scenario, the
steps taken in the real AWS console, and the actual results — including things
that didn't behave the way the course slides implied.

## Method

Each lab follows the same loop:

1. **Diagram** — a visual of the concept before touching the console
2. **Mini-lab** — a small, timed hands-on exercise (10–15 min) on real AWS
3. **Quiz** — a knowledge check after the lab

## Labs

| # | Section | Status | Notes |
|---|---|---|---|
| [01](./01-iam) | IAM — multi-group cumulative permissions | ✅ Done | Union of group policies, tagging trade-offs, Policy Simulator |
| 02 | ELB & Auto Scaling Group | 🔜 Next | |
| — | ECS & Fargate | 📋 Planned | |
| — | Serverless: Lambda / DynamoDB / API Gateway | 📋 Planned | |
| — | Databases in AWS | 📋 Planned | |
| — | IAM Advanced (ABAC) | 📋 Planned | |
| — | VPC | 📋 Planned | The exam's biggest topic |
| — | Security / KMS / WAF | 📋 Planned | |
| — | DR & Migration | 📋 Planned | |

Order is driven by weakest quiz scores first, not strictly the course order.

## A note on screenshots

All AWS account IDs are cropped or blurred before commit. If you spot one that
slipped through, please open an issue.

## Why this exists

Built alongside an AWS SAA-C03 course while working toward a Cloud /
Infrastructure Engineer role. The goal is proof of hands-on understanding,
not just a certificate.
