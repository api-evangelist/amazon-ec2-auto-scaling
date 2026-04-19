# Amazon EC2 Auto Scaling (amazon-ec2-auto-scaling)
Amazon EC2 Auto Scaling helps you maintain application availability and lets you automatically add or remove EC2 instances according to conditions you define. You can use fleet management features to maintain the health and availability of your fleet, and use dynamic and predictive scaling to add or remove EC2 instances to meet demand.

**URL:** [Visit APIs.json URL](https://aws.amazon.com/ec2/autoscaling/)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags:

 - Amazon Web Services, Auto Scaling, AWS, Compute, EC2, High Availability, Scaling

## Timestamps

- **Created:** 2026-03-16
- **Modified:** 2026-04-19

## APIs

### Amazon EC2 Auto Scaling API
The Amazon EC2 Auto Scaling API provides programmatic access to create and manage Auto Scaling groups, launch configurations, scaling policies, scheduled actions, lifecycle hooks, and warm pools for automatic capacity management.

**Human URL:** [https://aws.amazon.com/ec2/autoscaling/](https://aws.amazon.com/ec2/autoscaling/)

#### Tags:

 - Auto Scaling, Capacity Management, Compute, EC2

#### Properties

- [Documentation](https://docs.aws.amazon.com/autoscaling/ec2/APIReference/Welcome.html)
- [OpenAPI](openapi/amazon-ec2-auto-scaling-openapi.yaml)
- [OpenAPI](https://api.apis.guru/v2/specs/amazonaws.com/autoscaling/2011-01-01/openapi.yaml)
- [JSONSchema](json-schema/ec2-auto-scaling-auto-scaling-group-schema.json)
- [JSONLD](json-ld/amazon-ec2-auto-scaling-context.jsonld)
- [GettingStarted](https://docs.aws.amazon.com/autoscaling/ec2/userguide/get-started-with-ec2-auto-scaling.html)
- [Pricing](https://aws.amazon.com/ec2/autoscaling/pricing/)
- [FAQ](https://aws.amazon.com/ec2/autoscaling/faqs/)
- [APIReference](https://docs.aws.amazon.com/autoscaling/ec2/APIReference/)
- [Authentication](https://docs.aws.amazon.com/autoscaling/ec2/APIReference/CommonParameters.html)
- [RateLimits](https://docs.aws.amazon.com/autoscaling/ec2/APIReference/ec2-auto-scaling-api-throttling.html)
- [JSONSchema](json-schema/ec2-auto-scaling-accelerator-count-request-schema.json)
- [JSONStructure](json-structure/ec2-auto-scaling-accelerator-count-request-structure.json)
- [Example](examples/ec2-auto-scaling-accelerator-count-request-example.json)

## Common Properties

- [Portal](https://aws.amazon.com/)
- [DeveloperPortal](https://aws.amazon.com/developer/)
- [Documentation](https://docs.aws.amazon.com/autoscaling/)
- [TermsOfService](https://aws.amazon.com/service-terms/)
- [PrivacyPolicy](https://aws.amazon.com/privacy/)
- [Support](https://aws.amazon.com/support/)
- [Blog](https://aws.amazon.com/blogs/compute/category/compute/auto-scaling/)
- [GitHubOrganization](https://github.com/aws)
- [Console](https://console.aws.amazon.com/ec2/autoscaling/)
- [SignUp](https://portal.aws.amazon.com/billing/signup)
- [Login](https://signin.aws.amazon.com/)
- [StatusPage](https://health.aws.amazon.com/health/status)
- [KnowledgeCenter](https://repost.aws/knowledge-center)
- [YouTube](https://www.youtube.com/user/AmazonWebServices)
- [StackOverflow](https://stackoverflow.com/questions/tagged/amazon-ec2-auto-scaling)
- [Contact](https://aws.amazon.com/contact-us/)
- [Security](https://aws.amazon.com/security/)
- [Compliance](https://aws.amazon.com/compliance/)
- [SpectralRules](rules/amazon-ec2-auto-scaling-spectral-rules.yml)
- [Vocabulary](vocabulary/amazon-ec2-auto-scaling-vocabulary.yaml)
- [NaftikoCapability](capabilities/ec2-auto-scaling-management.yaml)

## Features

| Name | Description |
|------|-------------|
| Dynamic Scaling | Automatically scales EC2 capacity up or down in response to real-time demand using target tracking, step, or simple scaling policies. |
| Predictive Scaling | Uses machine learning to forecast future demand and proactively adds EC2 instances ahead of anticipated load spikes. |
| Scheduled Scaling | Scales capacity at pre-defined times based on predictable load patterns or business cycles. |
| Fleet Management | Automatically detects and replaces unhealthy instances to maintain application availability and fleet health. |
| Instance Refresh | Gradually updates EC2 instances in an Auto Scaling group with new AMIs or launch template versions. |
| Warm Pools | Pre-initializes EC2 instances to reduce latency for scale-out events by keeping a pool of instances in a stopped or running state. |
| Mixed Instances Policy | Combines On-Demand and Spot instances with multiple instance types for cost optimization and availability. |
| Lifecycle Hooks | Pauses instance launch or termination to perform custom actions such as installing software or draining connections. |

## Use Cases

| Name | Description |
|------|-------------|
| Web Application Scaling | Automatically scale web server fleets to handle variable HTTP traffic loads without over-provisioning. |
| Batch Processing | Scale compute capacity up when jobs arrive and down when completed to minimize costs for batch workloads. |
| Microservices Autoscaling | Independently scale each microservice based on its own traffic patterns and resource utilization. |
| Cost Optimization | Combine Spot and On-Demand instances to reduce EC2 costs while maintaining availability. |
| Blue/Green Deployments | Use instance refresh to gradually replace old instances with new ones for zero-downtime deployments. |

## Integrations

| Name | Description |
|------|-------------|
| Amazon EC2 | Launches and terminates EC2 instances based on scaling policies and schedules. |
| Amazon CloudWatch | Uses CloudWatch metrics and alarms to trigger dynamic scaling policies automatically. |
| Elastic Load Balancing | Automatically registers new instances with load balancers and deregisters terminated instances. |
| AWS Systems Manager | Integrates with Systems Manager for instance configuration and patch management during lifecycle hooks. |
| Amazon SNS | Sends notifications for scaling events, instance launches, and terminations via SNS topics. |
| AWS Cost Management | Works with Cost Explorer and Budgets to monitor and optimize spend on Auto Scaling fleets. |

## Artifacts

Machine-readable API specifications organized by format.

### OpenAPI

- [Amazon Ec2 Auto Scaling](openapi/amazon-ec2-auto-scaling-openapi.yaml)

### JSON Schema

- [Ec2 Auto Scaling Accelerator Count Request](json-schema/ec2-auto-scaling-accelerator-count-request-schema.json)
- [Ec2 Auto Scaling Accelerator Manufacturer](json-schema/ec2-auto-scaling-accelerator-manufacturer-schema.json)
- [Ec2 Auto Scaling Accelerator Manufacturers](json-schema/ec2-auto-scaling-accelerator-manufacturers-schema.json)
- [Ec2 Auto Scaling Accelerator Name](json-schema/ec2-auto-scaling-accelerator-name-schema.json)
- [Ec2 Auto Scaling Accelerator Names](json-schema/ec2-auto-scaling-accelerator-names-schema.json)
- [Ec2 Auto Scaling Accelerator Total Memory Mi B Request](json-schema/ec2-auto-scaling-accelerator-total-memory-mi-b-request-schema.json)
- [Ec2 Auto Scaling Accelerator Type](json-schema/ec2-auto-scaling-accelerator-type-schema.json)
- [Ec2 Auto Scaling Accelerator Types](json-schema/ec2-auto-scaling-accelerator-types-schema.json)
- [Ec2 Auto Scaling Active Instance Refresh Not Found Fault](json-schema/ec2-auto-scaling-active-instance-refresh-not-found-fault-schema.json)
- [Ec2 Auto Scaling Activities](json-schema/ec2-auto-scaling-activities-schema.json)
- *...and 363 more*

### JSON Structure

- [Ec2 Auto Scaling Accelerator Count Request](json-structure/ec2-auto-scaling-accelerator-count-request-structure.json)
- [Ec2 Auto Scaling Accelerator Manufacturer](json-structure/ec2-auto-scaling-accelerator-manufacturer-structure.json)
- [Ec2 Auto Scaling Accelerator Manufacturers](json-structure/ec2-auto-scaling-accelerator-manufacturers-structure.json)
- [Ec2 Auto Scaling Accelerator Name](json-structure/ec2-auto-scaling-accelerator-name-structure.json)
- [Ec2 Auto Scaling Accelerator Names](json-structure/ec2-auto-scaling-accelerator-names-structure.json)
- [Ec2 Auto Scaling Accelerator Total Memory Mi B Request](json-structure/ec2-auto-scaling-accelerator-total-memory-mi-b-request-structure.json)
- [Ec2 Auto Scaling Accelerator Type](json-structure/ec2-auto-scaling-accelerator-type-structure.json)
- [Ec2 Auto Scaling Accelerator Types](json-structure/ec2-auto-scaling-accelerator-types-structure.json)
- [Ec2 Auto Scaling Active Instance Refresh Not Found Fault](json-structure/ec2-auto-scaling-active-instance-refresh-not-found-fault-structure.json)
- [Ec2 Auto Scaling Activities](json-structure/ec2-auto-scaling-activities-structure.json)
- *...and 363 more*

### JSON-LD

- [Amazon Ec2 Auto Scaling](json-ld/amazon-ec2-auto-scaling-context.jsonld)

## Capabilities

Naftiko capabilities organized as shared per-API definitions composed into customer-facing workflows.

### Shared Per-API Definitions

- [Ec2 Auto Scaling](capabilities/shared/ec2-auto-scaling.yaml) — 148 operations

### Workflow Capabilities

| Workflow | Tools | Persona |
|----------|-------|---------|
| [Ec2 Auto Scaling Management](capabilities/ec2-auto-scaling-management.yaml) | 10 | managing EC2 Auto Scaling groups, scaling policies, and lifecycle hooks for cloud operations engineers |

## Vocabulary

- [Amazon EC2 Auto Scaling Vocabulary](vocabulary/amazon-ec2-auto-scaling-vocabulary.yaml) — Unified taxonomy mapping 20 resources, 23 actions, 1 workflows, and 1 personas across operational (OpenAPI) and capability (Naftiko) dimensions

## Rules

- [Amazon Ec2 Auto Scaling Spectral Rules](rules/amazon-ec2-auto-scaling-spectral-rules.yml) — 19 rules enforcing Amazon EC2 Auto Scaling API conventions

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com