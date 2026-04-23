# Skill: verify-rhoai-content

**Purpose**: Validate RHOAI course content against quality standards, providing actionable feedback and readiness assessment.

**Category**: Quality Assurance  
**Status**: Active  
**Version**: 1.0

---

## Quick Reference

| Input | Example |
|-------|---------|
| Content path | `modules/ch2-serving/pages/s3-lab.adoc` or `modules/` (directory) |
| Validation mode | Default, `--strict`, `--auto-fix`, `--report-format` |
| Report format | `terminal` (default), `markdown`, `json` |

| Output | Description |
|--------|-------------|
| Validation report | Scores, issues, readiness assessment |
| Issue list | Line numbers, specific feedback, severity |
| Readiness level | STRONG (90-100), GOOD (75-89), ADEQUATE (60-74), WEAK (<60) |
| Suggestions | Actionable improvements with examples |

---

## What This Skill Does

The **verify-rhoai-content** skill validates course sections and provides:

1. **RHOAI Style Scoring** — 0-100 scale measuring adherence to the 13 signatures
2. **Structure Validation** — Ensures required elements are present and properly formatted
3. **Technical Accuracy Check** — Validates code blocks, product names, command syntax
4. **Readiness Assessment** — Determines if content is ready for publication
5. **Actionable Feedback** — Specific line numbers, before/after examples
6. **Batch Validation** — Analyze entire courses for consistency
7. **Auto-Fix Capability** — Automatically correct simple formatting issues (optional)

---

## Skill Parameters

### Required Parameters

**`content_path`** (string)
- Path to file or directory to validate
- File: Single AsciiDoc file (e.g., `modules/ch2/pages/s2-lab.adoc`)
- Directory: All AsciiDoc files recursively (e.g., `modules/`)
- Example: `modules/ch1-foundation/pages/s1-presentation.adoc`

### Optional Parameters

**`--strict`** (boolean, optional)
- Fail on warnings (not just errors)
- Default: false (warnings are suggestions, not blocking)
- Use for: Pre-publication checks, course consistency validation
- Example: `--strict` → warnings become errors

**`--auto-fix`** (boolean, optional)
- Automatically fix simple issues (indentation, capitalization, spacing)
- Default: false (report only, don't modify)
- Does NOT fix: Business value, lead openers, code examples (requires human judgment)
- Example: `--auto-fix` → fixes obvious formatting issues

**`--report-format`** (string, optional)
- Output format: `terminal`, `markdown`, `json`
- Default: `terminal` (human-readable console output)
- `markdown`: GitHub-flavored markdown for issues, embeddable in PRs
- `json`: Machine-readable format for CI/CD integration
- Example: `--report-format json` → CI/CD friendly output

**`--focus`** (string, optional)
- Focus on specific category: `structure`, `style`, `technical`, `accessibility`
- Default: all categories
- Useful for targeted checks
- Example: `--focus style` → only RHOAI style checks

---

## How to Use This Skill

### Basic Usage - Single File

```bash
/verify-rhoai-content \
  modules/ch2-serving/pages/s2-architecture.adoc
```

Returns a readiness assessment with overall score and suggestions.

**Output Example**:
```
✓ STRONG (92/100) - Ready for publication

Lead Opener: ✓ Excellent
Business Value: ✓ Strong
Structure: ✓ Correct
Definition Lists: ✓ Present
What's Next: ✓ Business-focused

Suggestions (0):
  None—this content meets all standards.
```

### Strict Validation - Pre-Publication

```bash
/verify-rhoai-content \
  modules/ch2-serving/pages/s2-architecture.adoc \
  --strict
```

Warnings become blockers; must achieve GOOD (75+) or STRONG (90+) to pass.

**Output Example**:
```
✗ ADEQUATE (68/100) - Revisions required

Lead Opener: ✓ Present
Business Value: ⚠ Weak connection to cost
Structure: ✓ Correct
Definition Lists: ✗ Missing on key concepts
What's Next: ⚠ Too generic

Issues (3):
  Line 12: Business value paragraph lacks specific cost numbers
  Line 45: Key concepts should use definition list (::) syntax
  Line 67: What's Next explains content order, not business enablement
```

### Auto-Fix Simple Issues

```bash
/verify-rhoai-content \
  modules/ch1-foundation/ \
  --auto-fix
```

Automatically fixes indentation, capitalization, spacing, and other formatting issues. Human review still required for content quality.

**Output Example**:
```
Fixed: 5 issues
  - s1-intro.adoc: Fixed title capitalization (3 issues)
  - s2-lab.adoc: Fixed code block indentation (2 issues)

Review required: 8 issues
  - s1-intro.adoc: Weak lead opener (line 8)
  - s3-quiz.adoc: Missing verification steps (line 34)
  [...]
```

### Batch Validation - Entire Course

```bash
/verify-rhoai-content \
  modules/ \
  --report-format markdown \
  --strict
```

Validates all sections, identifies inconsistencies, provides summary.

**Output Example**:
```markdown
# Validation Report: rhoai3-mycourse

## Summary
- Total sections: 15
- STRONG (90-100): 8
- GOOD (75-89): 5
- ADEQUATE (60-74): 2
- WEAK (<60): 0

## By Chapter
### Chapter 1: Foundations
- s1-intro.adoc: STRONG (94)
- s2-lab.adoc: GOOD (82)
- s3-quiz.adoc: STRONG (90)

### Chapter 2: Operations
- s1-arch.adoc: GOOD (78)
- s2-lab.adoc: ADEQUATE (68) ⚠️ Needs revision
- s3-deploy.adoc: GOOD (81)

## Issues by Category
[Detailed issue list with line numbers]

## Recommendations
- Chapter 2, s2: Strengthen business narrative (2-3 hours)
- Overall: Consistent use of definition lists (+5 consistency score)
```

### Focused Validation - Style Only

```bash
/verify-rhoai-content \
  modules/ch1/pages/s1-presentation.adoc \
  --focus style
```

Check only RHOAI stylistic compliance (ignores technical accuracy, structure).

---

## Output Specification

### Report Structure

Generated reports include:

```
OVERALL SCORE: X/100 (READINESS LEVEL)

[Summary line]

DETAILED BREAKDOWN:
  Category 1 (weight): Score/100 ✓ or ✗
  Category 2 (weight): Score/100
  ...

ISSUES [N]:
  [Line N]: [Issue description]
           [Context: 1-2 sentence explanation]
           [Suggestion: How to fix]
           [Severity: ERROR | WARNING | SUGGESTION]

SUMMARY:
  [What's good]
  [What needs work]
  [Estimated effort to fix]

NEXT STEPS:
  [Specific actions to achieve GOOD/STRONG score]
```

### Scoring Breakdown

```
Overall Score = (Structure × 0.25) + (RHOAI_Style × 0.35) + 
                (Style_Guide × 0.20) + (Technical × 0.15) + 
                (Accessibility × 0.05)

RHOAI_Style = (Lead_Opener × 0.30) + (Business_Narrative × 0.30) + 
               (Definition_Lists × 0.20) + (Whats_Next × 0.20)

Readiness Levels:
- STRONG (90-100): Ready for publication
- GOOD (75-89): Minor revisions (1-2 hours)
- ADEQUATE (60-74): Significant improvements needed (2-4 hours)
- WEAK (<60): Major revisions required (4+ hours)
```

### Issue Severity Levels

- **ERROR**: Blocks publication (missing critical element)
- **WARNING**: Should fix before publishing (quality issue)
- **SUGGESTION**: Optional improvement (nice-to-have)

---

## Skill Workflow

```
INPUT:
  - content_path (file or directory)
  - Optional: --strict, --auto-fix, --report-format, --focus

PROCESSING:
  1. Parse content
     - Load AsciiDoc file(s)
     - Extract document structure (attributes, headings, sections)
     - Parse code blocks and verify syntax type
  
  2. Validate structure (25%)
     - Check for required attributes (:time_estimate:, etc.)
     - Verify title present
     - Check lead paragraph [.lead] present
     - Verify objective:: definition
     - Check "What's Next" section
     - Verify proper heading hierarchy
  
  3. Validate RHOAI style (35%)
     A. Lead Opener Quality (30% of this subscore)
        - Present: [.lead] block exists
        - Format: Bold italic (*...*) 
        - Content: Declarative, not vague or question
        - Authority: Establishes engineering credibility
        - Uniqueness: Different from section title
     
     B. Business Narrative (30%)
        - Present: Within first paragraph
        - Timing: Before implementation details
        - Specificity: Mentions concrete business concern
        - Numbers: Includes specific values where relevant
        - Platform perspective: From platform engineer POV
        - Scenario integration: If provided, fits naturally
     
     C. Definition Lists (20%)
        - Used for objective::
        - Used for key concepts where appropriate
        - Consistent throughout
        - Proper :: syntax
     
     D. What's Next (20%)
        - Present: Dedicated section
        - Not generic: Explains what's enabled, not just preview
        - Business focused: Connects to outcomes
        - Logical flow: Transitions to next topic appropriately
  
  4. Validate style guide (20%)
     - No prohibited terms (robust, leverage, synergy, etc.)
     - Sentence case headlines
     - Proper capitalization and grammar
     - Inclusive language
     - Oxford commas
     - Product name correctness (Red Hat OpenShift AI, RHOAI)
  
  5. Validate technical accuracy (15%)
     - Code block source types correct (bash, yaml, python, etc.)
     - Product versions specified
     - Commands are valid for specified environment
     - No misleading claims
     - For labs: Verification steps present
     - Expected output shown (not just commands)
  
  6. Validate accessibility (5%)
     - Images have alt text
     - Links are descriptive
     - Heading hierarchy correct (no skips)
     - Content structure clear for screen readers
  
  7. Generate report
     - Calculate scores for each category
     - Identify issues with line numbers
     - Generate contextual suggestions
     - Assess readiness level
     - Output in requested format

OUTPUT:
  - Validation report (terminal/markdown/json)
  - Readiness level (STRONG/GOOD/ADEQUATE/WEAK)
  - Issue list with line numbers and severity
  - Actionable suggestions
  - Estimated effort to fix
  - Next steps for improvement
```

---

## Integration with Other Skills

### With create-rhoai-module

After generating a section:

```bash
# Generate section
/create-rhoai-module modules/ch2/pages/s2-lab.adoc lab \
  --learning-objective "Deploy a model to production"

# Validate immediately
/verify-rhoai-content modules/ch2/pages/s2-lab.adoc
# Target: 85+ (GOOD or STRONG)

# If score < 85, review suggestions and edit
# Then re-validate
/verify-rhoai-content modules/ch2/pages/s2-lab.adoc
```

### With create-rhoai-course

Validate entire course after generation:

```bash
# Generate course
/create-rhoai-course course-design.md --output-dir rhoai3-mycourse

# Fill in sections with create-rhoai-module...

# Batch validate entire course
/verify-rhoai-content rhoai3-mycourse/modules/ --strict
# All sections should score 75+
```

### With CI/CD Pipelines

Export validation data for automated checks:

```bash
/verify-rhoai-content modules/ \
  --report-format json \
  --strict \
  > validation-report.json

# In CI script:
# Fail if any section < 75, warn if < 85
```

---

## Examples

### Example 1: Validate a Presentation Section

```bash
/verify-rhoai-content \
  modules/ch1-foundation/pages/s1-intro.adoc
```

**Output**:
```
✓ STRONG (94/100) - Ready for publication

Lead Opener: ✓ Excellent (28/30)
  "GPU sharing requires fairness, not scarcity."
  Strong engineering authority, declarative tone

Business Value: ✓ Strong (29/30)
  Clear platform perspective
  Specific numbers ($50K/month GPU cost)
  Connects to governance theme

Definition Lists: ✓ Present (20/20)
  3 key objectives properly formatted

What's Next: ✓ Excellent (20/20)
  "Now that you understand the problem, the next section shows
   how quota management prevents this crisis."
  Business connection, logical flow

Structure: ✓ Correct (24/25)
  All required attributes present
  Proper heading hierarchy

Style Guide: ✓ Clean (19/20)
  No prohibited terms
  Proper capitalization
  One capitalization note: "API" vs "API" inconsistent (line 34)

Technical: ✓ Accurate (15/15)
  Product names correct (OpenShift AI 3.3)
  No outdated references

Accessibility: ✓ Good (5/5)
  Headings properly hierarchical
  Links descriptive

SUGGESTIONS (2):
  1. Line 34: Standardize "API" usage (currently "api" in one place)
     → Change line 34 from "manage the api" to "manage the API"
     Severity: SUGGESTION (does not block publication)

  2. Line 67: Consider adding a specific example
     → "For example, a financial services team with 4 A100 GPUs..."
     Severity: SUGGESTION (improves clarity)

NEXT STEPS:
  This section is publication-ready. Optional improvements above
  would boost score to 96+. No action required.
```

### Example 2: Validate a Lab Section (Needs Revision)

```bash
/verify-rhoai-content \
  modules/ch2-serving/pages/s2-lab.adoc \
  --strict
```

**Output**:
```
✗ ADEQUATE (68/100) - Revisions required

Lead Opener: ✓ Good (26/30)
  Present and declarative
  Could be more specific to learning outcome

Business Value: ⚠ Weak (18/30)
  Lacks specific cost numbers
  Doesn't frame from platform engineer perspective
  Missing "why this matters for operations"

Definition Lists: ✗ Missing (8/20)
  Prerequisites listed as bullets, should use definition list
  Lab steps using numbers, not definitions for key concepts

What's Next: ⚠ Generic (14/20)
  "In the next section..." is content preview, not business connection
  Doesn't explain what's enabled by completing this lab

Structure: ✓ Correct (23/25)
  All required sections present
  Proper heading hierarchy

Style Guide: ✓ Clean (18/20)
  One prohibited term: "leverage" (line 45)
  Capitalization issues: "GPU" vs "gpu" inconsistent

Technical: ⚠ Incomplete (10/15)
  Verification steps present but sparse
  Expected output not shown for all commands
  Commands should be tested (assumption: not verified)

Accessibility: ✓ Good (5/5)

ISSUES (6):
  1. Line 8: Lead opener is generic
     Current: "This lab shows how to deploy a model"
     Suggested: "Production deployment requires approval gates, not ClickOps."
     Severity: WARNING

  2. Line 12: Business value paragraph lacks platform perspective
     Add: "A platform engineer must..."
     Add: Specific cost (e.g., "$500/month per deployment")
     Severity: WARNING

  3. Line 34: Prerequisites should use definition list syntax
     Current: "- You need kubectl"
     Suggested: "Prerequisites:: kubectl 1.24+, OpenShift 4.12+"
     Severity: WARNING

  4. Line 45: Prohibited term "leverage"
     Current: "...leverage the approval system..."
     Suggested: "...use the approval system..."
     Severity: SUGGESTION

  5. Line 78: Verification step missing expected output
     Current: "$ oc get llminferenceservice"
     Suggested: Add expected output showing service ready
     Severity: ERROR

  6. Line 92: What's Next is too generic
     Current: "The next section covers monitoring."
     Suggested: "Now that you've deployed safely, the next section shows
                 how to monitor costs and catch errors before users do."
     Severity: WARNING

ESTIMATED EFFORT: 2-3 hours
  - Strengthen business narrative: 30 min
  - Fix definition lists: 30 min
  - Add verification steps and expected output: 1 hour
  - Test all commands: 30 min
  - Fix terminology: 15 min

NEXT STEPS:
  1. Rewrite business value paragraph (lines 12-15)
  2. Convert prerequisites to definition list (line 34)
  3. Add expected output to verification steps (line 78)
  4. Strengthen What's Next section (line 92)
  5. Re-validate after edits

TIPS:
  - See examples/presentation.adoc for strong business narrative
  - See style-guide/examples/whats-next-sections.adoc for pattern
  - Check rhoai3-maas labs for verification step examples
```

### Example 3: Batch Course Validation

```bash
/verify-rhoai-content \
  modules/ \
  --report-format markdown \
  --strict > VALIDATION-REPORT.md
```

**Output** (excerpt):
```markdown
# RHOAI Content Validation Report

**Generated**: 2026-04-06  
**Repository**: rhoai3-mycourse  
**Scope**: All modules (modules/)  
**Mode**: Strict (warnings = blockers)

## Summary

| Category | Score | Status |
|----------|-------|--------|
| Overall | 79/100 | GOOD |
| STRONG | 8 sections (47%) | ✓ |
| GOOD | 6 sections (35%) | ⚠ |
| ADEQUATE | 3 sections (18%) | ✗ |
| WEAK | 0 sections | ✓ |

**Recommendation**: Course is publishable but needs 2-3 sections improved for consistency.

## By Chapter

### Chapter 1: Foundations (3 sections)
- ✓ s1-intro.adoc: 94 STRONG
- ✓ s2-lab.adoc: 88 GOOD
- ✓ s3-quiz.adoc: 90 STRONG

### Chapter 2: Data Preparation (4 sections)
- ✓ s1-principles.adoc: 92 STRONG
- ⚠ s2-collection-lab.adoc: 71 ADEQUATE
- ✓ s3-quality.adoc: 86 GOOD
- ✓ s4-quiz.adoc: 89 GOOD

### Chapter 3: Execution (3 sections)
- ✓ s1-architectures.adoc: 88 GOOD
- ⚠ s2-single-gpu-lab.adoc: 68 ADEQUATE
- ✓ s3-multi-gpu-lab.adoc: 84 GOOD

### Chapter 4: Evaluation (3 sections)
- ✓ s1-metrics.adoc: 93 STRONG
- ⚠ s2-comparison-lab.adoc: 72 ADEQUATE
- ✓ s3-quiz.adoc: 87 GOOD

### Chapter 5: Operations (2 sections)
- ✓ s1-deployment.adoc: 91 STRONG
- ✓ s2-quiz.adoc: 88 GOOD

## Priority Fixes

**Estimated effort**: 6 hours total

### P1 - Chapter 2, s2 (68 ADEQUATE)
**Issue**: Weak business narrative, missing verification steps  
**Effort**: 2 hours  
**Impact**: Critical path (learners must complete)

### P2 - Chapter 3, s2 (68 ADEQUATE)
**Issue**: Generic What's Next, incomplete code examples  
**Effort**: 1.5 hours

### P3 - Chapter 4, s2 (72 ADEQUATE)
**Issue**: Definition lists missing, verification steps sparse  
**Effort**: 1.5 hours

## Style Consistency Notes

- **Definition list usage**: Inconsistent. Chapters 1-2 strong, Chapters 3-4 weak.
  Recommendation: Run `--auto-fix` to standardize, then review for content accuracy.

- **What's Next sections**: Generally strong, but Chapter 3 tends toward content preview
  rather than business enablement. See style-guide/examples/whats-next-sections.adoc.

- **Verification steps**: Labs in Chapter 2-3 lack expected output. Add examples from
  rhoai3-maas modules for consistency.

- **Lead opener quality**: Presentations excellent (92+ average), labs adequate (75+ average).
  Consider strengthening lab lead openers to match presentation quality.

## Full Issues List

[Detailed line-by-line issues for each section]
```

---

## Quality Standards

Content validated as **STRONG (90+)** meets these criteria:

- ✅ Strong lead opener (bold italic, declarative, engineering authority)
- ✅ Clear business value (platform perspective, specific numbers)
- ✅ Definition lists for structure
- ✅ Business-focused "What's Next"
- ✅ Proper AsciiDoc formatting
- ✅ Verified code examples (for labs)
- ✅ No prohibited terms
- ✅ Proper capitalization and grammar

Content validated as **GOOD (75-89)** has minor issues fixable in 1-2 hours:

- Minor business narrative weakness
- Definition lists mostly present
- Generic What's Next that needs strengthening
- Minor style inconsistencies

Content validated as **ADEQUATE (60-74)** needs significant improvement:

- Weak business connection
- Missing definition lists
- Generic or missing What's Next
- Code examples need verification
- Multiple style issues

Content validated as **WEAK (<60)** requires major revision:

- Little to no business narrative
- Lacks required structure
- Missing critical elements (lead, What's Next)
- Significant technical inaccuracies

---

## Common Issues and Fixes

### Issue: Weak Lead Opener

**Current**:
```asciidoc
[.lead]
*This section covers model deployment.*
```

**Problem**: Vague, not declarative, not engineering authority

**Fixed**:
```asciidoc
[.lead]
*Production deployment requires governance, not just kubectl apply.*
```

**Why**: Establishes the business problem (governance), uses declarative tone, assumes engineering credibility.

### Issue: Business Value Not Connected

**Current**:
```asciidoc
In this section, you will learn how to deploy models to OpenShift AI.
```

**Problem**: Describes activity, not business value or platform perspective

**Fixed**:
```asciidoc
A developer deploys models with a kubectl command. A platform engineer ensures that deployment goes through approval gates, leaving an audit trail, and integrating with rate limiting. This section shows how.
```

**Why**: Contrasts developer vs platform perspective, mentions business concerns (compliance, governance), then states learning goal.

### Issue: Generic What's Next

**Current**:
```asciidoc
== What's Next

In the next section, you will learn about monitoring models in production.
```

**Problem**: Just previews content order, doesn't explain business connection

**Fixed**:
```asciidoc
== What's Next

Now that your model is deployed safely with proper governance, the next section shows how to monitor its accuracy in real time—catching accuracy drift before it impacts users.
```

**Why**: Explains what capability is enabled, connects to business value, motivates next topic.

### Issue: Missing Verification Steps

**Current**:
```asciidoc
[source,bash]
----
$ oc apply -f model-deployment.yaml
----
```

**Problem**: Command shown, but no verification that it worked

**Fixed**:
```asciidoc
[source,bash]
----
$ oc apply -f model-deployment.yaml
service/my-model created
----

Verify the deployment succeeded:

[source,bash]
----
$ oc get llminferenceservice my-model -o jsonpath='{.status.conditions[0].reason}'
ModelReady
----

✓ When you see `ModelReady`, the model is deployed and accepting requests.
```

**Why**: Shows expected output, includes verification command, clarifies success criterion.

---

## Tips for High Scores

### ✅ DO

- **Use specific numbers** — "$50K/month" vs "expensive"
- **Include verification steps** — Show expected output, not just commands
- **Connect to platform perspective** — "A platform engineer must..." framing
- **Use definition lists** — For objectives, prerequisites, key concepts
- **Explain "What's Next"** — What capability is enabled, what problem it solves
- **Test code examples** — Verify all commands work in target environment

### ❌ DON'T

- **Use weak lead openers** — Avoid "This section covers...", "Learn about..."
- **Skip business context** — Technical details alone don't motivate learners
- **Use prohibited terms** — "robust", "leverage", "synergy" hurt the score
- **Assume generic structure** — Business Value → Architecture → Implementation order matters
- **Leave verification steps sparse** — Labs must show proof that tasks worked
- **Write vague What's Next** — Avoid "Next you'll learn...", explain what's enabled

---

## FAQ

**Q: What's the minimum score to publish?**

A: **GOOD (75+)** is publishable. **STRONG (90+)** is ideal. **ADEQUATE (60-74)** needs revision first.

**Q: Can I auto-fix all issues?**

A: No. `--auto-fix` only fixes obvious formatting (indentation, capitalization). Content quality (lead opener, business narrative, code verification) requires human judgment.

**Q: Why is my lab scoring lower than my presentation?**

A: Labs are weighted more heavily for code verification (15% of score). If commands aren't tested or verification steps are sparse, the score drops. Presentations focus on narrative clarity (less code-dependent).

**Q: Can I ignore warnings?**

A: In normal mode, yes—warnings are suggestions. In `--strict` mode, warnings become blockers and must be addressed before publishing.

**Q: How do I improve a score from ADEQUATE to GOOD?**

A: Typically 2-3 focused edits:
1. Strengthen business narrative with specific numbers
2. Add "Why this matters" from platform perspective  
3. Fix What's Next to explain capability, not just preview
4. Add verification steps with expected output (for labs)

Estimated effort: 1-2 hours.

**Q: What if content is technically correct but stylistically inconsistent?**

A: Run `verify-rhoai-content --focus style` to isolate style issues. Business narrative and lead opener are ~65% of the style score—focus there first. Then work on definition lists and What's Next sections.

---

## Related Skills

- **create-rhoai-module** — Generate sections; use verify-rhoai-content immediately after
- **create-rhoai-course** — Generate course structure; validate with verify-rhoai-content before publishing
- **harmonize-style** — Analyze existing content; use verify-rhoai-content to measure before/after

---

## Version History

- **1.0** (2026-04-06) — Initial release
  - Complete RHOAI style validation
  - Structure and technical accuracy checks
  - Readiness assessment scoring
  - Multi-format reporting (terminal/markdown/json)
  - Auto-fix capability for formatting issues
  - Batch validation for entire courses

---

## Contact & Support

Questions about this skill?
- Check `style-guide/RHOAI-STYLE-GUIDE.md` for patterns
- Review `examples/` for reference sections
- See `GETTING-STARTED.md` for workflow integration
- Reference actual rhoai3-* courses for examples

---

*Skill Definition: verify-rhoai-content v1.0*  
*Created: 2026-04-06*  
*Status: Ready for Implementation*
