---
layout: post
author: Lucas
title: AWS Cloud Practitioner Certification Notes
subtitle: Note Repository
categories: [aws]
tags: [aws, cloud, certificate, aws cloud practicioner]
---
<!--🔴 🟠 ⚫ ⚪ 🟣 🟢 🟡 🔵-->

## Compute and Networking

### Amazon EC2 (Elastic Compute Cloud)

- Secure, resizable compute capacity in the cloud. It is designed to make web-scale computing easier for developers.
- It offers the broadest choice of processor, storage, networking, OS, and purchase model.
- Pricing:
  - $ Spot Instances.
  - $$ Savings Plan.
  - $$$ Reserved Instances.
  - \$$$$ On-Demand.
  - \$\$$$$ Dedicated Host.

### ELB (Elastic Load Balancer)

- Can load balance at a regional level.
- Works both for client-server and frontend-backend relationships.

### Amazon SNS (Simple Notification Service)

- Pub/sub service.
- Subscribers can be web servers, email addresses, AWS Lambda functions, or several other options.

### Amazon SQS (Simple Queue Service)

- Send, store, and receive messages between software components, without losing messages or requiring other services to be available.

### AWS Lambda

- Serverless compute service.
- Upload code, set trigger, pay only for time used.

### Amazon ECS (Elastic Container Service)

- Enables the running and scaling of containerized applications on AWS.

### Amazon EKS (Elastic Kubernetes Service)

- Provides the flexibility of Kubernetes with the security and resiliency of being an AWS managed service.

### AWS Fargate

- Serverless compute engine for containers (Lambda for whole containers instead of code).

### Amazon CloudFront

- Amazon's CDN (Content Delivery Network) service.
- Edge location: site that Amazon CloudFront uses to store cached copies of the content closer to the customers for faster delivery.

### Amazon VPC (Virtual Private Cloud)

- Virtual private AWS network divided into sections called subnets.
- Components:
  - Internet Gateway: "Door" to enter VPC. Connection between internet and VPC.
  - Virtual Private Gateway: Gateway that allows protected internet traffic coming from approved networks only.
  - AWS Direct Connect: Dedicated private connection between data center and VPC, instead of VPN encrypting.
  - Private Subnet: Contains resources that should be only accessible through private network, such as customers' personal info.
  - Network ACLs (Access Control Lists): Virtual firewall that controls in and outbound traffic at the subnet (boundary) level. They are STATELESS: they do not remember/recognize whether it is a returning or new packet, always checks them.
  - Security Group: Virtual firewall for an EC2 instance. Default: deny all inbound, allow all outbound traffic. They are STATEFUL: they allow request responses to return regardless of rules.

### Amazon Route 53

- Amazon's managed DNS service.
- A reliable way to route end users to internet applications hosted in AWS (ALIAS records to AWS resources). Useful when resources keep changing IP addresses.
- New domain names can be registered directly in Route 53 & DNS records transferred for existing domain names managed by other domain registrars.
- Can load balance on DNS level.

## Storage

### Amazon EBS (Elastic Block Store)

- Snapshots: Incremental backups (only changed data is backed up).
- Volume size and type can be configured before provisioning, then it gets attached to the EC2 instance.
- Better for huge files, since it breaks it down to blocks and saves only what changes.
- Better for complex read, write, change functions.
- It's an AZ level resource, needs to be in the same AZ to attach to EC2 instances.
- Since it's like a physical harddrive, it doesn't automatically scale.

### Amazon S3 (Simple Storage Service)

- Web enabled (every object has a URL with access rights), regionally distributed.
- Serverless, lower cost.
- Better for complete objects and occasional changes.
- With object storage, each object consists of data, metadata (info about data), and a key (ID).
- 0 bytes to 5 TB per object.
- Consistency:
  - New upload: Immediate (available immediately).
  - Update/Deletion: Eventual.
- Tiers:
  - Standard: 11 9's of durability after 1 year.
  - Standard-IA (Infrequent Access): Lower storage price, higher retrieval price.
  - One Zone-IA: 1 availability zone instead of 3.
  - Intelligent-Tiering: Automatic tiering based on usage. Small monthly monitoring and automation fee. No performance impact or operational overhead.
  - Glacier: Lower cost, might take hours to retrieve objects.
  - Glacier Deep: Lowest cost, retrieve objects within 12 hours.
- Lifecycle Management: Policies that move data automatically between tiers based on how old they are.

### Amazon EFS (Elastic File System)

- Can have multiple instances reading and writing simultaneously.
- Linux file system.
- Regional resource, any EC2 instance can access it within the region.
- Automatically scale.

### Amazon RDS (Relational Database Service)

- Amazon's managed database.
- 6 different engines:
  - Amazon Aurora: compatible with MySQL and PostgreSQL. 6 copies at any time accross 3 AZ's, up to 15 read replicas. Continuous backup to S3, and has point-in-time recovery.
  - PostgreSQL.
  - MySQL.
  - MariaDB.
  - Oracle Database.
  - Microsoft SQL Server.

### DynamoDB

- Serverless tables that auto-scale.
- Millisecond response time.
- It's a non-relationable database, or NoSQL (doesn't support SQL-like queries).

### Amazon Redshift

- Data warehousing as a service.
- Handles big data, historical (non-changing data) analytics as opposed to operational analysis.

### Amazon DMS (Data Migration Service)

- Safely migrate data from source to target database.
- Source remains fully operational.
- Source and target don't have to be same type.
- Uses are continuous replication, database consolidation, and dev/test db migration.

### Additional DB services

- Amazon DocumentDB: Supports MongoDB workloads.
- Amazon Neptune: graph database. Good with recommendation engines and fraud detection.
- Amazon QLDB (Quantum Ledger Database): Immutable ledger.
- Amazon Managed Blockchain: Create and manage blockchain networks with open-source frameworks.
- Amazon ElastiCache: Adds caching layers on top of db. Supports 2 types of data stores, Redis and Memcached.
- Amazon DAX (DynamoDB Accelerator): In-memory cache for DynamoDB. Milli to microseconds.

## Security

### AWS IAM (Identity and Access Management)

- When an AWS account is first created, it begins with only one identity, known as the root user.
- Best practice is to never use root user unless absolutely necessary.
- MFA (Multi-Factor Authentication) is an extra layer of security, such as a second form of authentication like a random code sent to a mobile device.
- IAM user: Name and credential for a person/app that interacts with AWS services and resources. No permissions by default.
- IAM policy: Grants or denies specific permissions for specific resources and can be attached to IAM users.
- IAM group: Policies can be written for groups, and then users are assigned to the groups.
- IAM role: An identity that can be assumed to gain temporary access to permissions.

### AWS Organizations

- Central location to manage AWS accounts with consolidation billing.
- Hierarchical groupings (Organization Units) of accounts.
- SCPs (Service Control Policies): Specify maximum permission for accounts in the organization.

### AWS Artifact

- Service that provides on-demand access to AWS security and compliance reports and select online agreements.
- AWS Artifact Agreements: Review, accept, and manage agreements with AWS regarding the use of certain types of information throughout AWS services.
- AWS Artifact Reports: Provide compliance reports from third-party auditors. AWS is compliant with a variety of global, regional, and industry-specific security standards and regulations. It remains up to date with the latest reports released.

### AWS Shield

- Service that protects applications against DDoS attacks.
- Plans:
  - Standard: Automatic, no cost. Protects from the most common, frequently occurring types of DDoS attacks.
  - Advanced: Paid service with detailed attack diagnostics. Integrates with other AWS services such as CloudFront, Route 53, and ELB.

### AWS WAF (Website Application Firewall)

- Monitor and filter incoming traffic, proactive defense with machine learning.

### AWS KMS (Key Management Service)

- Encryption operations through the use of cryptographic keys.

### Amazon Inspector

- Automated security assessment against infrastructure.

### Amazon GuardDuty

- Uses integrated threat intelligence to identify threats.
- Runs independently from AWS services.
- Identifies threats by continuously monitoring the network activity and account behavior within a AWS environment.

## Monitoring and Analytics

### Amazon CloudWatch

- Monitors and manages various metrics.
- Configures alarm actions based on data from those metrics.
- Customizable dashboard feature enables access all metrics for resources from a single location.

### AWS CloudTrail

- Records API calls: ID of caller, time of call, source IP address of caller, etc.
- Events are typically updated in CloudTrail within 15 minutes after an API call.

### AWS Trusted Advisor

- Inspects AWS environment and provides real-time recommendations in accordance with AWS best practices.
- 5 categories: Cost Optimization, Performance, Security, Fault Tolerance, Service Limits.

## Pricing and Support

- Pay for what is used. Pay less for reserved resources. Pay less with volume-based discounts.
- AWS Free Tier comes in 3 types:
  - Always Free: Lambda, DynamoDB.
  - 12 Months Free: S3 Standard, EFS.
  - Trials: Inspector, Lightsail.
- AWS Pricing Calculator: Explore AWS services and create an estimate for the cost of use cases on AWS.
- AWS Billing & Cost Management dashboard: Pay AWS bills, monitor usage, and analyze and control costs.
- Consolidated Billing: Add up usage of multiple accounts within the Organization to qualify for volume-based discounts.
- AWS Budgets: Create budgets and set alarms to plan and monitor service usage, service costs, and instance reservations.
- AWS Cost Explorer: Visualize, understand, and manage AWS costs and usage over time. Offers graphs and filters.
- AWS Support tiers:
  - Basic: 24/7 Customer service, documentation, whitepapers, forums, Trusted Advisor, AWS Personal Health Dashboard.
  - Developer: Email access to customer support (24h response time).
  - Business: Full set of checks for Trusted Advisor, phone access to support, infrastructure event management.
  - Enterprise: 15-min SLA for business-critical workload, TAM (Technical Account Manager).
- TAM: Provides guidance, architectural reviews, and ongoing communication for companies to plan, deploy, and optimize applications.
- AWS Marketplace: Digital catalog that includes thousands of software listings from independent software vendors.

## Migration

- AWS CAF (Cloud Adoption Framework): Organizes guidance into six areas of focus, called Perspectives:
  - Business capabilities: Business, People, Governance.
  - Technical capabilities: Platform, Security, Operations.
- 6 different strategies (R's) for migrating applications:
  - Rehosting: Move applications "as is" to AWS without optimization.
  - Replatforming: Rehosting with a few optimizations, no core code change, no new dev efforts. Eg: MySQL -> RDS MySQL.
  - Repurchasing: Abandoning legacy software vendors and moving to another one or a cloud-based one.
  - Refactoring/Re-architecting: New code, driven by business need of new features. Highest initial cost.
  - Retaining: No need to migrate apps about to be deprecated.
  - Retiring: A % of app portfolio usually is no longer needed, can be terminated.
- AWS Snow Family:
  - AWS Snowcone: 2 CPUs, 4 GB of memory, 8 TB of usable storage
  - AWS Snowball Edge:
    - Compute Optimized: 52 vCPUs, 208GiB memory, 42 TB HDD, 7.68 TB SSD.
    - Storage Optimized: 40 vCPUs, 80GiB memory, 80 TB HDD, 1 TB SSD.
  - AWS Snowmobile: 100 PB HDD

### AWS Innovation Services

- Amazon SageMaker: Build, train, and deploy Machine Learning models quickly.
- AI services:
  - Transcribe: Convert speech to text.
  - Comprehend: Discover patterns in text.
  - Lex: Build voice and text chatbots.
  - Amazon A2I: Provides built-in human review workflows for common machine learning use cases.

## Other Notes

- AWS Cloud: Pay only for what is used.
- Factors for selecting a Region:
  - Compliance.
  - Latency/Proximity.
  - Feature/Service Availability.
  - Pricing.
- Availability Zone: A single data center or a group of data centers within a Region.
- Everything in AWS is an API call. These API calls can be crafted by the customer or facilitated by AWS:
  - DIY options:
    - AWS Management Console: Browser-based. Good for learning, testing, viewing bills, etc.
    - AWS CLI: Make API calls from own terminal (write commands).
    - SDKs (Software Development Kits): Use a specific language to make calls (write programs).
  - Managed tools:
    - AWS Elastic Beanstalk: Provide code and config settings, and EB deploys resources necessary for Load balancing, Scaling, Monitoring, etc
    - AWS CloudFormation: Infrastructure as Code. What is wanted without specifying how. CF will do that. Create an environment, called a CF Template, and CF does the rest.
- Five pillars of Well-Architected Framework:
  - Operational Excellence.
  - Security.
  - Reliability.
  - Performance Efficiency.
  - Cost Optimization.
- Benefits of the AWS Cloud:
  - Trade upfront expense for variable expense.
  - Benefit from massive economies of scale.
  - Stop guessing capacity.
  - Increase speed and agility.
  - Stop spending money running and maintaining data centers.
  - Go global in minutes.
- AWS Shared Responsibility Model:
  - AWS: Responsible for security "of" the cloud (Physical, Network, Hypervisor).
  - Customer: Responsible for security "in" the cloud (OS, Application, Data).

## Reference

- [AWS Cloud Practitioner Essentials](https://aws.amazon.com/training/digital/aws-cloud-practitioner-essentials/)
