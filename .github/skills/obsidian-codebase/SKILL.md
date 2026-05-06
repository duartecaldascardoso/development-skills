---
name: obsidian-codebase
description: Guide for reading, understanding, and maintaining codebases stored as markdown notes in Obsidian vaults. Use when working with knowledge bases or documentation stored as interconnected markdown files.
---

Use this skill to navigate and reason about Obsidian vaults structured as codebases where all files are markdown notes. When working with such vaults, the primary challenge is not reading individual files but understanding the architecture across many files and finding what matters for a specific task.

## Objectives

1. Develop a mental model of the vault structure before diving into individual files.
2. Navigate efficiently using backlinks and folder patterns rather than scrolling.
3. Distinguish between evergreen guidance and time-specific notes.
4. Maintain rigor when extracting and applying knowledge from the vault.
5. Write updates that match the existing voice and structure.

## Navigate Before You Search

Obsidian vaults work best when you understand their organization. Before opening files randomly, map the folder structure and identify the core categories. Most well-maintained vaults have clear logical separations: one section for principles, another for specific projects or domains, and a third for utilities or reference material.

Start by checking the vault root for a README or index file that explains the intended structure. If none exists, browse the top-level folders and read one representative file from each to understand what goes where. This upfront investment saves time because you can then ask more precise questions about specific regions of the vault rather than treating it as an unorganized pile of notes.

When the vault is deep (many nested folders), focus on the immediate children of your target folder. If you need to understand a single project or domain, read the folder's index file first if present. Obsidian vaults often use index files to summarize their contents and point to key notes. If an explicit index exists, it is usually more reliable than guessing.

## Use Backlinks to Understand Relationships

Obsidian files reference each other through wikilinks and backlinks. When you encounter a note that seems relevant to your task, check its backlinks to understand what other notes depend on it or relate to it. This creates a quick mental picture of how that note fits into the broader knowledge base without reading every related file.

Be intentional about following backlinks. It is easy to get lost in a web of cross-references. Instead, follow only links that directly support your current goal. If a backlink seems tangential, skip it and move on.

## Distinguish Evergreen from Time-Specific Content

Obsidian vaults often mix timeless principles with dated notes, project logs, or time-sensitive decisions. Always check the creation and modification dates of files and whether they are tagged as archived, deprecated, or superseded. A note from years ago may no longer reflect current practice, or it may contain the only documentation of an old approach you need to understand for maintenance.

When writing or updating notes, be clear about timing assumptions. If your guidance applies only to a specific version or period, say so explicitly. Evergreen principles stay relevant longer if they are stated without unnecessary temporal qualifiers.

## Reading for Extraction

When you need to extract knowledge from the vault (rules, architecture decisions, code patterns), read for structure first, details second. Skim each file for headings, key sentences, and concrete examples. Most well-written vault notes use headers to signal what each section covers, allowing you to jump to the relevant part without processing the entire file.

Take note of inconsistencies or conflicts within the vault. If you find two notes with contradictory guidance, identify which is more recent or authoritative before relying on either. When updating related notes, check for other files that might be affected.

## Writing Updates with Rigor

When you add or modify notes in the vault, maintain consistency with the existing voice. This does not mean being bland, but rather avoiding unnecessary elaboration, hedging language, or marketing-style framing. Write directly about what is true, what works, and what does not work. Use specific examples over abstract generalizations.

Avoid hedging phrases like "arguably," "one could say," or "in a sense." If you are uncertain about details, say so clearly and note what further information is needed. Include dates or versioning information if the guidance is tied to a specific context.

Use markdown features to structure your notes purposefully. Headings should reflect the actual hierarchy of ideas, not be created for cosmetic reasons. Lists should contain related items of similar granularity; if one item is much more complex than others, it probably belongs in its own section with more explanation.

Connect your new or updated notes to related content through wikilinks. If your note depends on understanding concepts in another note, link to it. If another note will likely reference what you are writing, add a backlink comment or update that note to reference your new work.

## Example Vault Navigation

Suppose you are working in a vault with this structure:

```
vault/
├── README.md
├── principles/
│   ├── index.md
│   ├── writing-clarity.md
│   ├── code-organization.md
│   └── testing-strategy.md
├── projects/
│   ├── project-alpha/
│   │   ├── index.md
│   │   ├── architecture.md
│   │   ├── setup.md
│   │   └── decisions/
│   │       ├── decision-001.md
│   │       └── decision-002.md
│   └── project-beta/
│       ├── index.md
│       └── status.md
└── utilities/
    ├── templates.md
    └── tools.md
```

Start at README.md to understand the overall purpose. Then read principles/index.md and projects/index.md to learn what each section covers. If you are working on project-alpha, open projects/project-alpha/index.md first. This file typically notes who owns the project, its current status, and where to find critical documentation. From there, dive into architecture.md or decisions/ as your task requires.

If you need to understand a principle referenced in project-alpha but documented in principles/, follow that link. However, do not spend time reading unrelated princip files unless they also appear in backlinks from project-alpha's notes.

## Quality Bar

- Clear, legible folder and file structure reflecting the vault's actual content organization.
- Files are named descriptively and linked intentionally through wikilinks.
- Dates and version information are present when context-dependent.
- Writing is direct, specific, and free of unnecessary qualification or hedging.
- Index files summarize their sections and point to detailed notes.
- Backlinks are used to show relationships without creating tangled webs.
