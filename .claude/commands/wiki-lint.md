# Wiki Lint

Perform a health check on the wiki, finding issues and suggesting improvements.

## Instructions

You are performing a comprehensive health check on the LLM Wiki.

### Pre-flight

1. Read `CLAUDE.md` to understand the wiki's schema and conventions.
2. Read `index.md` to get the full page catalog.
3. Read `log.md` to understand recent activity.

### Scan

Read every wiki page. For each page, check:

#### Structural Issues
- [ ] **Missing frontmatter**: Page lacks YAML frontmatter or required fields
- [ ] **Bad filename**: Doesn't follow kebab-case convention
- [ ] **Wrong directory**: Page is in the wrong category directory
- [ ] **Broken links**: Internal links that point to non-existent pages

#### Content Issues
- [ ] **Contradictions**: Claims that conflict with other pages (cite both pages)
- [ ] **Stale content**: Information that has been superseded by newer sources
- [ ] **Unsourced claims**: Statements without source citations
- [ ] **Thin pages**: Pages with very little content that could be expanded

#### Graph Issues
- [ ] **Orphan pages**: Pages with no inbound links from other pages
- [ ] **Missing pages**: Entities or concepts mentioned in text but lacking their own page
- [ ] **Missing cross-references**: Mentions of entities/concepts that should be linked but aren't
- [ ] **Hub imbalance**: Important pages that are under-linked

#### Index & Log Issues
- [ ] **Index out of sync**: Pages that exist but aren't in the index, or index entries for deleted pages
- [ ] **Stale summaries**: Index summaries that don't match the current page content

### Report

Present findings in a structured report:

```markdown
## Wiki Health Report — YYYY-MM-DD

### Summary
- Total pages: N
- Total sources: N
- Issues found: N (X critical, Y warnings, Z suggestions)

### 🔴 Critical Issues
(Things that are wrong and should be fixed)

### 🟡 Warnings
(Things that could cause problems)

### 🟢 Suggestions
(Improvements and opportunities)

### 📊 Statistics
- Most connected page: X (N links)
- Least connected page: X (N links)
- Average sources per entity: N
- Topics with most coverage: ...
- Topics with least coverage: ...

### 🔍 Recommended Next Steps
1. Sources to look for: ...
2. Questions to explore: ...
3. Pages to create: ...
```

### Fix

After presenting the report, ask: **"Want me to fix the auto-fixable issues?"**

Auto-fixable issues include:
- Adding missing cross-reference links
- Updating the index to match actual pages
- Adding missing frontmatter fields
- Fixing broken internal links

Do NOT auto-fix:
- Contradictions (need user judgment)
- Content quality issues
- Structural reorganization

If the user agrees, fix the issues and log the lint pass in `log.md`.
