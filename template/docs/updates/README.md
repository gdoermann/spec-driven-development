# Documentation Updates

This directory contains incremental documentation updates that haven't been merged into the main documentation yet. This approach helps maintain documentation accuracy without constantly updating large files.

## Purpose

- **Track changes** as they happen during development
- **Avoid conflicts** when multiple features modify same docs
- **Review updates** before merging into main documentation
- **Maintain history** of what changed and when

## Structure

Updates are organized by date and feature:

```
updates/
├── README.md                          # This file
├── 2024-01-31_USER_AUTH.md           # Updates from auth feature
├── 2024-02-01_SEARCH_FEATURE.md      # Updates from search feature
└── 2024-02-15_MONTHLY_MERGE.md       # Record of monthly consolidation
```

## Update File Format

Each update file should follow this format:

```markdown
# Documentation Updates - [DATE] - [FEATURE_NAME]

## Summary
Brief description of what changed and why.

## Database Changes

### New Tables
- `table_name` - Description

### Modified Tables
- `existing_table` - Added columns: column1, column2

### Removed Tables
- None

## API Changes

### New Endpoints
- `POST /api/v1/new-endpoint` - Description

### Modified Endpoints
- `GET /api/v1/existing` - Added query parameter `filter`

### Deprecated Endpoints
- `GET /api/v1/old` - Use `/api/v1/new` instead

## Architecture Changes
- Description of any architectural changes

## New Patterns
- Pattern name - Brief description (added to PATTERNS.md)

## Configuration Changes
- New environment variables
- Changed defaults

## Notes
- Any other important changes or considerations
```

## Workflow

### 1. During Development
When implementing a feature that changes the system:

```markdown
# Create an update file
docs/updates/YYYY-MM-DD_FEATURE_NAME.md

# Document changes as you make them
- Database schema changes
- API modifications  
- New components or services
- Configuration updates
```

### 2. During Code Review
- Review documentation updates alongside code
- Ensure all changes are captured
- Verify accuracy against implementation

### 3. After Deployment
- Keep update files for reference
- Use for release notes
- Helps troubleshoot issues

### 4. Monthly Consolidation
On the first of each month:

1. **Audit all updates** using the Auditor role
2. **Merge into main docs**:
   - DATABASE.md
   - API_DOCS.md
   - ARCHITECTURE.md
   - Other affected docs
3. **Create consolidation record**:
   ```markdown
   # Monthly Documentation Merge - [DATE]
   
   ## Updates Merged
   - 2024-01-31_USER_AUTH.md
   - 2024-02-01_SEARCH_FEATURE.md
   
   ## Main Docs Updated
   - DATABASE.md - Added 3 tables
   - API_DOCS.md - Added 5 endpoints
   ```
4. **Archive merged updates** to `updates/archived/`

## Best Practices

### Do's
- ✅ Create update file when starting feature
- ✅ Update in real-time as you code
- ✅ Include examples of changes
- ✅ Note breaking changes prominently
- ✅ Reference the plan that drove changes

### Don'ts
- ❌ Wait until after implementation
- ❌ Batch multiple features in one file
- ❌ Forget to document removals
- ❌ Skip "small" changes

## Examples

### Good Update Entry
```markdown
### Modified Tables
- `users` - Added columns:
  - `last_login_at` (TIMESTAMP) - Track user activity
  - `login_count` (INTEGER DEFAULT 0) - Count total logins
  - Added index on `last_login_at` for reporting queries
```

### Poor Update Entry
```markdown
### Modified Tables
- Updated users table
```

## Integration with Roles

### Documenter
- Creates initial update files
- Ensures completeness
- Merges into main docs monthly

### Auditor
- Reviews updates against code
- Verifies before consolidation
- Checks for missing changes

### Developer
- Updates documentation during coding
- References updates in commits
- Ensures accuracy

## Quick Commands

### Create Today's Update File
```bash
DATE=$(date +%Y-%m-%d)
FEATURE="YOUR_FEATURE_NAME"
touch "docs/updates/${DATE}_${FEATURE}.md"
```

### Find Recent Updates
```bash
ls -la docs/updates/*.md | grep -v archived | tail -10
```

### Check for Unmerged Updates
```bash
find docs/updates -name "*.md" -mtime +30 -not -path "*/archived/*"
```

---

*Remember: Documentation updates are as important as code changes. Keep them current!*