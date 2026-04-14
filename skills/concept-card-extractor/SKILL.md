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

### 0. Explore Vault structure and identify storage locations

Before creating any notes, explore the Obsidian Vault's directory structure to find the most appropriate places for **two types of cards**:
- **Knowledge concepts** (theories, methodologies, patterns, general terms)
- **Products** (software products, open-source projects, libraries, frameworks, tools, platforms)

1. List the top-level folders in the vault (e.g., using `glob`, `ls`, or `obsidian eval`). Exclude system folders such as `.obsidian/`, `Attachments/`, `Templates/`, etc.
2. Look for an existing **concept-card root folder**. Priority indicators include names like:
   - `Concepts/`, `2-Concepts/`, `Cards/`, `Zettelkasten/`, `概念/`, `知识卡片/`
   - Any folder that clearly serves as a knowledge-base or atomic-notes hub
3. Look for an existing **product/tool root folder**. Priority indicators include names like:
   - `Products/`, `Tools/`, `Software/`, `Projects/`, `Libraries/`, `产品/`, `工具/`, `库/`
   - Any folder that clearly serves as a catalog for software, tools, or products
4. If **one** obvious root is found for each type, treat them as `$CONCEPT_FOLDER` and `$PRODUCT_FOLDER`.
5. If **multiple** plausible roots exist for a type, list them and **ask the user** which one to use.
6. If **none** are found for a type, propose a reasonable location based on the vault's existing organization (e.g., create `Concepts/` and `Products/` at vault root, or under an existing category folder), and **confirm with the user** before proceeding.
7. Once both roots are determined, explore their subfolder structures to understand how items are currently organized by domain (e.g., `AI/`, `Engineering/`, `Business/`). You'll use this structure in Step 5 to place each new card in the right subfolder.

> **Note:** If the vault uses a **flat or unified structure** (all cards in one folder without separate concept/product roots), treat that single root as both `$CONCEPT_FOLDER` and `$PRODUCT_FOLDER`, but still tag and classify each card appropriately as either a "概念" or "产品/工具".

### 1. Identify the source file and domain

Ask the user for the file path if not provided. Also determine the domain/topic of the file (e.g., AI, Engineering, Business) so you know which subfolder to use when storing the extracted cards.

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

**Knowledge Concepts:**
- Named frameworks, methodologies, or theories (e.g., "ReAct", "思维树", "Harness Engineering")
- Technical terms with specific meaning in the source (e.g., "认知债务", "认知负荷")
- Distinct models, patterns, or metaphors introduced by the author
- General concepts and principles (e.g., "依赖注入", "事件驱动架构")

**Products / Tools:**
- Software products and platforms (e.g., "Obsidian", "Claude", "Docker", "Kubernetes")
- Open-source projects and libraries (e.g., "React", "LangChain", "PyTorch", "FastAPI")
- Frameworks and development tools (e.g., "Spring Boot", "Next.js", "Vite")
- APIs, protocols, and specifications that are product-like (e.g., "OpenAI API", "GraphQL", "gRPC")

**How to classify:**
- If the term represents a **theory, pattern, or abstract idea** → classify as **concept**
- If the term represents a **concrete software, library, tool, or platform** → classify as **product**
- When in doubt, consider: *Is this something you can download, install, or use as a tool?* If yes → **product**; if no → **concept**

Avoid overly generic terms (e.g., "computer", "software", "AI") unless the source gives them a specific technical meaning.

For each extracted item, capture:

- **Name** (prefer the commonly used Chinese or English term; include an alias if both are common, or if there's a widely used abbreviation)
- **Type**: `concept` or `product`
- **One-sentence definition**
- **2-5 key points/bullet takeaways**
- **Relevant excerpt** (1-2 sentences or a short paragraph directly from the source)
- **Related concepts/products** mentioned in the source (these will become wikilinks)

If the source does not contain any extractable concepts worth making into atomic cards, report this to the user instead of forcing cards.

### 4. Check for existing notes

Before creating any new note, check whether a note for that item already exists **anywhere in the vault**. Search using:

- `obsidian search` if available, searching for the concept name
- `glob` or `grep` across the entire vault for files matching the concept name
- Case-insensitive matching
- Checking aliases in frontmatter when reading candidate files
- Also check inside `$CONCEPT_FOLDER` and `$PRODUCT_FOLDER` and their subfolders specifically

If an existing note is found anywhere in the vault, read it fully to avoid duplicates.

### 5. Generate or supplement each concept card

#### If the note does NOT exist:

Create a new `.md` file using the exact format below. Choose the storage location based on the item's **type**:

**For Knowledge Concepts (type = `concept`):**
- Store under `$CONCEPT_FOLDER` in the most appropriate subdomain subfolder (e.g., `$CONCEPT_FOLDER/AI/`, `$CONCEPT_FOLDER/Engineering/`)
- Use tags: `概念` and the domain tag

**For Products/Tools (type = `product`):**
- Store under `$PRODUCT_FOLDER` in the most appropriate subdomain subfolder (e.g., `$PRODUCT_FOLDER/DevTools/`, `$PRODUCT_FOLDER/AI/`)
- Use tags: `产品` (or `工具`/`库` as appropriate) and the domain tag

**For both types:**
- If a matching subfolder already exists, use it
- If no suitable subfolder exists, create one that accurately reflects the item's category
- If the root folder has no subfolders, place the card directly inside or create a new subfolder as appropriate

**File naming:** Use the concept's primary name as the filename. Examples:

- Single word or acronym: `CoT.md`, `RAG.md`, `ReAct.md`
- Chinese term: `注意力机制.md`, `认知债务.md`
- Multi-word English phrase: prefer hyphens when natural (`Plan-and-Execute.md`), but use spaced version (`Chain of Thought.md`) if that's the common way the term is written

The filename should match the wikilink that people would naturally use when referencing this concept.

**Frontmatter templates (for NEW notes):**

**For Knowledge Concepts:**
```yaml
---
tags:
  - 概念
  - <Domain>
  - <OptionalSubTag>
type: concept
source:
  - "[[<SourceFileName>]]"
created: <YYYY-MM-DD>
aliases:
  - <Alias1>
---
```

**For Products/Tools:**
```yaml
---
tags:
  - 产品
  - <Domain>
  - <OptionalSubTag>
type: product
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

For every card you created or updated, ensure its "关联概念" section includes wikilinks to other concepts and products you extracted from the same source (if they are genuinely related). This builds the Zettelkasten network.

If a related card does not yet exist, still include the wikilink — the link will light up once the card is created.

### 7. Quality check

Before writing files, quickly verify:

- **Frontmatter validity**: Ensure the YAML is correct (no unescaped special characters, proper list indentation).
- **Tag correctness**: Concept cards should have tag `概念`; product cards should have tag `产品` (or `工具`/`库`). All cards need at least one domain tag.
- **Wikilink consistency**: Filenames and wikilinks should match (e.g., if the file is `Plan-and-Execute.md`, the wikilink should be `[[Plan-and-Execute]]`).
- **Source accuracy**: The `source` field contains the correct source filename.
- **No duplicates**: You haven't created two cards for the same item under different names.

### 8. Link extracted items in the source document

After all cards are created or updated, **revisit the source file** and replace mentions of the newly extracted concepts and products with Obsidian wikilinks `[[ConceptName]]` (use the exact filename without the `.md` extension).

- Only link the **first meaningful mention** of each item in a given section, or the mention that provides the most context. Avoid hyperlinking every single occurrence, which creates visual noise.
- If the source already uses links or formatting for that term, preserve the existing style and add the wikilink when appropriate.
- If the source file should remain untouched (e.g., it is an external readonly reference), skip this step and inform the user.

### 9. Present a summary to the user

Summarize:

- Which concepts and products you extracted
- Which files were created vs. supplemented
- Any inferences you made (domain, file location, naming)
- If the user prefers, you can write the files first and then report.

If you are unsure about file paths or naming, ask the user to confirm before writing.

## Formatting rules

- Use standard Obsidian wikilinks `[[ConceptName]]` for internal links.
- Frontmatter must be valid YAML.
- **Concept cards** should include tag `概念` and a domain tag (e.g., `AI`, `Engineering`, `Business`).
- **Product cards** should include tag `产品` (or `工具`/`库` as appropriate) and a domain tag.
- Include `type: concept` or `type: product` in the frontmatter for new notes.
- Keep each card atomic: one item per file.
- Use the source file name (not full path) in the `source` field, wrapped in wikilink syntax when appropriate.

## Example

**User prompt:** "请从 `1-Sources/Reports/ai_agent_patterns_comprehensive_research.md` 中提取概念，生成概念卡片"

**Your actions:**

1. Explore the vault structure → discover:
   - `$CONCEPT_FOLDER = 2-Concepts/` with subfolders `AI/`, `Engineering/`, `Business/`
   - `$PRODUCT_FOLDER = 3-Products/` with subfolders `AI/`, `DevTools/`, `Platforms/`
2. Read `1-Sources/Reports/ai_agent_patterns_comprehensive_research.md`
3. Extract items and classify:
   - **Concepts**: "ReAct", "CoT", "ToT" → type = `concept`
   - **Product**: "LangChain" → type = `product`
4. Search the whole vault for existing notes
5. For "CoT" (concept):
   - If `CoT.md` exists anywhere → read it, merge new points/excerpts, add source if missing, add wikilinks.
   - If it does not exist → create `$CONCEPT_FOLDER/AI/CoT.md` with tags `概念`, `AI` and `type: concept`.
6. For "LangChain" (product):
   - If `LangChain.md` exists anywhere → read it and merge.
   - If it does not exist → create `$PRODUCT_FOLDER/AI/LangChain.md` with tags `产品`, `AI` and `type: product`.
7. Ensure "关联概念" includes `[[ReAct]]` and `[[ToT]]` where relevant.
8. Report: "已探索 Vault 结构。已提取 3 个概念 + 1 个产品。新建 1 个概念卡片（ToT）、1 个产品卡片（LangChain），补充 2 个已有概念卡片（ReAct、CoT）。"

## Troubleshooting

- **Can't find the source file?** Verify the path with the user. Vault root is typically the current working directory.
- **Can't determine storage folders?** If Step 0 exploration reveals no clear roots for concepts or products, propose locations to the user (e.g., create `Concepts/` and `Products/` at vault root). Never guess without user confirmation.
- **Concept vs Product classification unclear?** Ask yourself: *Can you download, install, or use it as a tool?* If yes → **product**; if it's an idea, theory, or pattern → **concept**. When still uncertain, prefer **concept** or ask the user.
- **Ambiguous domain?** When in doubt, ask the user or use a broad domain like `Engineering` or `General`.
- **Existing note with a different filename?** Search content/aliases across the entire vault; if you find a match, treat it as the same item and merge rather than creating a duplicate.
- **Very large source file?** You may read it in chunks, but be systematic so you don't miss important items. Summarize each chunk before extracting concepts and products.
