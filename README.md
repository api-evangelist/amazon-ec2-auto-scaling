# Amazon EC2 Auto Scaling (amazon-ec2-auto-scaling)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
