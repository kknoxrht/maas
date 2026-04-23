# Red Hat OpenShift AI Training: Solving the GPU Silo - with MaaS Enablement

## SMEs for Development

* Taylor Smith - Subject Matter Expert
* James Harmison - Subject Matter Expert
* Jonathan Zarecki - Product Owner (Consulted only)
* Karlos Knox - Content Architect

## Problem Statement

Organizations currently face a "GPU Silo" crisis where expensive, limited hardware is trapped within individual teams. This leads to simultaneous idle capacity and project bottlenecks. Without a centralized Models-as-a-Service (MaaS) layer, teams request and manage their own dedicated GPUs, causing expensive hardware to sit under-utilized while high-priority projects stall. 

This fragmentation creates several operational failures:
* Data scientists waste time debugging CUDA drivers, Docker containers, and K8s manifests instead of tuning models.
* Multiple teams download and deploy the exact same generative AI models, leading to redundant deployments and wasted resources.
* Without a centralized API gateway, there is zero visibility into data routing, making audit logs and PII filtering impossible to enforce (Security "Black Boxes").
* Organizations have no way to track usage across users or forecast future compute needs.

## Course Goal

To equip Platform Engineers, AI/ML Platform Administrators, and Developers with the technical expertise to design, deploy, and manage a scalable Models-as-a-Service (MaaS) ecosystem within OpenShift AI. By the end of this training, participants will be able to transition their infrastructure from "Private Power Generators" (siloed hardware) to a centralized "Municipal Utility Grid". Participants will learn to configure robust governance, enforce usage quotas, and seamlessly integrate production-ready AI endpoints into downstream applications using a token-based delivery model.

## Major Changes in this Release

* **Transition to Token Delivery**: Shifts the focus from generic hardware management to a centralized inference strategy where lines of business consume "tokens" as a utility.
* **MaaS-Specific Observability**: Moves away from standard infrastructure metrics (e.g., DCGM hardware temperature) to tracking "Authorized Hits" and token counts per user/group.
* **Built-in Governance**: Introduces tier-based access control (Free, Premium, Enterprise) to enforce request rate limits and token quotas directly within the platform.
* **Automated Chargeback Integration**: Teaches administrators how to use token hit data to generate cross-department financial reports based on exact consumption.

## Target Audience

* **Platform Engineers**: Responsible for configuring MaaS through the OpenShift AI operator, establishing tiers, and setting rate limits.
* **AI/ML Platform Administrators**: Managing the day-to-day governance, user group assignments, and observability dashboards.
* **Developers**: Consuming MaaS endpoints and API tokens to integrate models into downstream applications.
* **DevOps / SREs**: Monitoring cluster health, gateway routing, and resource exhaustion.
* **Target Level**: Tech Level 3 (Internal, Partners, and Customers).

## Prerequisite Skills/Knowledge

* Red Hat OpenShift administration experience (Version 4.20+).
* Basic Kubernetes operator management.
* Familiarity with core GPU concepts and generic GPU enablement (drivers, hardware certification), which is required before installing MaaS.
* A deployed instance of Red Hat OpenShift AI 3.3 or later.

## Performance Objectives (POs)

* **[PO 1] Evaluate MaaS Architecture**: Understand the transition from hardware management to token delivery, map the architectural layers (KServe, Kuadrant, Gateway API), and articulate how MaaS prevents "Shadow AI".
* **[PO 2] Administer the MaaS Platform**: Configure the OpenShift AI CRDs to enable the MaaS component, establish Gateway API routing, and activate the dashboard interface.
* **[PO 3] Implement MaaS Governance and Observability**: Define tiered service limits, configure group-based rate limiting, track token consumption metrics, and automate chargeback workflows.
* **[PO 4] Consume Endpoints & Agentic Integrations**: Navigate the Gen AI studio, generate authentication tokens, implement programmatic retry logic for rate limits, and integrate models into applications via OpenAI-compatible APIs.

## Considerations and Risks

* **Hardware Requirements**: MaaS requires certified data center grade accelerators (e.g., NVIDIA Turing/Ampere/Hopper/Blackwell, AMD CDNA, IBM Spyre). 
* **Runtime Limitations**: Currently, only the "Distributed inference with llm-d" runtime supports MaaS integration. Selecting a different runtime prevents publishing the model as a MaaS endpoint.
* **Strict Dependencies**: MaaS deployment will fail if the Red Hat Connectivity Link (Kuadrant), cert-manager, LeaderWorkerSet, or Gateway API operators are not properly configured beforehand.
* **Deletion Risks**: Deleting the final remaining tier in the system breaks the UI's ability to add new tiers.

## Products/Technologies

* Red Hat OpenShift Container Platform (4.20+)
* Red Hat OpenShift AI (3.3 or 3.4)
* KServe (Model serving infrastructure)
* Kuadrant (Authorino/Limitador for auth and rate limiting)
* OpenShift Gateway API (Network traffic routing)
* LeaderWorkerSet Operator (Required for distributed inference)
* Llama Stack Operator (Required for the dashboard UI)

## Progressive Diagram Build Strategy

* **Module 1 (The Crisis vs. The Solution)**: Visualizing the "Private Power Generator" (isolated GPUs) vs. the "Municipal Utility Grid" (centralized MaaS).
* **Module 2 (The Platform)**: Layered architecture showing a user request passing through the Gateway API, validated by Kuadrant, and served by KServe.
* **Module 3 (The Governance)**: A flowchart mapping OpenShift User Groups to Priority Tiers (Free, Premium, Enterprise) and their corresponding rate limits.
* **Module 4 (The Integration)**: Developer workflow showing API key generation, token consumption, and handling 429 Rate Limit HTTP responses.

---

## HIGH LEVEL DESIGN (HLD)

### Module 1: The Shift to Utility AI & Architecture Foundation

**Module Goal**: Evaluate MaaS concepts, define the business value of centralized orchestration, and map the foundational architecture.

**Key Sections**:
* **1.1: The GPU Silo Crisis**: 
    * Problem: Fragmented hardware, "Shadow AI", and redundant model deployments.
    * Solution: The "AI Utility Grid"—central IT manages the inference strategy, LOBs consume tokens.
* **1.2: Hardware & Platform Prerequisites**: 
    * Generic GPU enablement as the "foundation" and MaaS as the "wiring".
    * Certified accelerators required (NVIDIA, AMD, IBM Spyre).
* **1.3: The MaaS Architecture Stack**: 
    * KServe (Serving), Kuadrant (Circuit Breakers/Policy), Gateway API (Routing).

**Learning Path**: Presentation → Architecture Review → Use Case Discussion

---

### Module 2: Administering the MaaS Platform

**Module Goal**: Configure the RHOAI Custom Resource Definitions (CRDs) to enable the MaaS infrastructure and expose the Gen AI dashboard.

**Key Sections**:
* **2.1: Enabling the Grid (CRD Configuration)**:
    * Updating `DataScienceCluster` to set `modelsAsService.managementState: Managed`.
    * Enabling the dashboard interface via `OdhDashboardConfig` (`genAiStudio: true`, `modelAsService: true`).
* **2.2: Deployment Workflows**:
    * Platform First vs. Model Retrofit (enrolling existing models via annotations).
* **2.3: Troubleshooting Component Enablement**:
    * Verifying the `maas-api` pod, `maas-default-gateway`, and Kuadrant readiness.

**Learning Path**: Presentation → Platform Enablement Lab → Troubleshooting Scenarios

---

### Module 3: Deploying Models to the MaaS Catalog

**Module Goal**: Deploy generative AI models, configure distributed serving runtimes, and publish them to the governed catalog.

**Key Sections**:
* **3.1: Serving Runtimes & Distributed Inference**:
    * Requirement to use "Distributed inference with llm-d" for MaaS integration.
    * LeaderWorkerSet Operator dependencies.
* **3.2: Publishing as a MaaS Endpoint**:
    * Configuring "Publish as MaaS endpoint" during deployment.
    * Assigning model access to "All tiers" vs. "Specific tiers".
* **3.3: Updating Access Dynamically**:
    * Modifying tier access post-deployment and understanding immediate 403 Forbidden impacts.

**Learning Path**: Presentation → Model Deployment Lab → Access Update Lab

---

### Module 4: Governance, Observability, and Chargeback

**Module Goal**: Establish service tiers, enforce request limits, and track token consumption for cross-department billing.

**Key Sections**:
* **4.1: Defining Governance Tiers**:
    * Default tiers: Free (Level 0), Premium (Level 1), Enterprise (Level 2).
    * Mapping OpenShift user groups to tier levels (highest level wins).
* **4.2: Rate Limits and Quotas**:
    * Request rate limits (requests per minute) vs. Token consumption limits.
    * Enforced by Kuadrant's Limitador component.
* **4.3: Observability & Automated Chargeback**:
    * Infrastructure metrics (DCGM/Temperature) vs. MaaS metrics (Authorized Hits/Tokens).
    * Calculating billing: $$Cost = (Input Tokens \times Rate_{in}) + (Output Tokens \times Rate_{out})$$.

**Learning Path**: Presentation → Tier Creation Lab → Chargeback Calculation Lab

---

### Module 5: API Integration & Consumer Experience

**Module Goal**: Enable developers to autonomously discover models, generate authentication, and integrate endpoints into applications.

**Key Sections**:
* **5.1: The Developer Dashboard**:
    * Navigating Gen AI Studio to view the model catalog and active tier limits.
    * Generating Bearer tokens (API Keys).
* **5.2: Consuming the OpenAI-Compatible API**:
    * Making `/v1/models` and `/v1/chat/completions` REST calls.
    * Testing models interactively in the MaaS Playground.
* **5.3: Error Handling & Application Patterns**:
    * Handling 401 (Unauthorized) and 403 (Forbidden) errors.
    * Implementing Python retry logic with exponential backoff for 429 Rate Limit errors (`Retry-After` header).

**Learning Path**: Presentation → Token Generation Lab → API Integration & Retry Scripting Lab

---

## Integration with rhoai3-skills Toolkit

This design document maps directly to the specific capabilities of OpenShift AI's MaaS implementation:
* **Clear Learning Objectives**: Each module addresses specific persona needs (Platform Engineer vs. Developer).
* **Business Scenario Integration**: Frames technical configuration around solving the real-world "Shadow AI" and budget allocation problems.
* **Lab-Heavy**: Includes practical OpenShift CRD manipulation, KServe deployment, and `curl`/Python API integration.
* **Measurement Focus**: Teaches how to translate technical token limits into financial chargeback mechanisms.

## Module-to-Skill Mapping

| Module | Presentation Sections | Lab Sections |
| :--- | :--- | :--- |
| 1: The Shift to Utility AI | GPU Silo Crisis, MaaS Stack Architecture | Architecture mapping exercise |
| 2: Administering the Platform | CRDs, Dashboard Enablement, Gateway | OpenShift CRD config, troubleshooting enablement |
| 3: Deploying Models | `llm-d` runtime, publishing endpoints | Deploying a generative model to the MaaS catalog |
| 4: Governance & Observability | Tier mapping, rate limits, chargeback math | Creating custom tiers, applying token quotas |
| 5: API Integration | Token generation, OpenAI APIs, 429 errors | Python application integration with backoff logic |

## Success Metrics

**For Learners**:
* Can successfully update OpenShift CRDs to deploy the MaaS architecture.
* Can publish an LLM and bind it to a strictly governed consumption tier.
* Can generate API tokens and execute chat completions programmatically.

**For Organization**:
* Eliminates "Shadow AI" by providing an officially sanctioned, easily accessible model gateway.
* Maximizes existing GPU hardware utilization by pooling resources.
* Enables accurate cross-department financial billing based on exact token consumption.