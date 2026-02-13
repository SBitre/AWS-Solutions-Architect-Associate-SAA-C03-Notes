# ☁️ AWS Solutions Architect Associate (SAA-C03) — Study Notes

**Comprehensive study notes for the AWS Certified Solutions Architect Associate exam, written from hands-on learning and real-world understanding.**

Currently pursuing MS in Informatics (Cloud Concentration) at Northeastern University, Boston. These notes document my journey to AWS SAA certification.

[![AWS](https://img.shields.io/badge/AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Status](https://img.shields.io/badge/Exam_Target-March_2026-blue?style=for-the-badge)](https://aws.amazon.com/certification/certified-solutions-architect-associate/)

> "Don't just pass the exam — understand the WHY behind every service and architecture decision."

---

## 📊 Progress Tracker

| Section | Topic | Status |
|---------|-------|--------|
| 01 | [Introduction & Exam Overview](./01-introduction/) | ✅ Complete |
| 02 | [Code & Slides Download](./02-Code/) | ✅ Complete |
| 03 | [Getting started with AWS](./03-AWS/) | ✅ Complete |
| 04 | [IAM & AWS CLI](./04-IAM/) | ✅ Complete |
| 05 | [EC2 Fundamentals](./05-EC2/) | ✅ Complete |
| 06 | [EC2 - Solutions Architect Associate Level](./06-EC2/) | ✅ Complete |
| 07 | [EC2 Instance Storage](./07-EC2/) | ✅ Complete |
| 08 | [S3 — Simple Storage Service](./08-s3/) | ⬜ Up Next |
| 09 | [S3 — Advanced & Security](./09-s3-advanced/) | ⬜ Not Started |
| 10 | [CloudFront & Global Accelerator](./10-cloudfront/) | ⬜ Not Started |
| 11 | [AWS Storage Extras](./11-storage-extras/) | ⬜ Not Started |
| 12 | [Decoupling — SQS, SNS, Kinesis](./12-decoupling/) | ⬜ Not Started |
| 13 | [Containers — ECS, Fargate, ECR, EKS](./13-containers/) | ⬜ Not Started |
| 14 | [Serverless — Lambda](./14-serverless-lambda/) | ⬜ Not Started |
| 15 | [Serverless — DynamoDB](./15-serverless-dynamodb/) | ⬜ Not Started |
| 16 | [Serverless — API Gateway, Cognito, Step Functions](./16-serverless-extras/) | ⬜ Not Started |
| 17 | [Databases & Analytics](./17-databases-analytics/) | ⬜ Not Started |
| 18 | [Data & Analytics](./18-data-analytics/) | ⬜ Not Started |
| 19 | [Machine Learning](./19-machine-learning/) | ⬜ Not Started |
| 20 | [Monitoring — CloudWatch, CloudTrail, Config](./20-monitoring/) | ⬜ Not Started |
| 21 | [IAM Advanced](./21-iam-advanced/) | ⬜ Not Started |
| 22 | [AWS Security & Encryption — KMS, SSM](./22-security-encryption/) | ⬜ Not Started |
| 23 | [Networking — VPC Deep Dive](./23-vpc/) | ⬜ Not Started |
| 24 | [Disaster Recovery & Migrations](./24-disaster-recovery/) | ⬜ Not Started |
| 25 | [Solution Architectures](./25-solution-architectures/) | ⬜ Not Started |
| 26 | [Well-Architected Framework & Ecosystem](./26-well-architected/) | ⬜ Not Started |
| 27 | [Practice Exam & Review](./27-practice-exam/) | ⬜ Not Started |

**Progress: 7/27 sections (26%)**

---

## 🗂️ Repository Structure

```
AWS-SAA-C03-Notes/
├── README.md
├── 01-introduction/
│   ├── notes.md
│   └── images/
├── 02-iam/
│   ├── notes.md
│   ├── key-concepts.md
│   └── images/
├── 03-ec2/
│   ├── notes.md
│   ├── instance-types.md
│   ├── security-groups.md
│   ├── purchasing-options.md
│   └── images/
├── 04-ec2-storage/
│   ├── notes.md
│   ├── ebs-vs-efs.md
│   └── images/
├── 05-elb-asg/
│   ├── notes.md
│   ├── alb-vs-nlb.md
│   ├── auto-scaling-policies.md
│   └── images/
├── 06-rds-aurora-elasticache/
│   ├── notes.md
│   ├── rds-vs-aurora.md
│   ├── read-replicas-vs-multi-az.md
│   ├── elasticache-strategies.md
│   └── images/
├── 07-route53/
│   ├── notes.md
│   ├── routing-policies.md
│   └── images/
├── 08-s3/
│   ├── notes.md
│   └── images/
├── ... (remaining sections)
├── cheat-sheets/
│   ├── services-comparison.md
│   ├── port-numbers.md
│   ├── exam-tips.md
│   └── when-to-use-what.md
└── practice/
    ├── scenario-questions.md
    └── architecture-decisions.md
```

---

## 📝 Note Format

Each section follows a consistent structure:

```markdown
# Section Title

## Overview
What this service does and why it exists.

## Key Concepts
Core ideas explained in my own words.

## Architecture & How It Works
Diagrams showing how the service fits into real architectures.

## Important Configurations
Settings and options that matter for the exam and real life.

## When to Use vs NOT Use
Decision-making framework — this is what the exam actually tests.

## Comparisons
Service A vs Service B — when to pick which.

## Exam Tips
Specific things Stephane highlighted for the exam.

## Real-World Connection
How this connects to my DevOps projects and work.
```

---

## 🧠 Key Decision Frameworks

### Database Selection
```
Need SQL + joins + transactions?
  └── YES → RDS or Aurora
  └── NO → Need key-value with millisecond latency?
              └── YES → DynamoDB
              └── NO → Need caching?
                        └── YES → ElastiCache (Redis/Memcached)
                        └── NO → Need document store?
                                  └── YES → DocumentDB
                                  └── NO → Need graph?
                                            └── YES → Neptune
```

### Storage Selection
```
Object storage (files, images, backups)?
  └── S3
Block storage (attached to EC2)?
  └── EBS
Shared file system (multiple EC2)?
  └── EFS (Linux) or FSx (Windows/Lustre)
Archival (rarely accessed)?
  └── S3 Glacier
```

### Compute Selection
```
Long-running server?
  └── EC2
Containers?
  └── ECS/Fargate (simple) or EKS (Kubernetes)
Short-running code (< 15 min)?
  └── Lambda
Batch processing?
  └── AWS Batch
```

---

## 🎯 Exam Information

| Detail | Info |
|--------|------|
| **Exam Code** | SAA-C03 |
| **Duration** | 130 minutes |
| **Questions** | 65 questions |
| **Passing Score** | 720/1000 |
| **Cost** | $150 USD |
| **Format** | Multiple choice & multiple response |
| **Target Date** | March 2026 |

### Exam Domains
| Domain | Weight |
|--------|--------|
| Design Secure Architectures | 30% |
| Design Resilient Architectures | 26% |
| Design High-Performing Architectures | 24% |
| Design Cost-Optimized Architectures | 20% |

---

## 🔗 Related Repositories

| Repo | Description |
|------|-------------|
| [DevOps Implementation Notes](https://github.com/SBitre/DevOps-Implementation---Docker-Kubernetes-AWS-EKS-Terraform-CICD-GitHub-Actions-ArgoCD-Helm-OTEL) | Hands-on DevOps with Docker, K8s, Terraform, CI/CD |
| [CampusQuick](https://github.com/SBitre/CampasQuick-Cloud_Delivery) | Serverless campus delivery platform on AWS |
| Project Wolverine (Coming Soon) | Self-healing K8s with AI incident response |

---

## 📚 Study Resources

| Resource | Type |
|----------|------|
| Ultimate AWS Certified Solutions Architect Associate 2026 — Stephane Maarek | Video Course (Udemy) |
| AWS Official Documentation | Reference |
| AWS Well-Architected Framework | Whitepaper |
| Stephane Maarek's Practice Exam | Exam Prep |

---

## 👨‍💻 Author

**Sumukh Pitre**
MS Informatics (Cloud Concentration) | Northeastern University, Boston

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/YOUR-LINKEDIN/)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/SBitre)

---

## 📄 License

These are personal study notes written in my own words based on hands-on learning. For educational purposes only.

MIT License — see [LICENSE](./LICENSE) for details.