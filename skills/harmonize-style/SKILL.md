# Skill: harmonize-style

**Purpose**: Analyze existing content for RHOAI style alignment; recommend rewriting only when variance is high AND impact is significant. Conservative approach: ~40% of content gets "NO ACTION NEEDED" recommendation.

**Category**: Content Analysis & Optional Rewriting  
**Status**: Active  
**Version**: 1.0

---

## Quick Reference

| Input | Example |
|-------|---------|
| Content path | `modules/ch1-basics/pages/s1-intro.adoc` or `modules/` |
| Analysis mode | Default (analyze), `--preview-only` (show without acting) |
| Rewrite action | `--rewrite` (only after user sees analysis) |

| Output | Description |
|--------|-------------|
| Analysis report | Variance score, impact assessment, recommendation |
| Before/after preview | How content would change (if rewrite suggested) |
| Decision matrix | Why this recommendation was made |

---

## What This Skill Does

The **harmonize-style** skill provides conservative style analysis:

1. **Analyzes Existing Content** — Measures deviation from RHOAI patterns
2. **Assesses Business Impact** — Whether the variance matters
3. **Makes Recommendation** — NO ACTION / OPTIONAL / CONSIDER / RECOMMENDED
4. **Previews Changes** — Shows what rewriting would do (without modifying)
5. **Respects Effective Content** — Doesn't force rewrites for ~40% of content

**Key Principle**: This skill recommends NO ACTION for approximately 40% of content, preserving effective alternative approaches and respecting author voice where it's already working well.

---

## Skill Parameters

### Required Parameters

**`content_path`** (string)
- Path to file or directory
- File: Single AsciiDoc file (e.g., `modules/ch1/pages/s1.adoc`)
- Directory: All files recursively (e.g., `modules/ch1/`)
- Example: `modules/ch1-foundation/pages/s1-intro.adoc`

### Optional Parameters

**`--preview-only`** (boolean, optional)
- Analyze and show findings without offering to rewrite
- Default: false (after analysis, skill offers to rewrite if recommended)
- Use for: Deciding whether changes are worth doing
- Example: `--preview-only` → Shows analysis, asks for confirmation before rewrite

**`--rewrite`** (boolean, optional)
- Apply recommended rewriting changes
- Only works AFTER user has seen analysis and agreed
- Default: false (analysis only)
- Use for: Applying improvements after decision made
- Example: `--rewrite` → Modifies files based on recommendations

**`--threshold`** (integer, optional)
- Variance threshold (0-100) for recommendations
- Default: 60 (recommend rewrite if variance > 60 AND impact > 50)
- Lower threshold = more aggressive recommendations
- Example: `--threshold 40` → Recommend more rewrites (lower bar)

---

## How to Use This Skill

### Basic Usage - Analyze Single File

```bash
/harmonize-style modules/ch1-foundation/pages/s1-intro.adoc
```

Returns analysis with recommendation:

**Output Example (Strong Content)**:
```
✓ NO ACTION NEEDED

Variance: 15/100 (excellent match)
Impact: 80/100 (high-importance section)

Summary:
  This content already matches RHOAI style exceptionally well.
  No changes recommended.

Strengths:
  ✓ Excellent lead opener: "GPU sharing requires fairness..."
  ✓ Strong business narrative: Specific cost impact mentioned
  ✓ Clear What's Next: Explains capability enabled
  ✓ Proper use of definition lists

Next steps: This section is ready for publication.
```

### Analyze with Preview Before Decision

```bash
/harmonize-style modules/ch2-serving/pages/s2-lab.adoc --preview-only
```

Shows analysis and before/after preview (WITHOUT modifying files):

**Output Example (Rewrite Recommended)**:
```
⚠ REWRITE RECOMMENDED

Variance: 72/100 (significant deviation)
Impact: 65/100 (important section)

Decision: High variance in high-impact section.
          Harmonization recommended for consistency.

PREVIEW: How this section would change
─────────────────────────────────────────────────────

CURRENT (Lead Opener):
  [.lead]
  *This lab shows how to deploy models.*

PROPOSED (Lead Opener):
  [.lead]
  *Production deployment requires approval gates, not ClickOps.*

───

CURRENT (Business Narrative):
  In this lab, you'll learn to deploy models using kubectl.

PROPOSED (Business Narrative):
  A developer deploys with kubectl. A platform engineer ensures
  the deployment follows approval gates, leaves audit trails, and
  integrates with cost tracking. This lab shows how.

───

CURRENT (What's Next):
  In the next section, you will learn about monitoring.

PROPOSED (What's Next):
  Now that your model is deployed safely with proper governance,
  the next section shows how to monitor its accuracy in real time—
  catching drift before it impacts users.

───

Impact of changes:
  ✓ Lead opener gains authority and specificity
  ✓ Business narrative shifts to platform perspective
  ✓ What's Next explains capability, not just content order
  ✓ Overall score would improve from 68 → 85 (GOOD)

Ready to apply?
  Option A: /harmonize-style ... --rewrite  (apply these changes)
  Option B: Keep original content (no changes)
  Option C: Manually edit specific sections
```

### Apply Rewriting After Preview

Once user has seen preview and decided to proceed:

```bash
/harmonize-style modules/ch2-serving/pages/s2-lab.adoc --rewrite
```

Applies the recommended changes:

**Output**:
```
✓ Rewriting complete

modules/ch2-serving/pages/s2-lab.adoc
  ✓ Updated lead opener
  ✓ Strengthened business narrative
  ✓ Rewrote What's Next section
  ✓ 3 changes applied

Score improved: 68 → 85 (GOOD)

Next: 
  - Review the changes: open the file in your editor
  - Run /verify-rhoai-content to validate
  - Commit the changes: git add, git commit
```

### Batch Analysis - Entire Course

```bash
/harmonize-style modules/ --preview-only
```

Analyzes all sections, categorizes by recommendation:

**Output**:
```
Course-Wide Analysis: rhoai3-mycourse

SUMMARY BY RECOMMENDATION:
───────────────────────────

✓ NO ACTION NEEDED: 7 sections (47%)
  Excellent style match. No changes recommended.
  - modules/ch1/pages/s1.adoc (94/100)
  - modules/ch1/pages/s2.adoc (92/100)
  - modules/ch2/pages/s1.adoc (90/100)
  [...]

⚠ OPTIONAL IMPROVEMENTS: 5 sections (33%)
  Minor variance detected. Changes optional.
  - modules/ch2/pages/s3.adoc (58/100, impact 45/100)
  - modules/ch3/pages/s1.adoc (62/100, impact 48/100)
  [...]

→ CONSIDER IMPROVEMENTS: 2 sections (13%)
  Variance present, but low impact.
  - modules/ch4/pages/s1.adoc (70/100, impact 35/100)
  - modules/ch4/pages/s2.adoc (65/100, impact 40/100)

✗ REWRITE RECOMMENDED: 1 section (7%)
  High variance in high-impact section.
  - modules/ch1/pages/s3.adoc (78/100, impact 72/100)

NEXT STEPS:

1. For "NO ACTION NEEDED" sections:
   ✓ These are ready for publication. Keep as-is.

2. For "OPTIONAL" sections:
   ⚠ Review individually with --preview-only
   ⚠ Update only if time permits and improvements are valuable

3. For "CONSIDER" sections:
   → These are lower priority. Focus on "RECOMMENDED" first.

4. For "REWRITE RECOMMENDED" section:
   ✗ Worth updating. Use --preview-only first, then --rewrite.

Action: Review priority sections and decide on updates.
```

---

## The Decision Algorithm

### Step 1: Calculate Style Variance (0-100)

**Variance Components** (each weighted):

```
Lead Opener (25%):
  ✓ Present and strong (bold italic, declarative): 0 points
  ⚠ Present but weak (vague, generic): 12 points
  ✗ Missing or very weak: 25 points

Business Narrative (30%):
  ✓ Clear platform perspective with specificity: 0 points
  ⚠ Business mentioned but weak: 15 points
  ✗ Missing or no business context: 30 points

Structure (20%):
  ✓ Business→Architecture→Implementation flow with definition lists: 0 points
  ⚠ Mostly structured but missing some elements: 10 points
  ✗ No clear structure: 20 points

What's Next (15%):
  ✓ Business-focused, explains capability enabled: 0 points
  ⚠ Present but generic (content preview): 8 points
  ✗ Missing or very generic: 15 points

Technical Authority (10%):
  ✓ Specific numbers, formulas, exact commands: 0 points
  ⚠ Some generality, could be more specific: 5 points
  ✗ Vague, no specific details: 10 points

Total Variance = sum of all components (0-100)
```

### Step 2: Calculate Business Impact (0-100)

**Content Type** (base score):
```
Chapter index/intro:      80  (sets tone for whole course)
First section (ch1):      75  (learner's entry point)
Mid-course presentation:  50  (good to strong, not critical)
Lab section:              40  (technical content > style)
Quiz/assessment:          30  (content correctness > style)
Reference material:       20  (style matters less)
```

**Position Modifiers** (added to base):
```
Architecture/strategy section: +20  (shapes thinking)
Intro/overview content:        +20  (frames experience)
First 3 sections (user journey): +15 (most visibility)
Mid-course sections:            +5   (context already set)
Final review sections:          -10  (less impact)
```

**Formula**:
```
Impact = Base Score + Position Modifiers
         (capped at 0-100)
```

### Step 3: Apply Decision Matrix

```
Variance < 30 REGARDLESS OF IMPACT:
  └─ Decision: NO ACTION NEEDED
     Reason: Excellent style match already
     Action: Keep content as-is

30 ≤ Variance < 60 AND Impact < 50:
  └─ Decision: OPTIONAL IMPROVEMENTS
     Reason: Minor variance, low impact
     Action: Changes optional; recommend if time available

30 ≤ Variance < 60 AND Impact ≥ 50:
  └─ Decision: CONSIDER IMPROVEMENTS
     Reason: Low-moderate variance, meaningful section
     Action: Worth considering if polishing course

Variance ≥ 60 AND Impact < 50:
  └─ Decision: CONSIDER IMPROVEMENTS
     Reason: High variance but low business impact
     Action: Lower priority; focus elsewhere first

Variance ≥ 60 AND Impact ≥ 50:
  └─ Decision: REWRITE RECOMMENDED
     Reason: High variance in high-impact section
     Action: Worth improving for consistency

Variance ≥ 80 OR Requires Manual Review:
  └─ Decision: ESCALATE TO HUMAN
     Reason: Unclear recommendation
     Action: Show preview, let user decide
```

### Examples of Decision Logic

**Example 1: Well-styled mid-chapter presentation**
```
Variance: 18 (excellent match)
Impact: 55 (important section)
Decision: NO ACTION NEEDED
Reason: Excellent style already; no change needed
```

**Example 2: Weak quiz section**
```
Variance: 75 (high variance)
Impact: 30 (quiz = low style impact)
Decision: CONSIDER IMPROVEMENTS
Reason: Variance present but quiz prioritizes content over style
```

**Example 3: Weak first lab (critical entry point)**
```
Variance: 72 (high variance)
Impact: 75 (first section = high impact)
Decision: REWRITE RECOMMENDED
Reason: High variance in high-visibility section
```

**Example 4: Optional improvements**
```
Variance: 45 (moderate variance)
Impact: 40 (mid-course, lower impact)
Decision: OPTIONAL IMPROVEMENTS
Reason: Nice to fix if time available, not critical
```

---

## Rewriting Strategy

When variance is high, the skill rewrites **only** these elements:

### Always Rewritten
- **Lead opener** (if weak/missing)
- **Business narrative** (if missing platform perspective)
- **"What's Next" section** (if too generic)

### Never Rewritten (Human-controlled)
- **Code blocks** (preserve author's examples/commands)
- **Detailed technical explanations** (preserve accuracy)
- **Specific examples or scenarios** (preserve domain expertise)
- **Troubleshooting steps** (must be tested, verified)

### Conditionally Rewritten
- **Definition lists** (converted from bullets if needed)
- **Capitalization & formatting** (grammar corrections)
- **Prohibited terms** (replaced with specific language)

**Principle**: Preserve technical content; harmonize style elements.

---

## Output Specification

### Analysis Report Structure

```
[RECOMMENDATION]

Variance: X/100 ([Description])
Impact: Y/100 ([Description])

Summary:
  [1-2 sentence explanation of recommendation]

Current State:
  [What works well]
  [What needs improvement]

Proposed Changes:
  [If rewrite recommended]
  [Before/after examples of key changes]

Score Impact:
  Current score: X/100 ([Readiness level])
  Potential score: Y/100 ([After rewrite])

Next Steps:
  [Action based on recommendation]
```

### Recommendation Levels

```
✓ NO ACTION NEEDED
  → Content excellent; keep as-is
  → Ready for publication
  → No changes recommended

⚠ OPTIONAL IMPROVEMENTS
  → Minor variance detected
  → Changes optional for consistency
  → Worth doing if polishing course
  → Effort: 30-60 minutes

→ CONSIDER IMPROVEMENTS
  → Variance present; low impact
  → Worth considering if already working on section
  → Lower priority; focus on "RECOMMENDED" first
  → Effort: 1-2 hours if chosen

✗ REWRITE RECOMMENDED
  → High variance in high-impact section
  → Worth improving for user experience
  → Recommend prioritizing this update
  → Effort: 1-2 hours to implement
```

---

## Integration with Other Skills

### With verify-rhoai-content

Compare before/after:

```bash
# 1. Analyze current state
/harmonize-style modules/ch1/pages/s1.adoc --preview-only

# 2. If rewriting, apply changes
/harmonize-style modules/ch1/pages/s1.adoc --rewrite

# 3. Validate improvement
/verify-rhoai-content modules/ch1/pages/s1.adoc
# Score should improve from (e.g.) 68 → 85
```

### With create-rhoai-module

harmonize-style helps update older content while create-rhoai-module generates new sections:

```bash
# Old content: analyze for updates
/harmonize-style modules/ch2-old/ --preview-only

# New content: use skill to generate
/create-rhoai-module modules/ch2-new/pages/s4-fresh.adoc lab ...

# Both pass validation
/verify-rhoai-content modules/ch2/
```

---

## Examples

### Example 1: Excellent Content (No Action Needed)

```bash
/harmonize-style modules/ch1-foundation/pages/s1-gpu-basics.adoc
```

**Output**:
```
✓ NO ACTION NEEDED

Variance: 8/100 (excellent match)
Impact: 80/100 (high-importance opening section)

Summary:
  This section is already exceptionally well-aligned with RHOAI style.
  No changes are recommended.

Strengths:
  ✓ Lead opener is declarative and authoritative
    "GPU scheduling requires fairness, not hope."
  
  ✓ Business narrative frames platform engineer perspective
    "A developer requests a GPU. A platform engineer allocates
     fairly across teams and tracks costs. This section shows how."
  
  ✓ What's Next explains capability enabled
    "Now that you understand the scheduling problem, the next
     section shows how to implement fair allocation with Kubernetes."
  
  ✓ Definition lists used for objectives and concepts
  
  ✓ Structure flows: Business Value → Architecture → Implementation

Score: 94/100 (STRONG)

Next steps:
  This section is publication-ready.
  No changes needed. Move on to other content.
```

### Example 2: Good Content with Optional Improvements

```bash
/harmonize-style modules/ch3-operations/pages/s2-monitoring-lab.adoc --preview-only
```

**Output**:
```
⚠ OPTIONAL IMPROVEMENTS

Variance: 48/100 (minor-to-moderate variance)
Impact: 42/100 (mid-course lab, lower business impact)

Summary:
  This lab section has minor style differences from RHOAI patterns.
  Updates are optional—focus on higher-priority sections first.

Current Issues:
  ⚠ Lead opener is present but generic
    Current: "This lab teaches monitoring"
    Suggested: "Monitoring catches drift before users do"
  
  ⚠ Business narrative focuses on technical steps, not platform value
    Current: "Steps to set up monitoring dashboards"
    Suggested: Add: "A platform engineer ensures monitoring is
                    built-in, not bolted-on"
  
  ⚠ What's Next just previews next topic
    Current: "The next section covers alerting"
    Suggested: Explain what's enabled: "Now that you monitor
               accuracy, the next section shows..."

Strengths:
  ✓ Code examples are verified and working
  ✓ Verification steps present with expected output
  ✓ Structure is logically organized
  ✓ Technical accuracy is excellent

Recommendation:
  These changes are NOT critical. Consider updating if:
  - You're already editing this section for other reasons
  - You're polishing the course for publication
  - You have time in your schedule

Score Impact:
  Current: 72/100 (ADEQUATE)
  If updated: 84/100 (GOOD)

Next steps:
  Option A: Keep as-is (content is functional)
  Option B: Review with --preview-only to see specific changes
  Option C: Apply updates with --rewrite when time permits
```

### Example 3: High-Variance First Section (Recommended)

```bash
/harmonize-style modules/ch1-foundation/pages/s1-intro.adoc --preview-only
```

**Output**:
```
✗ REWRITE RECOMMENDED

Variance: 78/100 (high variance)
Impact: 78/100 (opening section, high visibility)

Summary:
  This opening section has significant style variance and is the
  entry point for learners. Improvement recommended.

Current Issues:

1. Lead Opener is vague
   Current: "This section introduces fine-tuning"
   Proposed: "Fine-tuning is where generic becomes specific."
   Impact: ← Establishes authority vs explains

2. Business narrative missing platform perspective
   Current: "Fine-tuning is the process of adapting models..."
   Proposed: "A data scientist fine-tunes models ad hoc on
             personal hardware. A platform engineer provides
             governed, auditable fine-tuning infrastructure.
             Here's why that matters and how to build it."
   Impact: Shifts from feature description to platform value

3. What's Next is too generic
   Current: "The next section covers data preparation"
   Proposed: "Now that you understand the economics, the next
             section shows how to prepare quality training data—
             the biggest cost driver in fine-tuning projects."
   Impact: Explains business value of next topic

4. Missing business narrative numbers
   Current: [No cost context]
   Proposed: Add: "Fine-tuning costs roughly $[X] per GPU-hour
             for a 7B model."
   Impact: Grounds discussion in reality

Strengths:
  ✓ Content is accurate and complete
  ✓ Logical structure and flow
  ✓ Code examples (if present) are verified
  ✓ Good foundation for the skill

PREVIEW: What would change
─────────────────────────────────────

[Before/after comparison of key sections]

Score Impact:
  Current: 62/100 (ADEQUATE)
  If updated: 88/100 (GOOD)

Recommendation:
  This is the learner's entry point. Improving this section will:
  ✓ Set the right tone for the course
  ✓ Frame the business value clearly
  ✓ Improve overall course experience

Time estimate: 1-2 hours

Next steps:
  Option A: /harmonize-style ... --rewrite (apply changes)
  Option B: /harmonize-style ... --preview-only (review again)
  Option C: Manual edit (keep control of specific changes)
```

---

## Conservative Recommendations (Key Principle)

This skill is designed to be **conservative** and **respectful** of existing work:

- **40% of content** gets "NO ACTION NEEDED" — effective content is left alone
- **33% get OPTIONAL** — improvements suggested but not pushed
- **13% get CONSIDER** — lower priority alternatives
- **14% get RECOMMENDED** — only high-variance, high-impact sections

**Why this balance?**

1. **Not all effective style is RHOAI style** — Alternative voices can work well
2. **Respect author expertise** — They may know their domain best
3. **Technical accuracy > style** — Content that works should be preserved
4. **Gradual improvement > forced consistency** — Better to suggest than mandate

---

## FAQ

**Q: Will this change my code examples?**

A: No. Code blocks are never rewritten. Only style elements (lead opener, business narrative, What's Next) are changed.

**Q: What if I don't want my content changed?**

A: The skill only rewrites if you explicitly run `--rewrite`. Use `--preview-only` to see proposed changes first. You control all decisions.

**Q: How does this differ from verify-rhoai-content?**

A: 
- **verify-rhoai-content** rates content and explains what needs improvement (scoring tool)
- **harmonize-style** analyzes variance and OPTIONALLY rewrites (improvement tool)

Use both:
1. Check score with verify-rhoai-content
2. Analyze recommendations with harmonize-style
3. Apply improvements if agreed

**Q: Should I update all content?**

A: No. Focus on high-impact sections (first 3, chapter intros, strategy sections). Lower-priority content (labs, quizzes, reference) can keep alternative styles.

**Q: Can I apply this to my entire course at once?**

A: Yes: `harmonize-style modules/ --preview-only` to see all recommendations first. Then decide section-by-section.

**Q: Will rewriting break my code or confuse learners?**

A: No. Only narrative elements change. Code examples, step-by-step instructions, and technical explanations are preserved exactly.

---

## Tips for Best Results

### ✅ DO

- **Start with --preview-only** — See proposed changes before committing
- **Focus on high-impact sections** — Chapter intros and first sections matter most
- **Review before/after examples** — Understand what changes before applying
- **Test after rewriting** — Run verify-rhoai-content to validate improvements
- **Commit changes separately** — Git commit style updates separately from content changes

### ❌ DON'T

- **Rewrite without reviewing first** — Always preview before applying
- **Force updates on low-impact sections** — Quizzes, labs can stay as-is
- **Assume rewriting improves technical accuracy** — Only style elements change
- **Update code examples** — These should match your testing environment
- **Ignore the conservative recommendations** — If it says "NO ACTION", trust it

---

## Related Skills

- **verify-rhoai-content** — Quality check content; use to measure before/after
- **create-rhoai-module** — Generate new sections with perfect style
- **create-rhoai-course** — Start new course (which harmonize-style can later improve)

---

## Version History

- **1.0** (2026-04-06) — Initial release
  - Conservative variance and impact scoring
  - Decision matrix with 4 recommendation levels
  - Preview-first workflow (no unwanted changes)
  - Selective rewriting (preserves technical content)
  - Batch analysis for entire courses
  - ~40% of content gets "NO ACTION NEEDED" recommendation

---

## Contact & Support

Questions about this skill?
- Check `style-guide/RHOAI-STYLE-GUIDE.md` for patterns
- Review examples of "NO ACTION NEEDED" to understand ideal style
- See GETTING-STARTED.md for when to use harmonize-style vs. create-rhoai-module
- Reference rhoai3-* courses for style examples

---

*Skill Definition: harmonize-style v1.0*  
*Created: 2026-04-06*  
*Status: Ready for Implementation*
