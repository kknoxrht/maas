# Skill: create-rhoai-module

**Purpose**: Generate individual RHOAI course sections (presentation, lab, or quiz) with full RHOAI styling, business narrative, and proper AsciiDoc formatting.

**Category**: Content Generation  
**Status**: Active  
**Version**: 1.0

---

## Quick Reference

| Input | Example |
|-------|---------|
| Section type | `presentation`, `lab`, or `quiz` |
| Learning objective | `Deploy a fine-tuned model to production MaaS` |
| Business scenario | `ACME AI - Compliance Audit` (optional) |
| File path | `modules/ch2-serving/pages/s3-deployment-lab.adoc` |

| Output | Description |
|--------|-------------|
| AsciiDoc file | Complete section ready to build with Antora |
| Updated nav.adoc | Navigation file updated with section reference |
| Validation report | RHOAI style score and any issues found |

---

## What This Skill Does

The **create-rhoai-module** skill generates complete, production-ready course sections that:

1. **Match RHOAI Style** — Bold italic lead openers, definition lists, "What's Next" sections
2. **Include Business Narrative** — Connect technical concepts to cost, efficiency, governance
3. **Follow Structure** — Business Value → Architecture → Implementation → Validation pattern
4. **Generate Proper AsciiDoc** — Correct formatting, code blocks, admonitions
5. **Optimize for Learning** — Clear objectives, practical examples, verification steps
6. **Integrate with Course** — Update navigation, maintain consistency with adjacent sections

---

## Skill Parameters

### Required Parameters

**`section_type`** (string)
- Value: `presentation`, `lab`, or `quiz`
- Determines template and content structure
- `presentation` = lecture/conceptual content
- `lab` = hands-on exercises with commands/YAML
- `quiz` = knowledge check with scenario-based questions

**`learning_objective`** (string)
- What learners will accomplish/understand
- Should be specific and measurable
- Example: "Deploy a fine-tuned model to production with governance controls"
- Bad example: "Learn about deployment"

**`file_path`** (string)
- Target location for generated file
- Format: `modules/ch{N}-topic/pages/s{N}-descriptive-name.adoc`
- The skill will create parent directories if needed
- Example: `modules/ch2-serving/pages/s3-deployment-lab.adoc`

### Optional Parameters

**`business_scenario`** (string, optional)
- Which ACME AI Corp scenario to reference (or custom)
- Values: `storage`, `gpu-sharing`, `cost-shock`, `compliance`, `coordination`, `versioning`
- If not specified, skill will infer from learning objective
- Example: `compliance` → Uses ACME's audit scenario

**`previous_module`** (string, optional)
- Path to previous section for narrative continuity
- Skill reads previous section to maintain business context
- Helps ensure consistent company/scenario naming
- Example: `modules/ch2-serving/pages/s2-architecture.adoc`

**`reference_urls`** (array, optional)
- External documentation URLs to incorporate
- Skill will fetch and extract key concepts
- Use for staying current with product documentation
- Example: `["https://docs.openshift.com/..."]`

**`audience_role`** (string, optional)
- Focus perspective: `platform-engineer`, `ml-engineer`, `sre`
- Adjusts examples and terminology
- Default: `platform-engineer` (OpenShift AI focus)

**`include_troubleshooting`** (boolean, optional)
- For labs: add troubleshooting section
- Default: `true` for labs, `false` for presentations
- Includes 2-3 common failure modes and solutions

**`time_estimate`** (integer, optional)
- Minutes to complete section
- Default: `15` for presentations, `45` for labs, `10` for quizzes
- Skill uses this in the `:time_estimate:` attribute

---

## How to Use This Skill

### Basic Usage

```bash
/create-rhoai-module \
  modules/ch2-deployment/pages/s2-lab.adoc \
  lab \
  --learning-objective "Deploy a fine-tuned model to production"
```

This generates a lab section with:
- ✅ RHOAI styling (lead opener, definition lists)
- ✅ Business context (inferred from objective)
- ✅ Numbered exercises with code blocks
- ✅ Verification steps (expected output)
- ✅ Troubleshooting section
- ✅ Learning outcomes checklist
- ✅ "What's Next" transition

### With Business Scenario

```bash
/create-rhoai-module \
  modules/ch2-deployment/pages/s2-lab.adoc \
  lab \
  --learning-objective "Deploy a fine-tuned model with approval workflows" \
  --business-scenario compliance
```

This adds ACME's compliance audit context, grounding the lab in real business requirements.

### With Previous Module for Continuity

```bash
/create-rhoai-module \
  modules/ch2-serving/pages/s3-advanced-lab.adoc \
  lab \
  --learning-objective "Implement rate limiting and quotas" \
  --previous-module modules/ch2-serving/pages/s2-architecture.adoc
```

Skill reads the previous section, maintains narrative consistency, references concepts from the architecture section.

### Presentation Section

```bash
/create-rhoai-module \
  modules/ch2-serving/pages/s1-architecture.adoc \
  presentation \
  --learning-objective "Understand shared vs isolated model serving infrastructure" \
  --business-scenario gpu-sharing
```

Generates a presentation with:
- ✅ Lead opener
- ✅ Business context (GPU sharing challenges)
- ✅ Architecture explanation
- ✅ Trade-off analysis
- ✅ Key concepts with business impact
- ✅ Common mistakes section
- ✅ "What's Next" connection

### Quiz Section

```bash
/create-rhoai-module \
  modules/ch2-serving/pages/s4-quiz.adoc \
  quiz \
  --learning-objective "Test understanding of model serving trade-offs"
```

Generates a knowledge check with:
- ✅ 5 scenario-based questions
- ✅ Multiple choice answers
- ✅ Explanations for correct answers
- ✅ Scoring guide
- ✅ Review areas if learner struggles

---

## Output Specification

### File Structure

Generated file includes:

```asciidoc
:time_estimate: 45          ← From optional parameter or default

= Section Title

_Estimated reading time: *{time_estimate} minutes*._

[.lead]
*Bold, italic, declarative statement matching RHOAI signature.*

[Business narrative paragraph(s) connecting to business value]

Objective::
Learner outcome description.

== Section Content
[Content matches the structure pattern for section type]

== What's Next

[Business-focused transition to next topic]

////
Section notes: [Technical details, design decisions]
////
```

### Quality Standards

All generated sections meet these criteria:

- ✅ **RHOAI Style Score**: Minimum 85/100 (GOOD or STRONG)
- ✅ **Lead Opener**: Present, bold italic, declarative
- ✅ **Business Value**: Explicit connection within first paragraph
- ✅ **Definition Lists**: Used for objectives and key concepts
- ✅ **Proper AsciiDoc**: Valid syntax, correct source types
- ✅ **What's Next**: Business-focused transition, not generic preview
- ✅ **Code Blocks**: Proper formatting, expected output shown
- ✅ **No Prohibited Terms**: "robust", "leverage", "synergy" avoided

### Navigation Update

If updating an existing chapter's nav.adoc, the skill will:
1. Read `modules/chN-topic/nav.adoc`
2. Add reference to the new section
3. Maintain sequential ordering
4. Update xref targets

---

## Skill Workflow

```
INPUT:
  - section_type (presentation/lab/quiz)
  - learning_objective
  - file_path
  - Optional: business_scenario, previous_module, etc.

PROCESSING:
  1. Validate inputs
     - Check file_path format
     - Verify section_type is valid
     - Ensure learning_objective is specific
  
  2. Load context
     - If previous_module: read to extract narrative context
     - If business_scenario: load ACME scenario from examples/
     - If reference_urls: fetch and extract key concepts
  
  3. Generate content by section type
     
     For PRESENTATION:
     - Generate lead opener (declarative statement)
     - Write business narrative (1-2 paragraphs)
     - Outline major topics (2-4 sections)
     - Add key concepts with business context
     - Include common mistakes section
     - Generate What's Next
     
     For LAB:
     - Generate lead opener (what will be accomplished)
     - Write business context (why this matters)
     - List prerequisites and setup
     - Create 2-4 numbered tasks
     - Add verification steps after each major task
     - If include_troubleshooting: add common failures + solutions
     - Add learning outcomes checklist
     - Generate What's Next
     
     For QUIZ:
     - Generate 5 scenario-based questions
     - Each with business context
     - Multiple choice answers (3-4 options)
     - Explanations for correct answers
     - Scoring guide and review areas
     - Generate What's Next
  
  4. Format AsciiDoc
     - Add document attributes (:time_estimate:, etc.)
     - Convert to proper AsciiDoc syntax
     - Ensure code blocks have correct source types
     - Add section comments for maintainers
  
  5. Update navigation
     - Read target chapter's nav.adoc
     - Add xref to new section
     - Maintain ordering
     - Write updated nav.adoc
  
  6. Validate output
     - Check against RHOAI style guide
     - Verify required elements present
     - Run verify-rhoai-content score
     - Report score and any issues

OUTPUT:
  - New AsciiDoc file written to file_path
  - Updated nav.adoc written
  - Validation report with score
  - Summary of what was created
  - Next steps for the user
```

---

## Integration with Other Skills

### With verify-rhoai-content

After generation, you can immediately validate:

```bash
/create-rhoai-module modules/ch2/pages/s2-lab.adoc lab ...
# Generates section

/verify-rhoai-content modules/ch2/pages/s2-lab.adoc
# Score should be >= 85 (GOOD or STRONG)
```

### With create-rhoai-course

For new courses:

```bash
/create-rhoai-course my-course-design.md
# Generates structure with section stubs

/create-rhoai-module modules/ch1-foundation/pages/s1-presentation.adoc presentation ...
# Fill in each section
```

### With harmonize-style

To check consistency of generated content:

```bash
/harmonize-style modules/ch2/pages/s2-lab.adoc --preview-only
# Should return "NO ACTION NEEDED" (variance < 30)
```

---

## Examples

### Example 1: Create a Presentation on GPU Sizing

```bash
/create-rhoai-module \
  modules/ch2-optimization/pages/s1-gpu-sizing.adoc \
  presentation \
  --learning-objective "Calculate GPU memory requirements for model serving" \
  --business-scenario gpu-sharing \
  --time-estimate 20
```

**Generates**: Presentation explaining GPU sizing with ACME's GPU sharing crisis context, trade-offs, cost implications.

### Example 2: Create a Lab on Model Registry Access Control

```bash
/create-rhoai-module \
  modules/ch1-governance/pages/s2-registry-rbac.adoc \
  lab \
  --learning-objective "Implement RBAC policies for model registry access" \
  --business-scenario compliance \
  --previous-module modules/ch1-governance/pages/s1-governance-architecture.adoc \
  --include-troubleshooting true
```

**Generates**: Lab with 3-4 exercises, verification steps, troubleshooting section. References previous section on governance architecture for continuity.

### Example 3: Create a Quiz on Cost Trade-Offs

```bash
/create-rhoai-module \
  modules/ch2-optimization/pages/s3-cost-quiz.adoc \
  quiz \
  --learning-objective "Evaluate cost-accuracy trade-offs in model selection"
```

**Generates**: 5-question quiz with scenario-based questions on GPU selection, batch sizing, resource allocation. Each question grounded in business decision-making.

---

## Error Handling

### Invalid Inputs

**Error**: `learning_objective` too vague
```
❌ "Learn about deployment"
✅ "Deploy a fine-tuned model to production with governance controls"
```
**Solution**: Reword objective to be specific and measurable.

**Error**: `file_path` format incorrect
```
❌ modules/s1-section.adoc
✅ modules/ch1-topic/pages/s1-descriptive.adoc
```
**Solution**: Follow naming convention: `modules/chN-keyword/pages/sN-descriptive.adoc`

**Error**: `section_type` not recognized
```
❌ /create-rhoai-module ... content
✅ /create-rhoai-module ... presentation
```
**Solution**: Use `presentation`, `lab`, or `quiz`.

### Network Errors (with reference_urls)

If URLs can't be fetched, skill will:
1. Log a warning
2. Continue without those references
3. Note in output what was skipped
4. Suggest verifying documentation manually

---

## Quality Assurance

Every generated section passes:

1. **AsciiDoc Validation** — Valid syntax, builds without errors
2. **RHOAI Style Check** — Runs verify-rhoai-content, scores >= 85
3. **Content Review** — Objective met, structure correct, examples realistic
4. **Navigation Consistency** — nav.adoc properly updated

Automated checks catch issues; human review (by course creator) ensures content quality.

---

## Tips for Best Results

### ✅ DO

- **Be specific** with learning objectives
  - ✅ "Deploy a model to MaaS with request throttling"
  - ❌ "Deploy a model"

- **Use previous_module** for continuity
  - Maintains narrative consistency
  - References concepts from earlier sections

- **Include business_scenario** when relevant
  - Makes content grounded and memorable
  - Connects to real challenges organizations face

- **Set time_estimate** appropriately
  - Presentations: 15-20 min
  - Labs: 30-60 min (include setup, commands, verification, troubleshooting)
  - Quizzes: 10-15 min

### ❌ DON'T

- **Don't use vague objectives**
  - ❌ "Understand models"
  - ✅ "Calculate inference cost for concurrent user workload"

- **Don't skip required parameters**
  - All three (section_type, learning_objective, file_path) are required

- **Don't assume generated content is final**
  - Review output for accuracy (especially code examples)
  - Verify against your specific product versions
  - Test commands if they're in your labs

- **Don't ignore the validation report**
  - If score < 85, review the issues
  - Most can be fixed easily

---

## FAQ

**Q: Can I edit the generated content?**

A: Yes! The generated content is a starting point. Edit for:
- Specific product versions (commands may need adjustment)
- Your organization's specific architecture
- Domain-specific examples
- Local terminology or company-specific scenarios

**Q: What if my business scenario isn't in ACME examples?**

A: Provide `business_scenario` as a free-form description instead of a preset value. The skill will adapt.

**Q: Can I use this for non-RHOAI content?**

A: The skill is tuned for RHOAI (OpenShift AI, models, inference, cost). For other domains, use the style guide and templates as reference, but the business narrative patterns may differ.

**Q: How long does generation take?**

A: Typically 30-60 seconds depending on:
- Section type (quiz is fastest, lab can be longer due to multiple exercises)
- Whether reference URLs are being fetched
- File I/O operations

**Q: Can I generate multiple sections at once?**

A: Currently, one section per invocation. For a chapter with 3-4 sections, run the skill multiple times. Parallelization is possible in future versions.

---

## Related Skills

- **verify-rhoai-content** — Quality check generated sections
- **create-rhoai-course** — Generate full course structure (which you then populate with create-rhoai-module)
- **harmonize-style** — Ensure consistency across content

---

## Version History

- **1.0** (2026-04-06) — Initial release
  - Support for presentation, lab, quiz sections
  - RHOAI style pattern application
  - Business scenario integration
  - Navigation auto-update

---

## Contact & Support

Questions about this skill?
- Check examples in `examples/sample-course-designs/`
- Reference `style-guide/RHOAI-STYLE-GUIDE.md`
- Review `examples/business-scenarios/ACME-AI-Corp.md`
- Check the integration examples above

---

*Skill Definition: create-rhoai-module v1.0*  
*Created: 2026-04-06*  
*Status: Ready for Implementation*
