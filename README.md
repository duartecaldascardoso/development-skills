# Development Skills

This repository contains production-ready GitHub Copilot CLI skills.

Each skill follows the required `SKILL.md` format with YAML frontmatter and is organized for direct use in:

- project scope: `.github/skills/...`
- personal scope: `~/.copilot/skills/...`

## Skills

| Skill | Purpose |
| --- | --- |
| `obsidian-codebase` | Navigate and reason about Obsidian vaults structured as codebases where all files are markdown notes. Understand architecture across many files and extract knowledge efficiently. |
| `react-component-architecture` | Build/refactor React components into modular, maintainable folders with clear separation of presentation, logic, types, and tests. |
| `python-ai-agent-engineering` | Build/refactor Python AI agent code into safe, testable modules with explicit prompts, tools, schemas, and runtime wiring. |

## Repository Layout

```text
.github/skills/
├── obsidian-codebase/
│   └── SKILL.md
├── react-component-architecture/
│   └── SKILL.md
└── python-ai-agent-engineering/
    └── SKILL.md
```

## Installing as Personal (Global) Skills

```bash
mkdir -p ~/.copilot/skills
cp -R .github/skills/* ~/.copilot/skills/
```

Then in an active Copilot CLI session:

```text
/skills reload
/skills list
```
