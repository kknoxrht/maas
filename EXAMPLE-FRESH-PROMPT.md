# Example Fresh Prompt for New Content

This is a complete, ready-to-paste example for your next fresh conversation with Claude.

---

## COPY EVERYTHING BELOW THIS LINE AND PASTE INTO NEW CHAT

---

I'm creating content for the **models-as-a-service on Red Hat OpenShift AI enablement** course. This is part of an ongoing content development effort, and I need to maintain consistency with existing materials.

## Course Information

**Course**: This
**Working Directory**: `/Users/kaknox/Documents/GitHub/maas/`
**Current Chapter**: Chapter 3 - Deploying Models for MaaS
**Target Section**: modules/ch3-model-deployment/s3-deploy-overview.adoc

## Style and Quality Standards

**Primary Style Reference**: 
Read this chapter to understand the required style, depth, and structure:
`/Users/kaknox/Documents/GitHub/maas/modules/ch2-platform-admin/*


**Key Style Requirements**:
- Depth: Conceptual (WHY) + Architectural (WHAT) + Tactical (HOW)
- Tone: Technical depth with business context
- Audience: Platform Engineers working on production enterprise AI platforms
- Examples: Concrete scenarios with specific timing/metrics (not "quickly" - use "45 seconds")
- Callouts: Production guidance (TIP), warnings (WARNING), critical info (IMPORTANT)
- Integration: Reference Chapter 1 concepts, prepare for Chapter 2 lab

## Integration Requirements

**This content builds on**:
- Chapter 1, ch1-utility-ai
- Chapter 2, ch2-platform-admin



## Content Request

**GOAL**: Create this module on Deploying a model with Llm-d using OpenShift AI. 

**TARGET AUDIENCE**:
- Role: Platform Engineer
- Expertise Level: Intermediate completed Chapter 1 Models as a Service solving the GPU Silo
- Use Case Context: Production enterprise AI platform with multi-tenant workloads needing efficient access to models, with governance, chargeback, to increase velocity of AI projects and maximize the usage of expensive gpu hardware. 

**SCOPE**:

Must Cover:
- Understanding how to onboard models into the MaaS ecosystem.
- LeaderWorkerSet Operator dependencies.
- Deployment Flexibility: Deploying the platform first versus retrofitting existing models via annotations.
- Deploy a model and integrate it successfully with the MaaS feature.
- Requirement to use "Distributed inference with llm-d" for MaaS integration.
- In Advanced settings, select "Publish as MaaS endpoint" and assign it to "All tiers". 
- Serving Runtimes & Distributed Inference
- Assigning model access to "All tiers" vs. "Specific tiers".
- Configuring "Publish as MaaS endpoint" during deployment.
- Updating Access Dynamically:
- Modifying tier access post-deployment and understanding immediate 403 Forbidden impacts.




Must Exclude:
- MIG performance benchmarking (defer to Chapter 3 observability)
- Advanced MIG partitioning strategies beyond standard profiles (out of scope for this course)
- MIG on GPU models other than A100 (keep focused)
- GPU virtualization technologies other than MIG

Placement:
- Course: maas-gpu-enablement
- Chapter: ch2-mig
- Position: First section after chapter index (s1-mig-overview.adoc)

**REFERENCE MATERIALS**:

Style Reference:
- Primary: `/Users/kaknox/Documents/GitHub/maas-gpu-enablement/modules/ch1-gpu-operator/pages/s2-operators-overview.adoc`
- Pattern to match: Section 5 "Three-Layer MaaS Operator Stack" shows good depth for technical architecture

Content Reference:
- Check if `mig-reference/` directory exists with vendor documentation
- NVIDIA MIG User Guide: https://docs.nvidia.com/datacenter/tesla/mig-user-guide/
- GPU Operator MIG documentation: https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-mig.html

**DEPTH AND STYLE**:

Reading Time: 20 minutes

Content Balance:
- Conceptual (WHY): 30% - Business case for MIG (utilization metrics, cost savings)
- Architectural (WHAT): 40% - MIG profiles, modes, how partitioning works
- Practical (HOW): 30% - Configuration examples, verification commands

Tone:
- [X] Technical depth with business context
- [X] Production-ready guidance (production decision matrices)
- [X] Self-contained (assumes Chapter 1 knowledge of operators and ClusterPolicy)

Required Elements:
- [ ] Concrete example: Multi-tenant inference platform sharing A100-40GB across 3 teams
- [ ] Before/After comparison: Cluster GPU utilization with/without MIG
- [ ] ASCII diagram: MIG profile partitioning visualization on single A100
- [ ] Decision matrix: When to use MIG vs. time-slicing vs. full GPU (production decision point)
- [ ] Callouts: TIP for production MIG mode selection, WARNING about reconfiguration downtime
- [ ] Annotated ClusterPolicy YAML with MIG configuration enabled
- [ ] Specific metrics: "7x higher utilization", "reduces idle time from 60% to 15%" (not vague)
- [ ] Integration: Reference ClusterPolicy from Chapter 1, build on operator knowledge

**SUCCESS CRITERIA**:

Learning Objectives - Students should be able to:
- Explain what MIG is and articulate business value (improved GPU utilization, multi-tenant isolation)
- Identify appropriate MIG profiles for given workload requirements (memory, compute)
- Choose between MIG modes (Single vs. Mixed) for production use cases
- Understand how to enable MIG via ClusterPolicy Custom Resource (building on Chapter 1)
- Explain trade-offs between MIG, time-slicing, and full GPU allocation
- Make informed production decisions about MIG configuration

Verification:
- Students can determine correct MIG profile for workload with specific memory/compute needs
- Students can explain when MIG is appropriate vs. when time-slicing or full GPU is better
- Students can configure MIG in ClusterPolicy YAML

Integration Points:
- Builds on: Chapter 1 ClusterPolicy configuration, operator reconciliation concepts
- Prepares for: Chapter 2 MIG deployment lab, Chapter 3 monitoring MIG instances
- Cross-references: ClusterPolicy (ch1-gpu-operator/s2-operators-overview.adoc)

## Reference Materials

If `mig-reference/` directory exists in the repository, please read all files there to understand MIG concepts and configuration examples.

If not, use the online NVIDIA documentation URLs provided above as reference for technical accuracy.

## Instructions

1. **Read the style reference** (`s2-operators-overview.adoc`) first to understand depth and structure expectations
2. **Read the style guide** (`STYLE-GUIDE.md`) to understand writing patterns and quality standards
3. **Check terminology** (`TERMINOLOGY.md`) for canonical definitions of terms from Chapter 1 (ClusterPolicy, Operator, Custom Resource, etc.)
4. **Read reference materials** (mig-reference/ or online docs) to gather accurate MIG technical details
5. **Use Plan Mode** to design the page outline before writing any content
6. **Present the plan** for my review and approval before implementing
7. **After approval**, create the content following the approved plan
8. **Verify integration** by checking that:
   - ClusterPolicy is referenced with link to Chapter 1 definition
   - MIG configuration builds on Chapter 1 operator knowledge
   - Transitions to next section are smooth
9. **Update files**:
   - Create `modules/ch2-mig/pages/s1-mig-overview.adoc`
   - Update `modules/ch2-mig/nav.adoc` to include new section
   - Update chapter index if needed
10. **Build and verify** with `npm run build` to ensure no errors

---

## END OF PROMPT TO PASTE

---

# How to Use This Example

1. **Copy everything between the "COPY EVERYTHING BELOW" markers above**
2. **Customize for your actual topic**:
   - Change chapter/section names
   - Update "Must Cover" topics
   - Adjust reference materials
   - Modify success criteria
3. **Paste into new Claude conversation**
4. **Wait for Claude to read and create plan**
5. **Review plan and approve**
6. **Claude implements**

## Customization Points

When adapting this for your next topic, change:

- **Course Information** section: Chapter name, section name
- **Integration Requirements**: What it builds on, what it prepares for
- **SCOPE - Must Cover**: Your specific topics
- **SCOPE - Must Exclude**: What to defer
- **Reading Time**: Estimate based on content volume
- **Required Elements**: Examples specific to your topic
- **Learning Objectives**: What students should be able to do

## What NOT to Change

Keep these sections as-is:
- Style Reference paths (always point to s2-operators-overview.adoc)
- Style Guide reference
- Terminology reference
- Key Style Requirements (depth approach, tone)
- Instructions section (the workflow)
