# Documentation-First Method Quick Reference

## The Five Roles at a Glance

| Role | Purpose | Key Prompt | Output |
|------|---------|-----------|--------|
| 📝 **Documenter** | Create system docs | "Acting as a technical documentation specialist, analyze and document..." | Markdown docs |
| 🔍 **Auditor** | Verify accuracy | "Acting as a documentation auditor, review [doc] against actual code..." | Discrepancy list |
| 📊 **PM/Architect** | Plan features | "Acting as PM and Architect, create a plan for [feature]..." | Feature plan |
| 💻 **Developer** | Implement code | "Acting as Developer, implement Phase X of [plan]..." | Working code |
| 🧪 **Test Engineer** | Ensure quality | "Acting as Test Engineer, create tests for [feature]..." | Test suites |

## Essential Commands

### Starting a New Feature
```markdown
1. "Acting as Documenter, document the current [area] of the system"
2. "Acting as Auditor, verify this documentation against the code"
3. "Acting as PM/Architect, create a plan for [feature] using PLAN_TEMPLATE.md"
4. "Acting as Developer, implement Phase 1 of [plan]"
5. "Acting as Test Engineer, test the [feature] implementation"
```

### Quick Context Templates

#### Minimal Developer Context
```markdown
Acting as Developer, implement [specific task].

Context:
- Plan: docs/plans/active/[PLAN].md
- Database: docs/core/DATABASE_SUMMARY.md (sections: [relevant])
- Patterns: docs/meta/PATTERNS.md (pattern: [specific])
```

#### Full Feature Context
```markdown
Acting as Developer, implement [feature].

Context:
- Plan: [full plan]
- Architecture: docs/core/ARCHITECTURE.md
- Database: [relevant schema]
- API: [relevant endpoints]
- Patterns: [applicable patterns]
```

## Directory Structure
```
docs/
├── SUMMARY.md           # Start here
├── core/                # Frequently used
├── detailed/            # Complete docs
├── meta/                # Method docs
├── plans/               # Feature plans
│   ├── active/          # In progress
│   ├── backlog/         # Ready
│   └── done/            # Completed
└── updates/             # Incremental
```

## Plan Phases Template
```markdown
### Phase 1: [Name] ⏳
**Goal**: [What this achieves]
**Time**: [Estimate]

- [ ] **1.1 [Task Group]**
    - [ ] [Specific task]
    - [ ] [Specific task]
```

## Common Patterns Reference

### API Endpoint Pattern
```typescript
// Standard structure
export const handler = async (req, res) => {
  // 1. Validate input
  // 2. Check auth
  // 3. Business logic
  // 4. Return response
}
```

### Database Query Pattern
```sql
-- With error handling
BEGIN;
  -- Your operations
COMMIT;
EXCEPTION WHEN OTHERS THEN
  ROLLBACK;
  RAISE;
```

## Status Indicators
- ⏳ Not started
- 🚧 In progress
- ✅ Completed
- ❌ Blocked
- 🧪 Testing

## Error Response Codes
| Code | Meaning | Action |
|------|---------|--------|
| 400 | Bad Request | Check input |
| 401 | Unauthorized | Add auth |
| 403 | Forbidden | Check permissions |
| 404 | Not Found | Verify resource |
| 422 | Validation Error | Fix input data |
| 500 | Server Error | Check logs |

## Git Commit Format
```
type: Brief description

- Detailed change 1
- Detailed change 2

Refs: #ticket-number
```

Types: feat, fix, docs, style, refactor, test, chore

## Testing Checklist
- [ ] Unit tests for new functions
- [ ] Integration tests for workflows
- [ ] Error cases handled
- [ ] Performance acceptable
- [ ] Documentation updated

## When to Use Each Role

| Situation | Use This Role |
|-----------|---------------|
| Starting new project | Documenter |
| Documentation seems wrong | Auditor |
| Need to plan feature | PM/Architect |
| Ready to code | Developer |
| Implementation complete | Test Engineer |
| Found repeated pattern | Documenter (update PATTERNS.md) |

## Productivity Tips

1. **Batch Similar Tasks**: Do all planning, then all coding
2. **Update Immediately**: Check off tasks as completed
3. **Extract Patterns Early**: Don't wait for third use
4. **Keep Plans Small**: 1-2 week maximum
5. **Context Over Memory**: Always provide context

## Troubleshooting Quick Fixes

| Problem | Solution |
|---------|----------|
| Generic responses | Reduce context, be specific |
| Wrong assumptions | Use Auditor role first |
| Stuck on implementation | Break down plan further |
| Tests failing | Check plan success criteria |
| Slow progress | Review and optimize context |

---

*Keep this reference handy. The method becomes natural after 1-2 weeks of consistent use.*