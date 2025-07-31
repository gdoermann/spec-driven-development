---
marp: false
theme: gaia
paginate: true
backgroundColor: #fff
style: |
  section {
    font-family: 'Helvetica Neue', Arial, sans-serif;
  }
  h1 {
    color: #2563eb;
    font-size: 1.6em;
  }
  h2 {
    color: #1e40af;
    font-size: 1.2em;
    border-bottom: 2px solid #e5e7eb;
    padding-bottom: 0.1em;
  }
  h3 {
    color: #3730a3;
    font-size: 1.0em;
  }
  code {
    background-color: #f3f4f6;
    border-radius: 4px;
    padding: 2px 6px;
    font-size: 0.7em;
  }
  pre {
    background-color: #1f2937;
    border-radius: 8px;
    padding: .9em;
  }
  pre code {
    background-color: transparent;
    color: #f3f4f6;
    font-size: .5em;
  }
  blockquote {
    border-left: 4px solid #3b82f6;
    padding-left: .9em;
    color: #4b5563;
    font-style: italic;
  }
  .columns {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 2em;
  }
  strong {
    color: #1e40af;
  }
  ul {
    line-height: 1.6;
  }
  .highlight {
    background-color: #fef3c7;
    padding: 0.2em 0.4em;
    border-radius: 4px;
  }
---

<!-- _class: lead -->

# The Documentation-First Development Method
## with Claude Code

### Transform Claude into Your Entire Dev Team

🚀 **Achieve 25-person team output as a solo developer**

---

# The Problem We're Solving

<div class="columns">
<div>

## Traditional AI Coding
- Context loss between sessions
- Inconsistent outputs
- Hallucinations increase
- No complex projects
- No continuity

</div>
<div>

## What We Need
- Perfect context retention
- Consistent quality
- Accurate outputs
- Enterprise-scale capability
- Team-like specialization

</div>
</div>

> **The Solution**: Treat documentation as the source of truth for AI context

---

# Core Philosophy

📚 Document  
 → 📋 Plan  
  → 🛠️ Build  
   → 🧪 Test  
    → 📚 Document

> *"Perfect documentation enables perfect AI performance"*

---

# The Method Overview

## Five Specialized Claude Roles

1. **📝 Documenter** - Creates comprehensive system documentation
2. **🔍 Auditor** - Validates documentation accuracy
3. **📊 Project Manager + Architect** - Plans features and technical design
4. **💻 Developer** - Implements according to plan
5. **🧪 Test Engineer** - Ensures quality and completeness

Each role has specific prompts, contexts, and outputs

---

# Phase 1: Documentation Foundation

## Build Your Knowledge Base

```
docs/
├── SUMMARY.md                 # One-page overview
├── core/                      # Essential references
│   ├── DATABASE_SUMMARY.md    # Key tables only
│   ├── API_SUMMARY.md         # Endpoint list
│   └── ARCHITECTURE.md        # System design
├── detailed/                  # Full documentation
│   ├── DATABASE.md           # Complete schema
│   ├── API_DOCS.md          # Full specs
│   └── FLOWS.md             # Business logic
└── meta/                     # Method docs
    ├── PLAN_TEMPLATE.md      # Template for plans
    ├── ROLES.md              # Role definitions
    └── PATTERNS.md           # Reusable patterns
```


**Start small, expand as needed**

</div>
</div>

---

# The Documenter Role

## 📝 Your Documentation Specialist

### Core Responsibilities:
- **Analyze** existing codebase thoroughly
- **Document** current state and architecture
- **Create** comprehensive system documentation
- **Establish** knowledge base foundation

**Purpose**: Creates foundation for all other roles to work effectively

---

# Documenter: Prompt Template

### Standard Prompt:
```markdown
Acting as a technical documentation specialist, analyze the codebase and create comprehensive documentation for [specific area]. 

Include:
1. Current state overview
2. Key components and relationships
3. Important business rules
4. Technical constraints
5. Integration points

Use standard format from docs/meta/PLAN_TEMPLATE.md
```

**Key**: Start with summaries, expand to detailed docs as needed

---

# The Auditor Role

## Ensuring Documentation Accuracy

```markdown
Acting as a documentation auditor, review the following documentation against the actual codebase:

[Documentation to audit]

Check for:
1. Factual accuracy against code
2. Completeness
3. Clarity and consistency
4. Outdated information
5. Internal contradictions

Return specific issues with corrections.
```

> Run repeatedly until **zero issues found**

---

# Hierarchical Documentation

## Optimize Token Usage

<div class="columns">
<div>

### Start Small
- SUMMARY.md (1 page)
- Core concepts only
- Links to details


</div>
<div>

### Expand as Needed
- Drill down when required
- Keep context focused
- Reduce token usage by 60%

</div>
</div>

---

# Hierarchical Documentation

### Example Context
```markdown
# Developer Context for FEATURE

## Include
- Current plan
- DATABASE_SUMMARY.md 
  (sections: orders)
- API_SUMMARY.md 
  (sections: /api/orders/*)

## Exclude
- Other plans
- Testing docs
- Deployment info
```

---

# Phase 2: Planning

## The Project Manager + Architect Role

### Two Jobs, One Role:
- **PM**: Defines the "what" and "why"
- **Architect**: Defines the "how"

---

## The Project Manager + Architect Role

### Plan Organization:
```
docs/plans/
├── README.md          # Prioritized list
├── active/           # Currently working
├── backlog/          # Future plans
├── testing/          # Complete, testing
├── done/             # Deployed
└── archived/         # Historical
```

---

# The Perfect Plan Template


```markdown
# [FEATURE_NAME]

## Quick Summary
[50 words max - what and why]

## Business Context
- **Problem**: What problem does this solve?
- **Users Affected**: Who benefits?
- **Success Metrics**: How do we measure success?

## Technical Specification
### Dependencies
- **Blocks**: [Other plans that must complete first]
- **Enhances**: [Plans that would benefit]
```

<span class="highlight">Every plan follows the same template</span>

---

# Implementation Planning: Phases

```markdown
## Implementation Plan

### Phase 1: Backend Foundation ⏳
**Goal**: Database and API setup
**Estimated Time**: 2 days

- [ ] **1.1 Database Setup**
    - [ ] Create migration file
    - [ ] Add tables: print_job, print_job_item
    - [ ] Add indexes for performance
    - [ ] Test migration up/down

- [ ] **1.2 API Implementation**
    - [ ] Create endpoint: create-print-job
    - [ ] Add validation
    - [ ] Add tests
```

---

# Prioritization System

### Managing Multiple Plans

```markdown
# Plans & Features Directory
## 🎯 Current Implementation Priorities
### Priority 1: Critical Path - **HIGH**
1. **FEATURE_A.md (Phase 2)** - **BLOCKED**
   - Status: Phase 1 complete, waiting on B
   - Blocking: FEATURE_C, FEATURE_D
   - Timeline: 2 days once unblocked

### Priority 2: Enhancements - **MEDIUM**
1. **FEATURE_E.md** - **READY**
   - Status: No blockers
   - Timeline: 1 day
```

Always show dependencies and blockers clearly

---

# Phase 3: Implementation

## The Developer Role

### Focused Context Strategy

Only include what's needed:
- Current plan being implemented
- Relevant sections of core docs
- Specific patterns that apply
- Nothing else

---
## The Developer Role

### Checkpoint Approach
After each task group (1.1, 1.2):
1. Update plan with ✓ checkmarks
2. Note any deviations
3. Commit progress
4. Update documentation notes/changelog
5. Continue to next section

---

# Progress Tracking

## Keep Plans Updated

```markdown
### Phase 1: Backend Foundation ✅ (Completed 2024-01-31)
**Goal**: Database and API setup
**Actual Time**: 1.5 days (vs 2 days estimated)

- [x] **1.1 Database Setup**
    - [x] Create migration file
    - [x] Add tables: print_job, print_job_item
    - [x] Add indexes for: partner_id, status
    - [x] Test migration up/down
    - Note: Added created_at index for performance

- [x] **1.2 API Implementation**
    - [x] Create endpoint: create-print-job
    - Note: Added rate limiting not in plan
```

---

# The Test Engineer Role: QA

```markdown
# Testing: FEATURE_NAME

## Unit Tests
- [ ] Database functions
- [ ] API endpoint validation
- [ ] Component rendering

## Integration Tests  
- [ ] Full user flow
- [ ] Error scenarios
- [ ] Performance benchmarks

## Manual Testing
- [ ] Cross-browser check
- [ ] Mobile responsiveness
```

Test based on the plan's success criteria

---

# Phase 4: Documentation Updates


<div class="columns">
<div>

### Incremental Updates
```
docs/updates/
├── 2024-01-31_FEATURE.md
│   ├── Database changes
│   ├── API changes
│   └── New components
└── README.md
```

Track changes as you go

</div>
<div>

### Consolidation
Monthly process:
1. Auditor reviews updates
2. Merge into main docs
3. Archive increments
4. Update SUMMARY.md

</div>
</div>

**Keep docs current at all times**

---

# Pattern Extraction

## Build Your Library: Extract Reusable Patterns

```typescript
// PATTERNS.md
## Supabase Edge Function with Validation
export const handler = async (req: Request) => {
  // 1. CORS headers
  if (req.method === 'OPTIONS') return corsResponse()
  // 2. Auth check
  const user = await requireAuth(req)
  // 3. Input validation
  const input = await validateInput(req, schema)
  // 4. Business logic
  const result = await businessLogic(input, user)
  return successResponse(result)
}
```

---

# Decision Log

## Prevent Re-litigation

```markdown
# DECISIONS.md

## 2024-01-31: Print Queue Architecture
- **Decision**: Item-level tracking
- **Alternatives**: Order-level (simpler)
- **Rationale**: Supports partial reprints
- **Trade-offs**: Complex queries, better accuracy
- **Revisit**: After 1000 print jobs
```

Document the "why" behind architectural choices

---

# Success Metrics

## Track Your Improvement

### Quantitative
- **Velocity**: Features per week *(should increase)*
- **Quality**: Bugs per feature *(should decrease)*
- **Reuse**: Patterns extracted and reused
- **Context Time**: Minutes to resume after break

---

# Success Metrics

## Track Your Improvement

### Qualitative
- Documentation accuracy improves
- Plans become more precise
- Less back-and-forth with Claude
- Confidence in system grows

---

# Common Pitfalls

## Learn From Experience

<div class="columns">
<div>

### Documentation Drift
**Symptom**: Plans ≠ reality
**Solution**: Update during dev, not after

</div>
<div>


### Context Overload
**Symptom**: Generic responses
**Solution**: Hierarchical docs

</div>
</div>

---

# Common Pitfalls

## Learn From Experience

<div class="columns">
<div>

### Role Confusion
**Symptom**: Dev making architecture decisions
**Solution**: Clear handoffs

</div>
<div>

### Over-Planning
**Symptom**: Plans > implementation
**Solution**: Time-box planning

</div>
</div>

---

# Getting Started

## Your First Session

```markdown
1. "Acting as a Documenter, analyze my codebase and create a DATABASE.md file"
2. "Acting as an Auditor, review this DATABASE.md against the actual schema"
3. "Acting as a Project Manager and Architect, create a plan for [feature] using PLAN_TEMPLATE.md"
4. "Acting as a Developer, implement Phase 1 of the plan"
5. "Update the plan marking Phase 1 complete"
```

Start small, build momentum

---

# The Compound Effect

## Why This Method Works

### Traditional Approach
- Each feature starts from scratch
- Context rebuilt every time
- Quality varies wildly
- Knowledge lost between sessions

---

# The Compound Effect

## Why This Method Works

### Documentation-First
- Each feature builds on the last
- Context perfectly preserved
- Quality continuously improves
- Knowledge compounds over time

> **Result**: 10x productivity gain within days


---

# The Three Pillars of Success

**1. Documentation is Code** 
Treat it with the same respect and maintenance

**2. Roles Provide Focus** 
Each role has one job, does it perfectly  

**3. Process Creates Compound Gains** 
Every cycle makes the next one better


> *"The best time to start was yesterday. The second best time is now."*

---

<!-- _class: lead -->

# Start Your Journey

## 🚀 Transform Your Development Today

**Remember**: Discipline in the process creates freedom in the results

---

# Questions?

## Let's Build Something Amazing

### Connect & Learn More:
- Author: Greg Doermann (gdoermann@gmail.com)
- Method documentation: `https://github.com/gdoermann/ai-coding-doc-first-method`

<br>

💡 **Pro Tip**: Start with one small feature and follow the process exactly. The results will convince you.

---

<!-- _class: lead -->

# Thank You!

## Now go build something incredible 🚀

Remember: You now have the power of a 25-person team at your fingertips.

**Use it wisely.**