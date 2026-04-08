---
name: concept-card-extractor
description: Extract key concepts and technical terms from specified documents (reports, articles, papers, code docs) and generate or supplement Obsidian notes as atomic concept cards. Always use this skill when the user mentions extracting concepts, generating concept cards, creating knowledge cards from files, or supplementing Obsidian notes with terminology explanations. Use it whenever the task involves reading a document and turning its concepts into Zettelkasten-style Obsidian notes vault.
---

# Concept Card Extractor

This skill guides you through extracting concepts and technical terms from a user-specified file and generating (or supplementing) atomic concept cards in an Obsidian vault.

## When to use

Use this skill when the user asks you to:

- Extract concepts or technical terms from a file
- Generate concept cards / knowledge cards from a document
- Supplement or update existing Obsidian notes with new concept explanations
- Read a report/article and create Zettelkasten-style notes

## Workflow

Follow these steps in order:

### 0. Identity top folder of concept cards (Zettelkasten-style notes)

Ask the user, or determined from other preference settings for top folder of concept cards, referenced as `$CONCEPT_FOLDER` in following articles.

### 1. Identify the source file and domain

Ask the user for the file path if not provided. Also determine the domain/topic of the file (e.g., AI, Engineering, Business) so you know which subfolder under `$CONCEPT_FOLDER` to use.

If the user does not specify a domain, infer it from the file content or path, and tell the user your inference before proceeding.

### 2. Read the source file

Read the entire file if it's reasonably sized (< 500 lines). For very large files (> 1000 lines):

- First scan the table of contents or headings to understand the structure
- Use `grep` to locate sections that define or explain key terms
- Read the most relevant sections in full, rather than attempting to read every line
- Focus on sections that introduce named concepts, frameworks, or technical terms

### 3. Extract key concepts

Find as many concepts or terms as possible that meet the following criteria: they are clearly defined or exactly explained in the source file; and deserve their own atomic notes.

**For long documents**, do not attempt to extract everything at once. Instead:

- First, understand the overall structure (sections, chapters, or logical blocks).
- Analyze and extract concepts **section by section** (or in coherent chunks of ~500–1000 lines).
- After finishing one chunk, summarize what you found before moving to the next.
- Maintain a running list of candidate concepts so nothing is lost across chunks.
- Only proceed to the next chunk after extracting and documenting the concepts from the current one.

Good candidates are:

- Named frameworks, methodologies, or theories (e.g., "ReAct", "思维树", "Harness Engineering")
- Technical terms with specific meaning in the source (e.g., "认知债务", "MCP")
- Distinct models, patterns, or metaphors introduced by the author

Avoid overly generic terms (e.g., "computer", "software", "AI") unless the source gives them a specific technical meaning.

For each concept, capture:

- **Concept name** (prefer the commonly used Chinese or English term; include an alias if both are common, or if there's a widely used abbreviation)
- **One-sentence definition**
- **2-5 key points/bullet takeaways**
- **Relevant excerpt** (1-2 sentences or a short paragraph directly from the source)
- **Related concepts** mentioned in the source (these will become wikilinks)

If the source does not contain any extractable concepts worth making into atomic cards, report this to the user instead of forcing cards.

### 4. Check for existing concept notes

Before creating any new note, check whether a note for that concept already exists in specified Obsidan vault. Search using:

- tools/skills for search Obsidian notes if available.
- `glob` or `grep` for files matching the concept name
- Case-insensitive matching
- Checking aliases in frontmatter when reading candidate files

If an existing note is found, read it fully.

### 5. Generate or supplement each concept card

#### If the note does NOT exist:

Create a new `.md` file under `$CONCEPT_FOLDER` using the exact format below. **Store it in the most appropriate subdomain subfolder** (e.g., `$CONCEPT_FOLDER/AI/`, `$CONCEPT_FOLDER/Engineering/`, `$CONCEPT_FOLDER/Business/`). If no suitable subfolder exists, create one that accurately reflects the concept's category.

**File naming:** Use the concept's primary name as the filename. Examples:

- Single word or acronym: `CoT.md`, `RAG.md`, `ReAct.md`
- Chinese term: `注意力机制.md`, `认知债务.md`
- Multi-word English phrase: prefer hyphens when natural (`Plan-and-Execute.md`), but use spaced version (`Chain of Thought.md`) if that's the common way the term is written

The filename should match the wikilink that people would naturally use when referencing this concept.

**Frontmatter template (for NEW notes):**

```yaml
---
tags:
  - 概念
  - <Domain>
  - <OptionalSubTag>
source:
  - "[[<SourceFileName>]]"
created: <YYYY-MM-DD>
aliases:
  - <Alias1>
---
```

Use `source: "[[<SourceFileName>]]"` (wikilink format) for new notes, matching the vault's concept-card template. Use the source file's basename, not the full path.

**Body template:**

```markdown
# <Concept Name>

## 一句话定义

<One-sentence definition>

## 核心要点

- <Key point 1>
- <Key point 2>
- <Key point 3>

## 关联概念

- [[RelatedConcept1]]
- [[RelatedConcept2]]

## 待解决问题 / 可研究方向

- <Open question or research direction>

## 原文摘录

> <Relevant excerpt from the source>
```

#### If the note DOES exist:

Perform a **smart supplement/merge**:

- **Do NOT overwrite** existing content.
- **Preserve the existing heading structure** as much as possible. If the existing note uses a different section name for the same idea (e.g., "## Sources" vs "## 原文摘录"), use the existing note's naming convention when appending.
- If the existing note is missing a section (e.g., no "原文摘录" or no "待解决问题"), add that section using content from the new source.
- If a section exists but the new source illuminates a different angle, append new bullets or a new excerpt below the existing ones, marking the source when helpful (e.g., `> <excerpt> — 来源: [[<SourceFileName>]]`).
- **Do not add content that is semantically identical** to existing bullets or excerpts.
- Add any new related-concept wikilinks to the "关联概念" section if they aren't already there.
- If the source provides a clearer one-sentence definition and the existing one is vague, you may update the definition but preserve the old one as an alternative perspective.
- Update the frontmatter `source` list to include the new source file if it's not already there. **Match the existing source format** (e.g., if the note already uses `- - filename.md`, use that format; if it uses `"[[filename.md]]"`, use that format).
- Update `tags` if the new domain/tag is relevant and missing.

### 6. Link related concepts

For every concept card you created or updated, ensure its "关联概念" section includes wikilinks to other concepts you extracted from the same source (if they are genuinely related). This builds the Zettelkasten network.

If a related concept card does not yet exist, still include the wikilink — the link will light up once the card is created.

### 7. Quality check

Before writing files, quickly verify:

- **Frontmatter validity**: Ensure the YAML is correct (no unescaped special characters, proper list indentation).
- **Tag correctness**: Every card should have `概念` and at least one domain tag.
- **Wikilink consistency**: Filenames and wikilinks should match (e.g., if the file is `Plan-and-Execute.md`, the wikilink should be `[[Plan-and-Execute]]`).
- **Source accuracy**: The `source` field contains the correct source filename.
- **No duplicates**: You haven't created two cards for the same concept under different names.

### 8. Link extracted concepts in the source document

After all concept cards are created or updated, **revisit the source file** and replace mentions of the newly extracted concepts with Obsidian wikilinks `[[ConceptName]]` (use the exact filename without the `.md` extension).

- Only link the **first meaningful mention** of each concept in a given section, or the mention that provides the most context. Avoid hyperlinking every single occurrence, which creates visual noise.
- If the source already uses links or formatting for that term, preserve the existing style and add the wikilink when appropriate.
- If the source file should remain untouched (e.g., it is an external readonly reference), skip this step and inform the user.

### 9. Present a summary to the user

Summarize:

- Which concepts you extracted
- Which files were created vs. supplemented
- Any inferences you made (domain, file location, concept naming)
- If the user prefers, you can write the files first and then report.

If you are unsure about file paths or naming, ask the user to confirm before writing.

## Formatting rules

- Use standard Obsidian wikilinks `[[ConceptName]]` for internal links.
- Frontmatter must be valid YAML.
- Tags should include `概念` and a domain tag (e.g., `AI`, `Engineering`, `Business`).
- Keep each card atomic: one concept per file.
- Use the source file name (not full path) in the `source` field, wrapped in wikilink syntax when appropriate.

## Example

**User prompt:** "请从 `1-Sources/Reports/ai_agent_patterns_comprehensive_research.md` 中提取概念，生成概念卡片到 2-Concepts/AI/"

**Your actions:**

1. Read `1-Sources/Reports/ai_agent_patterns_comprehensive_research.md`
2. Extract concepts like "ReAct", "CoT", "ToT"
3. Check if `$CONCEPT_FOLDER/AI/ReAct.md`, `$CONCEPT_FOLDER/AI/CoT.md`, `$CONCEPT_FOLDER/AI/ToT.md` exist
4. For "CoT":
   - If `CoT.md` exists → read it, merge new points/excerpts, add source if missing, add wikilinks.
   - If it does not exist → create `$CONCEPT_FOLDER/AI/CoT.md` with frontmatter + body following the template.
5. Ensure "关联概念" includes `[[ReAct]]` and `[[ToT]]` where relevant.
6. Report: "已提取 3 个概念。新建 1 个卡片（ToT），补充 2 个已有卡片（ReAct、CoT）。"

## Troubleshooting

- **Can't find the source file?** Verify the path with the user. Vault root is typically the current working directory.
- **Ambiguous domain?** When in doubt, ask the user or use a broad domain like `Engineering` or `General`.
- **Existing note with a different filename?** Search content/aliases; if you find a match, treat it as the same concept and merge rather than creating a duplicate.
- **Very large source file?** You may read it in chunks, but be systematic so you don't miss important concepts. Summarize each chunk before extracting concepts.
