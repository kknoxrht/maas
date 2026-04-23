# Skill: create-rhoai-design-document

**Purpose**: Generate comprehensive course design documents that maximize the effectiveness of the rhoai3-skills toolkit, following the proven format from rhoai3-maas and optimized for RHOAI courses.

**Category**: Planning & Design  
**Status**: Active  
**Version**: 1.0

---

## Quick Reference

| Input | Example |
|-------|---------|
| Course topic | "Fine-Tuning Language Models on OpenShift AI" |
| Target audience | "Platform Engineers, ML Engineers, DevOps" |
| Duration | "8-12 hours" |
| Number of modules | "5-6" |
| Problem domain | "Organizations struggle with domain-specific model adaptation" |

| Output | Description |
|--------|-------------|
| Design document | Complete markdown file following rhoai3 format |
| Module structure | 5-6 modules with clear hierarchy |
| POs (Performance Objectives) | 4-6 measurable learning outcomes |
| HLD (High Level Design) | Module-by-module breakdown with labs |
| Lab recommendations | Where to place hands-on exercises |
| Toolkit integration guide | How to use create-rhoai-module for each section |

---

## What This Skill Does

The **create-rhoai-design-document** skill generates professional course design documents that:

1. **Follow Proven Format** — Uses the rhoai3-maas format as baseline, adapted for your domain
2. **Articulate Business Value** — Clear problem statement showing why the course matters
3. **Define Clear Outcomes** — 4-6 performance objectives with measurable criteria
4. **Structure for Toolkit Usage** — Breaks down into logical sections optimized for create-rhoai-module
5. **Include Labs Throughout** — Identifies where hands-on exercises belong
6. **Plan Progressive Learning** — Flows from concepts → implementation → validation
7. **Provide Realistic Content** — References real models, tools, scenarios, cost numbers

---

## Skill Parameters

### Required Parameters

**`course_topic`** (string)
- What is the course about?
- Should be specific and action-oriented
- Example: "Fine-Tuning Language Models on OpenShift AI"
- Bad example: "Models" (too vague)

**`problem_statement`** (string)
- What challenge does this course address?
- Should show the "Shadow X" vs "Factory X" tension (RHOAI pattern)
- Should highlight business impact (cost, efficiency, governance, risk)
- Example: "Organizations download models ad-hoc without governance, creating compliance and cost risks. A platform approach solves this."

**`target_roles`** (array of strings)
- Who should take this course? (Platform Engineers, ML Engineers, DevOps, etc.)
- Each role has different motivations and background knowledge
- Example: ["Platform Engineers", "ML Engineers", "DevOps/SREs"]

**`estimated_duration`** (string)
- Total course time in hours
- Example: "8-12 hours" or "20-30 hours"
- Used to distribute time across modules

### Optional Parameters

**`num_modules`** (integer, optional)
- How many modules/chapters? Default: 5-6
- 5-6 modules typical for 8-12 hour courses
- 8-10 modules for 20-30 hour courses

**`primary_technology`** (string, optional)
- Main focus (MaaS, Fine-Tuning, Storage, GPU Optimization, etc.)
- Helps tailor examples and scenarios
- Default: inferred from course_topic

**`domain_examples`** (array, optional)
- Specific domains to use (finance, healthcare, legal, manufacturing)
- Makes course more relevant and memorable
- Example: ["Financial Analysis", "Medical Imaging", "Legal Documents"]

**`prerequisite_experience`** (string, optional)
- What should learners already know?
- Example: "Basic Kubernetes, understanding of ML concepts"
- Used in Prerequisites section

**`capstone_style`** (string, optional)
- How should the capstone be structured?
- Options: "individual-project", "domain-options", "real-world-scenario"
- Default: "domain-options" (students pick their domain)

**`include_cost_analysis`** (boolean, optional)
- Should the design emphasize cost modeling and ROI?
- Recommended for infrastructure/optimization courses
- Default: true

**`smes`** (array, optional)
- Subject matter experts involved in course development
- Example: ["Jane Smith", "John Doe"]
- If not provided, design notes where SMEs should be identified

---

## How to Use This Skill

### Basic Usage

```bash
/create-rhoai-design-document \
  --course-topic "Fine-Tuning Language Models on OpenShift AI" \
  --problem-statement "Organizations struggle to adapt models for domain-specific tasks. Without a platform approach, they either lose data privacy or incur cloud lock-in costs." \
  --target-roles "Platform Engineers" "ML Engineers" "DevOps/SREs"
```

This generates a complete design document with:
- ✅ Problem statement
- ✅ Course goal and learning outcomes
- ✅ Target audience and prerequisites
- ✅ 5-6 modules with clear learning objectives
- ✅ High-level design for each module
- ✅ Lab placement recommendations
- ✅ Integration with create-rhoai-module skill
- ✅ Success metrics

### With Domain Examples

```bash
/create-rhoai-design-document \
  --course-topic "GPU Optimization for Large Language Model Inference" \
  --problem-statement "GPU infrastructure is expensive and underutilized without proper governance and resource sharing." \
  --target-roles "Platform Engineers" "DevOps" \
  --domain-examples "Financial Trading" "Healthcare AI" "Customer Support Chatbots" \
  --include-cost-analysis true
```

This adds domain-specific examples throughout (cost models for finance vs healthcare, etc.).

### Full Customization

```bash
/create-rhoai-design-document \
  --course-topic "Model Registry & Governance on OpenShift AI" \
  --problem-statement "Without a central model registry, organizations can't track which models are in production or who approved them." \
  --target-roles "Platform Engineers" "Architects" \
  --num-modules 4 \
  --primary-technology "Model Governance" \
  --estimated-duration "6-8 hours" \
  --capstone-style "real-world-scenario" \
  --smes "Alice Johnson" "Bob Chen"
```

---

## Output Specification

### File Structure

Generated design document includes:

```markdown
# [Course Title]

## SMEs for Development
[Names and roles]

## Problem Statement
[2-3 paragraphs describing the challenge, business impact, and solution]

## Course Goal
[Clear statement of what students will achieve]

## Major Changes in this Release
[Version-specific updates if applicable]

## Target Audience
- [Role 1]: [What they care about]
- [Role 2]: [What they care about]

## Prerequisite Skills/Knowledge
- [Prerequisite 1]
- [Prerequisite 2]

## Performance Objectives (POs)
- **[PO 1]** [Measurable outcome 1]
- **[PO 2]** [Measurable outcome 2]
- ... (4-6 total)

## Products/Technologies
- [Product/Technology 1]
- [Product/Technology 2]

## Progressive Diagram Build Strategy
[Description of how diagrams will evolve through course]

---

## HIGH LEVEL DESIGN (HLD)

### Module 1: [Title]
**Module Goal**: [What students achieve]
**Key Sections**: [1.1, 1.2, 1.3, etc.]
**Learning Path**: [Presentation → Lab → Assessment]

### Module 2: [Title]
...

## Integration with rhoai3-skills Toolkit
[Table showing how modules map to create-rhoai-module sections]

---

## Success Metrics
[How to measure if course achieves goals]
```

### Quality Standards

Generated documents include:

- ✅ **Problem Statement**: Clear, specific, shows business impact
- ✅ **Course Goal**: Specific outcomes, not vague learning
- ✅ **Performance Objectives**: 4-6 measurable, outcomes-focused statements
- ✅ **Target Audience**: 2-4 roles with their perspectives
- ✅ **Module Structure**: 5-6 modules with clear progression
- ✅ **Labs Throughout**: Hands-on exercises identified at key points
- ✅ **Business Value**: Cost, efficiency, governance themes woven throughout
- ✅ **Toolkit Integration**: Clear mapping to create-rhoai-module

---

## Skill Workflow

```
INPUT:
  - course_topic
  - problem_statement
  - target_roles
  - Optional: num_modules, duration, domains, capstone_style, etc.

PROCESSING:
  1. Validate inputs
     - Course topic is specific (not too vague)
     - Problem statement shows business impact
     - Target roles are realistic
  
  2. Infer course structure
     - Estimate duration → number of modules
     - Problem domain → primary_technology
     - Roles → audience focus areas
  
  3. Generate components
     
     A. Header Sections
     - SME placeholders
     - Problem statement (Shadow X vs Factory X pattern)
     - Course goal aligned to problem
     - Target audience with role-specific context
     - Prerequisites mapped to roles
     
     B. Performance Objectives (4-6 POs)
     - PO 1: Conceptual understanding
     - PO 2: Strategic/design thinking
     - PO 3: Implementation skill
     - PO 4: Advanced technique
     - PO 5: Integration/operations (if applicable)
     - PO 6: Real-world application (capstone)
     
     C. Module Structure (5-6 modules)
     - Module 1: Foundations & business case
     - Module 2: Architecture & design
     - Module 3-4: Implementation details
     - Module 5: Operations & optimization
     - Module 6 (optional): Capstone project
     
     Each module includes:
     - Clear goal
     - 3-4 sections with learning objectives
     - Lab recommendations
     - Progressive diagram descriptions
     - Connection to POs
     
     D. Integration Guide
     - Table mapping modules to create-rhoai-module usage
     - Suggested section types (presentation/lab/quiz)
     - Business scenarios to use
     
     E. Success Metrics
     - What learners will be able to do
     - How organization benefits

  4. Verify quality
     - Check problem statement shows business impact
     - Verify POs are measurable
     - Ensure labs are distributed throughout
     - Confirm progression from basics → advanced
     - Validate toolkit integration is clear

OUTPUT:
  - Markdown file ready for course creation
  - Complete structure for create-rhoai-course or create-rhoai-module
  - Integration guide for maximum toolkit usage
```

---

## Integration with Other Skills

### With create-rhoai-course

After generating the design document, use create-rhoai-course:

```bash
# First: Generate design with this skill
/create-rhoai-design-document --course-topic "..." --problem-statement "..."

# Second: Use design to generate course structure
/create-rhoai-course my-design-document.md --output-dir rhoai3-mycourse

# Third: Fill in each section with create-rhoai-module
/create-rhoai-module modules/ch1-intro/pages/s1-presentation.adoc presentation \
  --learning-objective "Understand the problem and business value" \
  --business-scenario [from design]
```

### With create-rhoai-module

The design document tells create-rhoai-module exactly what to generate:

```bash
# Design specifies Module 1 has 3 sections:
# 1.1 Presentation on business value
# 1.2 Lab on cost calculation  
# 1.3 Quiz on ROI concepts

# So you would invoke:
/create-rhoai-module .../s1-presentation.adoc presentation \
  --learning-objective "Understand business value and ROI" \
  --previous-module [if applicable]

/create-rhoai-module .../s2-lab.adoc lab \
  --learning-objective "Calculate costs for your use case" \
  --include-troubleshooting true

/create-rhoai-module .../s3-quiz.adoc quiz \
  --learning-objective "Test understanding of ROI framework"
```

### With verify-rhoai-content

Use design document as reference when validating:

```bash
# Verify that modules follow the design
/verify-rhoai-content modules/ --strict

# Each section should score 85+ and match design intent
```

---

## Examples

### Example 1: Fine-Tuning Domain-Specific Models

```bash
/create-rhoai-design-document \
  --course-topic "Fine-Tuning Language Models for Domain-Specific Tasks" \
  --problem-statement "Organizations struggle to adapt generic models for specific domains (finance, healthcare, legal). Without a platform approach, fine-tuning remains a black box—no governance, no cost tracking, compliance risks." \
  --target-roles "Platform Engineers" "ML Engineers" "DevOps/SREs" \
  --estimated-duration "8-12 hours" \
  --domain-examples "Financial Analysis" "Medical Diagnosis" "Legal Document Review" \
  --include-cost-analysis true
```

**Generates**: 6-module course covering economics, strategy, data prep, execution, evaluation, and production deployment of fine-tuned models.

---

### Example 2: GPU Resource Optimization

```bash
/create-rhoai-design-document \
  --course-topic "GPU Optimization: From Silos to Shared Services" \
  --problem-statement "Expensive GPU infrastructure is trapped in silos, creating idle capacity in some teams while others queue indefinitely. GPU-as-a-Service solves this through fair scheduling and governance." \
  --target-roles "Platform Engineers" "DevOps" \
  --estimated-duration "6-8 hours" \
  --primary-technology "GPU Resource Management"
```

**Generates**: 5-module course on GPU architecture, fair scheduling, cost allocation, and operational management.

---

### Example 3: Data Pipeline Governance

```bash
/create-rhoai-design-document \
  --course-topic "Building Governed AI Data Pipelines on OpenShift" \
  --problem-statement "Data pipelines for AI lack governance—no audit trails, no access control, compliance violations. A platform approach brings governance and observability." \
  --target-roles "Platform Engineers" "Architects" \
  --num-modules 4 \
  --estimated-duration "4-6 hours"
```

**Generates**: 4-module focused course on data pipeline architecture, governance patterns, and operational best practices.

---

## Quality Checklist

Before finalizing, the skill verifies:

- [ ] **Problem Statement**: Clear business impact (cost, compliance, efficiency, risk)
- [ ] **Course Goal**: Specific outcomes, not vague learning
- [ ] **Target Audience**: 2-4 roles with role-specific context
- [ ] **Performance Objectives**: 4-6 measurable, outcomes-focused
- [ ] **Module Structure**: 5-6 modules with clear progression
- [ ] **Labs Throughout**: Exercises distributed, not clustered
- [ ] **Business Value**: Woven throughout, not just in intro
- [ ] **Toolkit Integration**: Clear mapping to create-rhoai-module
- [ ] **Progression**: Basics → advanced, concepts → practice
- [ ] **Success Metrics**: Learnable and measurable

---

## Tips for Best Results

### ✅ DO

- **Be specific**: "Fine-tuning for financial documents" not "ML models"
- **Show business impact**: "Reduces processing time from 2 hours to 10 minutes" not "faster processing"
- **Map to real roles**: Platform engineers care about cost/operations, ML engineers care about accuracy
- **Include domain examples**: Makes course memorable and relevant
- **Plan labs systematically**: One per major concept, progressive complexity

### ❌ DON'T

- **Be too vague**: "Advanced AI Topics" is too broad
- **Skip business context**: Technical details alone don't motivate learners
- **Over-design**: 8-10 modules for a 6-hour course creates cognitive overload
- **Forget prerequisites**: Assume learners have some background
- **Ignore cost/ROI**: RHOAI courses are pragmatic—include the numbers

---

## Common Customizations

### For Compliance-Heavy Domains (Healthcare, Finance)

Add sections on:
- Data lineage tracking
- Audit logging and access controls
- Regulatory requirements (HIPAA, GDPR, SOX)
- Compliance validation

### For Cost-Conscious Organizations

Emphasize:
- Cost calculation in every module
- ROI comparison frameworks
- Resource optimization strategies
- Spot instance usage, right-sizing

### For Rapid Implementation

Shorter course (4-6 modules focusing on:
- Quick wins (which features deliver value fastest)
- MVP deployment (minimum viable platform)
- Day-2 optimization (learning while operating)

### For Research/Academic Audience

Include:
- Research references and citations
- Theoretical foundations
- Advanced concepts and cutting-edge techniques
- Experimental approaches

---

## FAQ

**Q: How much detail should the design document include?**

A: Enough for someone (you or a colleague) to implement the course using create-rhoai-module. 2-3 sentence descriptions of each section are typical.

**Q: Can I have more/fewer than 5-6 modules?**

A: Yes. 4 modules for quick courses (4-6 hours), 8-10 for comprehensive courses (20-30 hours). Default is 5-6.

**Q: Should I write the design before or after the course?**

A: Before. The design is your roadmap. It guides what to create. Designing first prevents wasted effort.

**Q: Can I combine multiple domains in one course?**

A: Yes. Many courses cover foundation (same for all) then domain-specific labs. Example: GPU optimization applies to finance, healthcare, manufacturing.

**Q: What if I don't know all the details?**

A: The design document is a starting point. Complete what you know, use placeholders for what you'll fill in later (TBD).

**Q: How is this different from a lesson plan?**

A: Design document is higher-level (course structure, modules). Lesson plan is detailed (day-by-day, hour-by-hour). Design comes first, then lesson plans for each module.

---

## Related Skills

- **create-rhoai-course** — Generate course structure from design
- **create-rhoai-module** — Generate individual sections using this design
- **verify-rhoai-content** — Validate that created content matches design

---

## Version History

- **1.0** (2026-04-06) — Initial release
  - Generates complete design documents following rhoai3-maas format
  - Optimized for rhoai3-skills toolkit usage
  - Includes domain-specific examples and cost analysis
  - Integration guide for create-rhoai-module

---

## Contact & Support

Questions about this skill?
- Check `examples/design-documents/` for reference designs
- Review `GETTING-STARTED.md` for workflow guidance
- Reference `rhoai3-maas/designdocmaas.md` for the proven format

---

*Skill Definition: create-rhoai-design-document v1.0*
*Created: 2026-04-06*
*Status: Ready for Implementation*
