# Skill: create-rhoai-course

**Purpose**: Generate complete course structure from design document, creating Antora-ready directory scaffold with navigation and section stubs.

**Category**: Course Generation  
**Status**: Active  
**Version**: 1.0

---

## Quick Reference

| Input | Example |
|-------|---------|
| Design document | `my-course-design.md` (markdown with learning objectives) |
| Output directory | `rhoai3-my-course/` (target location) |
| Dry-run mode | `--dry-run` (preview without creating) |

| Output | Description |
|--------|-------------|
| Complete Antora structure | modules/ROOT, chapters, pages |
| Navigation files | nav.adoc for each chapter |
| Section stubs | Empty templates for content |
| antora.yml | Site configuration |
| Summary report | What was created, next steps |

---

## What This Skill Does

The **create-rhoai-course** skill generates complete course scaffolding:

1. **Parses Design Document** — Extracts course metadata, chapters, learning objectives
2. **Creates Directory Structure** — Full Antora module layout (modules/chN-keyword/)
3. **Generates Navigation** — nav.adoc files for each chapter
4. **Creates Section Stubs** — Template pages ready for create-rhoai-module
5. **Configures Antora** — antora.yml with all components
6. **Reports Output** — Summary of what was created and next steps

This turns a design document into a ready-to-fill course scaffold.

---

## Skill Parameters

### Required Parameters

**`design_doc_path`** (string)
- Path to course design document (markdown)
- Must include:
  - Course title (# heading)
  - Course goal
  - Target audience
  - Performance objectives table
  - Module structure with learning objectives
- Example: `course-designs/fine-tuning-design.md`
- See `examples/sample-course-designs/example-course-design.md` for format

**`output_dir`** (string, optional)
- Target directory for generated course
- Will be created if it doesn't exist
- Default: `rhoai3-{course-keyword}/` (derived from course title)
- Example: `--output-dir ~/courses/rhoai3-gpu-optimization`

### Optional Parameters

**`--dry-run`** (boolean, optional)
- Preview what would be created without actually creating it
- Default: false (create files)
- Use for: Validating before committing
- Example: `--dry-run` → Shows structure without file creation

**`--include-examples`** (boolean, optional)
- Copy example sections from rhoai3-maas to illustrate style
- Default: false
- Use for: Learning how sections should look
- Example: `--include-examples` → Adds sample presentation, lab, quiz

**`--with-diagrams`** (boolean, optional)
- Create placeholder files for architecture diagrams
- Default: false
- Creates: images/module-diagrams/ with SVG stubs
- Example: `--with-diagrams` → Adds architecture diagram placeholders

---

## How to Use This Skill

### Basic Usage

```bash
/create-rhoai-course \
  course-designs/my-course-design.md
```

Creates `rhoai3-my-course/` with full Antora structure.

**Output**:
```
Created course structure in rhoai3-my-course/

Generated:
  ✓ modules/ROOT/               (course home)
  ✓ modules/ch1-keyword/        (5 chapters)
  ✓ modules/ch2-keyword/
  ✓ modules/ch3-keyword/
  ✓ modules/ch4-keyword/
  ✓ modules/ch5-keyword/
  ✓ antora.yml                  (site config)
  ✓ package.json                (build tools)
  ✓ .gitignore                  (ignored files)

Ready for content:
  1. cd rhoai3-my-course
  2. npm install
  3. Use /create-rhoai-module to fill in sections
  
Next: Run `npm run build` to verify structure after adding content.
```

### With Custom Output Directory

```bash
/create-rhoai-course \
  course-designs/fine-tuning.md \
  --output-dir ~/courses/my-fine-tuning
```

Creates the course in specified directory.

### Preview Before Creating

```bash
/create-rhoai-course \
  course-designs/gpu-optimization.md \
  --dry-run
```

Shows what would be created without creating files:

**Output**:
```
DRY RUN: Would create the following structure

rhoai3-gpu-optimization/
├── .gitignore
├── antora.yml
├── package.json
├── README.md
├── modules/
│   ├── ROOT/
│   │   ├── nav.adoc
│   │   └── pages/
│   │       └── index.adoc
│   ├── ch1-fundamentals/
│   │   ├── nav.adoc
│   │   └── pages/
│   │       ├── index.adoc
│   │       ├── s1-gpu-basics.adoc
│   │       ├── s2-memory-requirements.adoc
│   │       └── s3-assessment.adoc
│   └── ... (additional chapters)

Total: 1 root module, 5 chapters, 18 pages

No files created. Use --dry-run=false to actually create.
```

### Include Example Sections (Learning Resources)

```bash
/create-rhoai-course \
  course-designs/my-course.md \
  --include-examples
```

Adds example presentations, labs, and quizzes from rhoai3-maas pattern:

**Output**:
```
Created course with example sections showing RHOAI style:

modules/ch1-topic/pages/
  ├── index.adoc
  ├── s1-example-presentation.adoc    (full example)
  ├── s2-example-lab.adoc             (full example)
  └── s3-example-quiz.adoc            (full example)

These show the RHOAI style and can be:
  - Deleted if you prefer to start fresh
  - Used as templates for your own content
  - Referenced for pattern examples

Actual sections still created as stubs (s4-..., s5-..., etc.)
```

### With Architecture Diagram Placeholders

```bash
/create-rhoai-course \
  course-designs/my-course.md \
  --with-diagrams
```

Creates placeholder files for your architecture diagrams:

**Output**:
```
Created diagram placeholders:

images/
  ├── ch1-fundamentals/
  │   ├── architecture-overview.svg
  │   ├── shadow-vs-factory.svg
  │   └── cost-model.svg
  ├── ch2-strategy/
  │   ├── base-model-selection.svg
  │   └── data-flow.svg
  └── ... (one per chapter)

SVG files are empty stubs. Fill in with your diagrams.
Recommend: Excalidraw (free), draw.io, or Lucidchart for creation.
```

---

## Design Document Format

The skill expects a markdown design document with this structure:

```markdown
# Course Title: Your Course Name

## Course Goal
[2-3 sentences describing what learners will accomplish]

## Target Audience
- [Role 1]: [What they care about]
- [Role 2]: [What they care about]

## Problem Statement
[Context for why this course exists, business value]

## Performance Objectives
- **PO 1**: [Measurable outcome]
- **PO 2**: [Measurable outcome]
...

## Module Structure

### Module 1: [Title]
Module goal and context

#### 1.1: [Section Title]
Learning objective and description

#### 1.2: [Section Title]
Learning objective and description

#### 1.3: [Section Title]
Learning objective and description

### Module 2: [Title]
...

[Repeat for all chapters]
```

**See**: `examples/sample-course-designs/example-course-design.md` for complete example.

---

## Output Specification

### Directory Structure Created

```
rhoai3-course-keyword/
├── .git/                      # Git repository initialized
├── .gitignore                 # Standard ignores for Antora projects
├── antora.yml                 # Antora configuration (component)
├── antora-playbook.yml        # Build playbook (if building standalone)
├── package.json               # NPM build tools (node_modules, scripts)
├── README.md                  # Quick start guide
├── modules/
│   ├── ROOT/
│   │   ├── nav.adoc
│   │   ├── pages/
│   │   │   ├── index.adoc     # Course home (with title, goal, POs)
│   │   │   └── [additional pages]
│   │   └── partials/
│   │       └── [shared content]
│   ├── ch1-keyword1/
│   │   ├── nav.adoc
│   │   ├── pages/
│   │   │   ├── index.adoc     # Chapter intro
│   │   │   ├── s1-title.adoc
│   │   │   ├── s2-title.adoc
│   │   │   └── s3-title.adoc
│   │   └── images/
│   │       └── [chapter images]
│   ├── ch2-keyword2/
│   │   └── ...
│   └── [remaining chapters...]
├── images/                    # Course-wide images
│   └── [logo, shared diagrams]
└── docs/
    └── CONTRIBUTING.md        # Contribution guidelines
```

### Section Stub Format

Each generated page includes basic template:

```asciidoc
:time_estimate: 15

= Section Title

_Estimated reading time: *{time_estimate} minutes*._

[.lead]
*[Lead opener - replace with your content]*

TBD: Content to be added.

== What's Next

[Next topic in sequence]

////
Section notes: 
- Learning objective: [from design doc]
- Built with create-rhoai-module skill
////
```

This stub can be:
- Filled in manually, OR
- Replaced with `create-rhoai-module` output

### Navigation File Format

Each chapter gets a nav.adoc:

```asciidoc
* xref:index.adoc[Chapter Title]
** xref:pages/s1-section-title.adoc[Section 1: Title]
** xref:pages/s2-section-title.adoc[Section 2: Title]
** xref:pages/s3-section-title.adoc[Section 3: Title]
```

---

## Skill Workflow

```
INPUT:
  - design_doc_path (required)
  - output_dir (optional)
  - --dry-run, --include-examples, --with-diagrams (optional flags)

PROCESSING:
  1. Parse design document
     - Extract course title
     - Extract course goal, target audience, problem statement
     - Extract performance objectives
     - Extract module structure (chapters, sections, learning objectives)
     - Validate required sections present
  
  2. Generate course metadata
     - Derive course keyword from title (e.g., "Fine-Tuning" → "fine-tuning")
     - Create course ID (e.g., "rhoai3-fine-tuning")
     - Map chapters to directory names (ch1-keyword1, ch2-keyword2, etc.)
     - Assign section numbers based on design structure
  
  3. Create directory structure
     - Root directory (rhoai3-course-keyword/)
     - modules/ROOT/ (course home)
     - modules/ch{N}-keyword/ for each chapter
     - modules/ch{N}-keyword/pages/ for content
     - modules/ch{N}-keyword/images/ for chapter images
     - images/ for course-wide images
  
  4. Generate configuration files
     - antora.yml (component metadata)
     - package.json (build dependencies)
     - .gitignore (standard Antora excludes)
     - README.md (quick start guide)
  
  5. Generate ROOT module
     - index.adoc with course title, goal, target audience
     - nav.adoc with chapter navigation
     - Embedded performance objectives
  
  6. Generate chapter directories
     For each chapter:
     - Create ch{N}-keyword/ directory
     - Create nav.adoc with section list
     - Create index.adoc (chapter intro)
     - Create pages/s{N}-section-name.adoc (one per learning objective)
     - Each page is a stub with time_estimate, lead opener placeholder
  
  7. If --include-examples
     - Copy rhoai3-maas example presentation → s1-example
     - Copy rhoai3-maas example lab → s2-example
     - Copy rhoai3-maas example quiz → s3-example
     - Add note explaining these are examples
  
  8. If --with-diagrams
     - Create images/ch{N}-diagrams/ for each chapter
     - Create SVG stub files with TODO comments
     - Map to learning objectives (e.g., "architecture-overview.svg" for architecture PO)
  
  9. Initialize Git
     - git init
     - git add .
     - git commit -m "Initial course scaffold from design document"
  
  10. Generate summary report
      - List created directories and files
      - Next steps for user
      - Integration instructions for create-rhoai-module

OUTPUT:
  - Complete course directory structure
  - All configuration files
  - Navigation stubs
  - Content page stubs
  - Git repository initialized
  - Summary report with next steps
```

---

## Integration with Other Skills

### With create-rhoai-module

After course generation, populate sections:

```bash
# 1. Generate course structure
/create-rhoai-course course-design.md --output-dir rhoai3-mycourse

# 2. Install dependencies
cd rhoai3-mycourse && npm install

# 3. Fill in each section using create-rhoai-module
/create-rhoai-module \
  modules/ch1-foundations/pages/s1-intro.adoc \
  presentation \
  --learning-objective "Understand fine-tuning vs pre-training"

/create-rhoai-module \
  modules/ch1-foundations/pages/s2-setup.adoc \
  lab \
  --learning-objective "Set up your development environment"

# 4. Verify each section
/verify-rhoai-content modules/ch1-foundations/pages/s1-intro.adoc
```

### With verify-rhoai-content

Validate structure and content:

```bash
# After generating course (before adding content)
/verify-rhoai-content modules/ --focus structure
# Checks that all nav.adoc and directory structure are correct

# After adding sections
/verify-rhoai-content modules/ --strict
# All sections should score GOOD (75+) or STRONG (90+)
```

### Workflow

**Complete workflow for new course**:

```
1. Design Document Creation
   └─ Write course-design.md with learning objectives

2. Course Generation
   └─ /create-rhoai-course course-design.md

3. Section Content Creation (per chapter)
   ├─ /create-rhoai-module ... presentation
   ├─ /create-rhoai-module ... lab
   ├─ /create-rhoai-module ... quiz
   └─ /verify-rhoai-content (after each)

4. Course Validation
   └─ /verify-rhoai-content modules/ --strict

5. Build & Preview
   └─ npm run build && npm run serve

6. Publication
   └─ git push origin main
```

---

## Examples

### Example 1: Simple 3-Chapter Course

```bash
/create-rhoai-course course-designs/simple-course.md
```

**Input Design** (excerpt):
```markdown
# Storage Governance on OpenShift AI

## Course Goal
Learn to set up model storage with proper governance and access control.

## Module Structure

### Module 1: Storage Fundamentals
#### 1.1: Why Storage Matters
#### 1.2: Storage Architecture
#### 1.3: Assessment

### Module 2: Governance
#### 2.1: Access Control
#### 2.2: Audit Trails
#### 2.3: Assessment

### Module 3: Operations
#### 3.1: Monitoring
#### 3.2: Troubleshooting
#### 3.3: Assessment
```

**Output Structure**:
```
rhoai3-storage-governance/
├── modules/
│   ├── ROOT/
│   ├── ch1-fundamentals/
│   │   ├── pages/s1-why-storage.adoc
│   │   ├── pages/s2-architecture.adoc
│   │   └── pages/s3-assessment.adoc
│   ├── ch2-governance/
│   │   ├── pages/s1-access-control.adoc
│   │   ├── pages/s2-audit-trails.adoc
│   │   └── pages/s3-assessment.adoc
│   └── ch3-operations/
│       ├── pages/s1-monitoring.adoc
│       ├── pages/s2-troubleshooting.adoc
│       └── pages/s3-assessment.adoc
```

### Example 2: With Example Sections

```bash
/create-rhoai-course course-designs/my-course.md --include-examples
```

Generates all stubs PLUS adds three example sections showing style:

```
modules/ch1-topic/pages/
  ├── index.adoc                      # Chapter intro
  ├── s1-example-presentation.adoc    # Full example (from rhoai3-maas)
  ├── s2-example-lab.adoc             # Full example (from rhoai3-maas)
  ├── s3-example-quiz.adoc            # Full example (from rhoai3-maas)
  ├── s4-your-content-1.adoc          # Your stubs
  ├── s5-your-content-2.adoc
  └── s6-your-content-3.adoc
```

Users can:
- Delete the example sections and use stubs
- Keep examples as templates
- Reference them while filling in stubs

### Example 3: With Diagrams

```bash
/create-rhoai-course course-designs/gpu-ops.md --with-diagrams
```

Creates placeholder SVG files:

```
images/
  ├── ch1-fundamentals/
  │   ├── gpu-architecture.svg
  │   ├── cost-model-chart.svg
  │   └── problem-statement.svg
  ├── ch2-configuration/
  │   ├── node-setup.svg
  │   └── resource-allocation.svg
  └── ch3-monitoring/
      ├── metrics-dashboard.svg
      └── alerts-workflow.svg
```

Each SVG has a TODO comment:
```xml
<!-- TODO: Add diagram for [learning objective] -->
<!-- Recommended tools: Excalidraw, draw.io, Lucidchart -->
<!-- Save as SVG (scalable, version control friendly) -->
```

---

## Quality Standards

Generated courses should:

- ✅ Have complete Antora structure (builds without errors)
- ✅ Have proper navigation (nav.adoc links are valid)
- ✅ Have section stubs with basic template
- ✅ Have git repository initialized
- ✅ Have package.json with build tools
- ✅ Have README.md with quick start
- ✅ Have all chapters and sections from design document

---

## Common Customizations

### Add Antora Playbook (for standalone builds)

The skill generates antora.yml (component). If you want to build this course standalone, add an antora-playbook.yml:

```yaml
site:
  title: Your Course Title
  start_page: rhoai3-course::index.adoc

content:
  sources:
    - url: .
      branches: HEAD

ui:
  bundle:
    url: https://cdn.jsdelivr.net/npm/@antora/ui-default@3.1.1/dist/ui-default-3.1.1.zip
    snapshot: true

output:
  dir: ./build/site
```

### Add GitHub Actions CI (optional)

Create `.github/workflows/verify.yml` to validate on every PR:

```yaml
name: Verify Course Content

on: [pull_request]

jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm install
      - run: npm run build
      - run: /verify-rhoai-content modules/ --strict
```

---

## Troubleshooting

### Problem: "Design document format not recognized"

**Cause**: Missing required sections (Course Goal, Module Structure)

**Solution**: 
- Check `examples/sample-course-designs/example-course-design.md` for required format
- Ensure all chapters have subsections with learning objectives
- Use markdown headings correctly (#, ##, ###)

### Problem: "Cannot create directories (permission denied)"

**Cause**: Output directory or parent is not writable

**Solution**:
- Use `--dry-run` first to see what would be created
- Check directory permissions
- Try different output directory with write access

### Problem: "Generated course doesn't build"

**Cause**: antora.yml or package.json issue

**Solution**:
1. Check antora.yml syntax (valid YAML, required fields)
2. Run `npm install` to verify dependencies
3. Run `npm run build` to see specific errors
4. Regenerate with `--dry-run` and compare

---

## FAQ

**Q: Can I manually edit the generated course?**

A: Absolutely. The generated structure is a starting point. You can:
- Edit nav.adoc to rearrange sections
- Create new sections manually (alongside generated ones)
- Delete section stubs you don't need
- Add custom configuration to antora.yml

**Q: What if my design document changes after generation?**

A: Regenerate the course. The new structure will overwrite old stubs, but:
- Keep your hand-written content (s4-my-content.adoc) in a different directory first
- Regenerate the course
- Move your content back in

Better: Design thoroughly before generating.

**Q: Can I use this with existing courses?**

A: The skill is designed for new courses. For existing courses:
- Use `create-rhoai-module` to add individual sections
- Use `verify-rhoai-content` to validate
- Use `harmonize-style` to update style

**Q: How do I organize 10+ chapters?**

A: Same way. The skill generates as many chapters as your design document specifies. Consider:
- Part structure (Part 1: Fundamentals, Part 2: Advanced)
- Using antora.yml groups to organize in navigation
- Adding a Part index between chapters (optional)

**Q: Can I share the generated course with others?**

A: Yes. Push the repository to GitHub and share the link. Others can:
- Clone and `npm install`
- Use `create-rhoai-module` to contribute sections
- Submit PRs

It's a full development-ready course scaffold.

---

## Related Skills

- **create-rhoai-module** — Populate sections after course generation
- **verify-rhoai-content** — Validate generated course structure and content
- **create-rhoai-design-document** — Generate design document (step before this skill)

---

## Version History

- **1.0** (2026-04-06) — Initial release
  - Parse design documents to course structure
  - Generate Antora-ready scaffold
  - Create navigation and section stubs
  - Initialize Git repository
  - Support for example sections and diagram placeholders
  - Complete documentation and examples

---

## Contact & Support

Questions about this skill?
- Review `examples/sample-course-designs/` for reference designs
- Check `GETTING-STARTED.md` for end-to-end workflow
- Reference actual rhoai3-* courses for structure examples

---

*Skill Definition: create-rhoai-course v1.0*  
*Created: 2026-04-06*  
*Status: Ready for Implementation*
