# Red Hat OpenShift AI: Solving the GPU Silo with MaaS Enablement

**Course Code**: rhoai3-maas  
**Version**: 3.3  
**Duration**: 4-5 hours  
**Level**: Tech Level 3 (Internal, Partners, and Customers)

## Course Overview

This course equips Platform Engineers, AI/ML Platform Administrators, and Developers with the technical expertise to design, deploy, and manage a scalable Models-as-a-Service (MaaS) ecosystem within Red Hat OpenShift AI.

Organizations face a "GPU Silo" crisis where expensive, limited hardware is trapped within individual teams, creating simultaneous idle capacity and project bottlenecks. This training teaches you to transform GPU infrastructure from "Private Power Generators" (siloed hardware) to a centralized "Municipal Utility Grid" with robust governance, usage quotas, and seamless integration of production-ready AI endpoints.

## Learning Objectives

By completing this course, you will be able to:

1. **Evaluate MaaS Architecture**: Understand the transition from hardware management to token delivery, map the architectural layers (KServe, Kuadrant, Gateway API), and articulate how MaaS prevents "Shadow AI"

2. **Administer the MaaS Platform**: Configure the OpenShift AI CRDs to enable the MaaS component, establish Gateway API routing, and activate the dashboard interface

3. **Implement MaaS Governance and Observability**: Define tiered service limits, configure group-based rate limiting, track token consumption metrics, and automate chargeback workflows

4. **Consume Endpoints & Agentic Integrations**: Navigate the Gen AI studio, generate authentication tokens, implement programmatic retry logic for rate limits, and integrate models into applications via OpenAI-compatible APIs

## Target Audience

- **Platform Engineers**: Configure MaaS through the OpenShift AI operator, establish tiers, and set rate limits
- **AI/ML Platform Administrators**: Manage day-to-day governance, user group assignments, and observability dashboards
- **Developers**: Consume MaaS endpoints and API tokens to integrate models into downstream applications
- **DevOps / SREs**: Monitor cluster health, gateway routing, and resource exhaustion

## Prerequisites

This course assumes you have:

- Red Hat OpenShift administration experience (Version 4.20+)
- Basic Kubernetes operator management knowledge
- Familiarity with core GPU concepts and generic GPU enablement (drivers, hardware certification)
- A deployed instance of Red Hat OpenShift AI 3.3 or later

## Course Modules

### Module 1: The Shift to Utility AI & Architecture Foundation
- **1.1**: The GPU Silo Crisis
- **1.2**: Hardware & Platform Prerequisites
- **1.3**: The MaaS Architecture Stack

### Module 2: Administering the MaaS Platform
- **2.1**: Enabling the Grid (CRD Configuration)
- **2.2**: Deployment Workflows
- **2.3**: Troubleshooting Component Enablement Lab

### Module 3: Deploying Models to the MaaS Catalog
- **3.1**: Serving Runtimes & Distributed Inference
- **3.2**: Lab - Publishing as a MaaS Endpoint
- **3.3**: Lab - Updating Access Dynamically

### Module 4: Governance, Observability, and Chargeback
- **4.1**: Defining Governance Tiers
- **4.2**: Rate Limits and Quotas
- **4.3**: Lab - Observability & Automated Chargeback

### Module 5: API Integration & Consumer Experience
- **5.1**: The Developer Dashboard
- **5.2**: Lab - Consuming the OpenAI-Compatible API
- **5.3**: Lab - Error Handling & Application Patterns

## Key Technologies Covered

- Red Hat OpenShift Container Platform (4.20+)
- Red Hat OpenShift AI (3.3 or 3.4)
- KServe (Model serving infrastructure)
- Kuadrant (Authorino/Limitador for auth and rate limiting)
- OpenShift Gateway API (Network traffic routing)
- LeaderWorkerSet Operator (Required for distributed inference)
- Llama Stack Operator (Required for the dashboard UI)

## Business Value

### Without MaaS:
- GPU hardware trapped in team silos creates idle capacity and bottlenecks simultaneously
- Multiple teams download and deploy the same models redundantly
- No audit trail for compliance requirements (Security "Black Boxes")
- Cannot track usage across users or forecast future compute needs
- Data scientists waste time debugging infrastructure instead of tuning models

### With MaaS:
- Centralized model catalog eliminates redundant deployments and wasted resources
- Fair resource allocation prevents team conflicts and maximizes GPU utilization
- Complete audit logs and PII filtering via API gateway
- Token-based consumption enables accurate cross-department financial chargeback
- Developers focus on applications, not infrastructure

## Building and Viewing the Course

### Local Development

1. **Install dependencies**:
   ```bash
   npm install
   ```

2. **Build the course**:
   ```bash
   npm run build
   ```

3. **Serve locally**:
   ```bash
   npm run serve
   ```

4. **View in browser**:
   ```
   http://localhost:8080
   ```

### Watch Mode (Development)

For active content development:

```bash
# Terminal 1: Watch for AsciiDoc changes
npm run watch:adoc

# Terminal 2: Serve the site
npm run serve
```

## Course Development

### Content Structure

This is an Antora-based documentation project:

```
maas/
├── antora.yml                    # Course metadata and navigation
├── modules/
│   ├── ROOT/                     # Course home and objectives
│   ├── LABENV/                   # Lab environment setup
│   ├── ch1-utility-ai/           # Module 1: Architecture
│   ├── ch2-platform-admin/       # Module 2: Administration
│   ├── ch3-model-deployment/     # Module 3: Model Deployment
│   ├── ch4-governance/           # Module 4: Governance
│   ├── ch5-api-integration/      # Module 5: API Integration
│   └── appendix/                 # Reference materials
└── images/                       # Course-wide images
```

### RHOAI Style Guide

This course follows Red Hat OpenShift AI (RHOAI) content patterns:

- **Bold italic lead openers** - Declarative, authoritative statements
- **Business narrative** - Cost, compliance, efficiency, governance focus
- **Developer vs Platform Engineer** pattern
- **Shadow AI vs Factory AI** contrast
- **Definition lists** for objectives and key concepts
- **"What's Next"** sections with capability focus (not just content preview)
- **Specific examples** - Real commands, YAML, cost numbers
- **Verification steps** in all labs

See `skills/` directory for detailed style guide specifications.

## Project Files

| File/Directory | Purpose |
|----------------|---------|
| `antora.yml` | Course component metadata and module navigation |
| `antora-playbook.yml` | Build configuration for Antora |
| `modules/` | Course content organized by module/chapter |
| `prompts/course-design.md` | Original course design document |
| `skills/` | RHOAI course development skill specifications |
| `COURSE-GENERATION-SUMMARY.md` | Detailed generation report |
| `USAGEGUIDE.adoc` | AsciiDoc editing guidelines |
| `DEVSPACE.md` | Development environment setup |

## Course Status

**Current Status**: Modules 1-2 Complete, Modules 3-5 Structured

- ✅ **Module 1**: Complete (3/3 sections with full content)
- ✅ **Module 2**: Complete (3/3 sections with full content)
- 🟡 **Module 3**: Structure created, content in progress
- 🟡 **Module 4**: Structure created, content in progress
- 🟡 **Module 5**: Structure created, content in progress

See `COURSE-GENERATION-SUMMARY.md` for detailed status and next steps.

## Contributing

This course follows the RHOAI style patterns documented in the `skills/` directory. When adding or editing content:

1. Read the relevant skill documentation in `skills/*/SKILL.md`
2. Follow the business narrative patterns in `skills/create-rhoai-module/prompts/business-narrative.txt`
3. Ensure all labs include verification steps with expected output
4. Use definition lists for objectives and key concepts
5. Test all commands on OpenShift AI 3.3+ before committing

## Subject Matter Experts

- Taylor Smith - Subject Matter Expert
- James Harmison - Subject Matter Expert
- Jonathan Zarecki - Product Owner (Consulted only)
- Karlos Knox - Content Architect

## License

Copyright © Red Hat, Inc.

## Resources

- [Red Hat OpenShift AI Documentation](https://docs.redhat.com/en/documentation/red_hat_openshift_ai)
- [KServe Documentation](https://kserve.github.io/website/)
- [Kuadrant Documentation](https://docs.kuadrant.io/)
- [Gateway API Documentation](https://gateway-api.sigs.k8s.io/)

---

**Questions or feedback?** See the course generation summary or contact the content architect.
