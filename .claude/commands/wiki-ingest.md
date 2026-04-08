# Wiki Ingest

Ingest a raw source document into the wiki, extracting knowledge and updating all relevant pages.

## Argument

$ARGUMENTS - Path to the source file to ingest (relative to project root). If not provided, list files in `raw/` and ask the user which one to ingest.

## Instructions

You are ingesting a new source into the LLM Wiki. Follow the workflow defined in `CLAUDE.md`.

### Pre-flight

1. Read `CLAUDE.md` to understand the wiki's schema and conventions.
2. Read `index.md` to understand the current state of the wiki.
3. Read `log.md` (last 20 lines) to understand recent activity.
4. Locate the source file. If `$ARGUMENTS` is empty, list all files in `raw/` (excluding `assets/`) and ask the user which one to ingest.
5. Check if a source summary already exists in `wiki/sources/`. If so, warn the user and ask if they want to re-ingest (which will update the existing summary).

### Ingest

1. **Read the source** completely. If it's very long, read in chunks.
2. **Discuss with the user**: Present 3-5 key takeaways and ask if there's anything specific they want to emphasize or de-emphasize. Wait for their response before proceeding.
3. **Create a source summary** in `wiki/sources/`:
   - Include proper frontmatter (title, type: source, created date, tags)
   - Write a structured summary with sections: Overview, Key Points, Notable Claims, Questions Raised
   - Cite specific passages where relevant
4. **Identify entities and concepts** mentioned in the source.
5. **For each entity/concept**:
   - If a wiki page already exists → read it, then update with new information from this source. Add the source to the page's source list. Note any contradictions with existing information.
   - If no page exists → create a new one with proper frontmatter and an initial write-up based on this source.
6. **Update `index.md`**: Add entries for any new pages. Update summaries and source counts for existing pages.
7. **Update `log.md`**: Append a log entry following the format in the schema.

### Report

After completing the ingest, provide a summary:
- Source summary page created
- New pages created (list them with links)
- Existing pages updated (list them with what changed)
- Any contradictions or interesting connections found
- Suggested follow-up questions or sources to look for
