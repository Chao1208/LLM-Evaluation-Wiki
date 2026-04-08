# Wiki Init

Initialize a new LLM Wiki project in the current working directory.

## Instructions

You are setting up a new LLM Wiki project. Follow these steps:

### Step 1: Ask the user

Ask the user the following questions (one message, let them answer together):

1. **Domain**: What is this wiki about? (e.g., research, book reading, personal growth, business, competitive analysis, trip planning, etc.)
2. **Description**: A one-sentence description of the wiki's purpose.
3. **Any special requirements?** (e.g., specific categories, special page types, language preference)

### Step 2: Create the directory structure

```
raw/
  assets/
wiki/
  entities/
  concepts/
  sources/
  analyses/
```

Create each directory. Add a `.gitkeep` file in empty directories so git tracks them.

### Step 3: Generate CLAUDE.md

Read the template from `templates/CLAUDE.md` if it exists in the skill directory. Otherwise, generate one from scratch following the LLM Wiki pattern.

Replace the placeholders:
- `{{DOMAIN}}` → user's chosen domain
- `{{DESCRIPTION}}` → user's description
- `{{DATE}}` → today's date

Customize the schema based on the user's domain. For example:
- **Book reading**: entities might include characters, places, artifacts; concepts might include themes, plot threads
- **Research**: entities might include authors, institutions, datasets; concepts might include theories, methods
- **Business**: entities might include companies, people, products; concepts might include strategies, market trends

Save as `CLAUDE.md` in the project root.

### Step 4: Create index.md

Create an initial `index.md` with the table structure defined in the schema, but with empty tables.

### Step 5: Create log.md

Create `log.md` with a single initialization entry:

```markdown
# Wiki Log

## [YYYY-MM-DD] init | Wiki Initialized
- Domain: {domain}
- Description: {description}
```

### Step 6: Initialize git (if not already a git repo)

Run `git init` if there's no `.git` directory. Create a `.gitignore` with sensible defaults:

```
.DS_Store
*.tmp
```

### Step 7: Report

Tell the user what was created and suggest next steps:
1. Drop source files into `raw/`
2. Run `/wiki-ingest` to process them
3. Open the directory in Obsidian for the best browsing experience
