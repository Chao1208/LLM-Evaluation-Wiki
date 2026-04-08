# Wiki Query

Query the wiki and synthesize an answer from the accumulated knowledge.

## Argument

$ARGUMENTS - The question to ask. If not provided, prompt the user for their question.

## Instructions

You are answering a question using the LLM Wiki as your knowledge base.

### Pre-flight

1. Read `CLAUDE.md` to understand the wiki's schema and conventions.
2. Read `index.md` to get an overview of all available pages.

### Search & Retrieve

1. Based on the question, identify the most relevant wiki pages from the index.
2. Read those pages thoroughly. If they reference other pages that seem relevant, follow the links and read those too.
3. If the wiki doesn't contain enough information to answer, say so explicitly and suggest what sources might help.

### Synthesize

1. Compose a clear, well-structured answer.
2. **Cite your sources**: For every claim, reference the wiki page it came from using markdown links.
3. If there are contradictions in the wiki, present both sides and note the conflict.
4. If the question requires information beyond what's in the wiki, clearly distinguish between "what the wiki says" and "what I know from general knowledge".

### Save (optional)

1. After presenting the answer, ask the user: **"This looks like a valuable analysis. Want me to save it to the wiki?"**
2. If yes:
   - Save as a new page in `wiki/analyses/` with proper frontmatter
   - The `type` should be `analysis`
   - Include the original question in the frontmatter
   - Update `index.md`
   - Append to `log.md`
3. If no, just log the query in `log.md` with `Answer saved: not saved`.

### Output Formats

Adapt the output format to the question type:
- **Factual questions** → concise answer with citations
- **Comparison questions** → markdown table
- **Timeline questions** → chronological list
- **Exploratory questions** → structured essay with headers
- **Data questions** → if appropriate, suggest generating a chart

Always ask if the user wants a different format.
