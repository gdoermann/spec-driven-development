# Getting Started with Documentation-First Development

This guide will walk you through setting up and using the Documentation-First Development Method with Claude Code.

## Quick Start (10 minutes)

### 1. Copy the Template
```bash
# Copy this template to your project
cp -r template/* your-project/
```

### 2. Initial Documentation Session
Start with Claude Code and use the Documenter role:

```markdown
Acting as a technical documentation specialist, analyze my codebase and create:
1. A DATABASE_SUMMARY.md with key tables
2. An API_SUMMARY.md with main endpoints
3. An updated SUMMARY.md for the project

Focus on the most important aspects first.
```

### 3. Audit the Documentation
Once documentation is created, switch to Auditor role:

```markdown
Acting as a documentation auditor, review the DATABASE_SUMMARY.md against the actual database schema. Report any discrepancies.
```

### 4. Create Your First Plan
Use the PM/Architect role:

```markdown
Acting as a Project Manager and Technical Architect, create a plan for [your feature] using the template from docs/meta/PLAN_TEMPLATE.md. 

Include business value, technical approach, and phased implementation.
```

### 5. Implement the Plan
Switch to Developer role:

```markdown
Acting as a Developer, implement Phase 1 of [PLAN_NAME].

Context:
- Plan: docs/plans/active/[PLAN_NAME].md
- Database: docs/core/DATABASE_SUMMARY.md
- API: docs/core/API_SUMMARY.md

Update the plan with checkmarks as tasks are completed.
```

## Understanding the Five Roles

### 📝 The Documenter
**Purpose**: Creates accurate system documentation
**When to use**: 
- Starting a new project
- After major changes
- When documentation is outdated

### 🔍 The Auditor  
**Purpose**: Ensures documentation matches reality
**When to use**:
- After Documenter creates docs
- Before starting new features
- Regular documentation reviews

### 📊 The PM/Architect
**Purpose**: Plans features with business and technical context
**When to use**:
- Before implementing any feature
- When requirements need clarification
- For technical design decisions

### 💻 The Developer
**Purpose**: Implements according to plans
**When to use**:
- After plan is approved
- For actual coding work
- When updating existing code

### 🧪 The Test Engineer
**Purpose**: Ensures quality through testing
**When to use**:
- After implementation
- For test creation
- During bug investigation

## Directory Structure Explained

```
docs/
├── SUMMARY.md              # Start here - project overview
├── core/                   # Essential, frequently-used docs
│   ├── DATABASE_SUMMARY.md # Key tables only
│   ├── API_SUMMARY.md      # Main endpoints
│   └── ARCHITECTURE.md     # System design
├── detailed/               # Complete documentation
│   ├── DATABASE.md         # Full schema
│   └── API_DOCS.md         # All endpoints
├── meta/                   # Method documentation
│   ├── ROLES.md            # Role definitions
│   ├── PLAN_TEMPLATE.md    # Plan template
│   ├── PATTERNS.md         # Code patterns
│   └── DECISIONS.md        # Architecture decisions
├── plans/                  # Feature plans
│   ├── active/             # Currently implementing
│   ├── backlog/            # Ready to start
│   ├── testing/            # In QA
│   ├── done/               # Completed
│   └── archived/           # Historical
└── updates/                # Incremental doc updates
```

## Best Practices

### 1. Start Small
- Don't document everything at once
- Focus on core tables and APIs first
- Expand documentation as needed

### 2. Keep Plans Focused
- Each plan should be 1-2 weeks max
- Break large features into multiple plans
- Always include clear success criteria

### 3. Update in Real-Time
- Check off tasks as you complete them
- Note any deviations from the plan
- Update documentation immediately

### 4. Use the Right Context
- Don't include everything in context
- Reference specific sections
- Use hierarchical documentation

### 5. Extract Patterns
- Notice repeated code structures
- Add them to PATTERNS.md
- Reuse in future implementations

## Common Workflows

### Starting a New Feature
1. Document current state (Documenter)
2. Audit documentation (Auditor)
3. Create feature plan (PM/Architect)
4. Implement in phases (Developer)
5. Test thoroughly (Test Engineer)
6. Update documentation (Documenter)

### Debugging Production Issues
1. Use Auditor to verify docs match production
2. Use Developer with specific context to investigate
3. Create plan for fix if complex
4. Implement fix
5. Update documentation

### Onboarding New Team Members
1. Start with SUMMARY.md
2. Review active plans
3. Use Documenter to explain specific areas
4. Provide role descriptions

## Measuring Success

Track these metrics:
- **Velocity**: Features completed per week
- **Quality**: Bugs per feature
- **Reuse**: Patterns used multiple times
- **Context Time**: Minutes to resume work

## Troubleshooting

### "Claude gives generic responses"
- Reduce context size
- Be more specific with role
- Use hierarchical documentation

### "Plans don't match implementation"
- Update plans during development
- Use checkmarks religiously
- Note all deviations

### "Documentation gets outdated"
- Update docs before coding
- Use Auditor role regularly
- Set documentation review schedule

## Advanced Techniques

### Context Optimization
```markdown
## Developer Context for ORDER_PROCESSING

Include:
- docs/plans/active/ORDER_PROCESSING.md
- docs/core/DATABASE_SUMMARY.md (sections: orders, order_items)
- docs/core/API_SUMMARY.md (endpoints: /orders/*)
- docs/meta/PATTERNS.md (patterns: Transaction Pattern)

Exclude:
- User management docs
- Unrelated API endpoints
- Completed plans
```

### Incremental Documentation
Instead of updating entire docs:
1. Create updates in `docs/updates/`
2. Use Auditor to review
3. Merge into main docs monthly

### Pattern Evolution
1. Implement feature
2. Extract common patterns
3. Document in PATTERNS.md
4. Reuse in next feature

## Next Steps

1. **Set up your documentation structure** using this template
2. **Create initial documentation** for your existing system
3. **Plan your first feature** using the method
4. **Implement with confidence** knowing you have perfect context
5. **Share your experience** and help improve the method

Remember: The first week requires discipline, but the productivity gains compound quickly. Trust the process!

---

*For detailed role descriptions and templates, see the [docs/meta](./docs/meta) directory.*