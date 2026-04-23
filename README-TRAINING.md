# About this Training

**Click here: [![Contribute](https://www.eclipse.org/che/contribute.svg)](https://devspaces.apps.tools-na100.dev.ole.redhat.com/#https://github.com/RedHatQuickCourses/maas) to start the development using devspace.**

This comprehensive training course teaches Platform Engineers, AI/ML Platform Administrators, and Developers how to transform fragmented GPU infrastructure into a centralized Models-as-a-Service (MaaS) ecosystem using Red Hat OpenShift AI.

Organizations struggle with the "GPU Silo" crisis—expensive hardware trapped in team silos creates idle capacity in some departments while high-priority projects stall in others. This course shows you how to transition from "Private Power Generators" (siloed hardware) to a "Municipal Utility Grid" (centralized MaaS) with governed token-based consumption, complete audit trails, and automated chargeback capabilities.

**Duration**: 4-5 hours  
**Level**: Tech Level 3 (Internal, Partners, and Customers)  
**Prerequisites**: OpenShift 4.20+ administration, Kubernetes operators, GPU enablement basics, Red Hat OpenShift AI 3.3+

# Objectives

By completing this training, you will be able to:

## PO 1: Evaluate MaaS Architecture
- Understand the transition from hardware management to token delivery
- Map the architectural layers (KServe, Kuadrant, Gateway API)
- Articulate how MaaS prevents "Shadow AI" and redundant model deployments
- Explain the business value of centralized model serving

## PO 2: Administer the MaaS Platform
- Configure OpenShift AI Custom Resource Definitions (CRDs) to enable MaaS
- Update `DataScienceCluster` and `OdhDashboardConfig` resources
- Establish Gateway API routing and verify component health
- Activate the Gen AI Studio dashboard interface
- Troubleshoot common component enablement issues

## PO 3: Implement MaaS Governance and Observability
- Define tiered service limits (Free, Premium, Enterprise)
- Configure group-based rate limiting using Kuadrant
- Map OpenShift user groups to tier levels
- Track token consumption metrics for observability
- Calculate and automate chargeback workflows for cross-department billing

## PO 4: Consume Endpoints & Agentic Integrations
- Navigate the Gen AI Studio to discover models and view tier limits
- Generate Bearer tokens (API keys) for authentication
- Make REST calls to `/v1/models` and `/v1/chat/completions` endpoints
- Implement programmatic retry logic for 429 Rate Limit errors with exponential backoff
- Integrate models into applications via OpenAI-compatible APIs
- Handle 401 (Unauthorized) and 403 (Forbidden) errors appropriately

