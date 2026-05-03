---
name: skill-studio-en
description: "Use this skill when users want to visualize, read, understand, inspect, edit, maintain, or save changes to Verdent skills in a fully English interface. It creates a local HTML Skill Studio for Skill Studio, Skill Reader, skill visualization, reading skills, understanding skills, editing skills, maintaining skills, saving skill changes, viewing SKILL.md, skill workflow analysis, trigger analysis, or simulating how an Agent will execute a skill."
---

# Skill Studio English

## Overview

Create a local browser-based Skill Studio that turns Verdent skills into readable, inspectable, editable software assets with an English-only interface. Use it to help users understand what a skill does, how an Agent is likely to execute it, which files it depends on, and how proposed edits should be staged for Agent-reviewed saving.

Do not write user edits directly from the browser into the real skill. Stage changes as JSON, then use Agent tools to review the diff and apply the final file edits.

## Core Workflows

### Render One Skill Directly

When the user asks to visualize a specific skill, do not make them choose from a generic picker. Generate a polished standalone HTML page for that exact skill:

```bash
python3 ~/.verdent/skills/skill-studio-en/scripts/skill_studio.py render <skill-name-or-path> --open
```

The generated HTML must show the selected skill directly, including purpose, trigger surface, execution map, editable files, full skill file tree, complete rendered source for every readable text file, and an editing area.

The standalone HTML is editable. Its **Export changes for Agent** button downloads a staged JSON bundle containing changed file contents. Tell the user to return to Agent with:

```text
I have edited my skill; please save it.
```

If the Agent cannot locate the downloaded staged JSON, ask the user for the downloaded file path, usually under `~/Downloads`.

Render pages as English-first reading experiences:

- Use English labels, navigation, instructions, status messages, and generated summaries.
- Keep components compact; avoid oversized hero cards, huge metrics, and noisy UI chrome.
- Keep the sidebar identity card minimal: only the avatar or mark and skill name, arranged on one horizontal line.
- Do not render long descriptions or full trigger sentences as giant pills; extract short trigger keywords and show long descriptions as readable summaries.
- Render execution maps as English explanatory steps, not raw extracted lines from `SKILL.md`.
- Explain what each file does in plain English next to the file path.
- Omit risk highlights from the default visual page unless the user explicitly asks for security or risk review.
- Do not include a separate reading-outline or document-structure section in the default page; the rendered Markdown source is enough.
- Render Markdown files as formatted reading content, not raw Markdown text.
- Still include full raw content in the editor so the user can modify exact source.
- Show the full skill structure and the complete rendered source for every readable text file.

### Open the Generic Skill Studio

Run the bundled server from the skill directory:

```bash
python3 ~/.verdent/skills/skill-studio-en/scripts/skill_studio.py serve
```

If a project-local skill directory is required, pass it explicitly:

```bash
python3 ~/.verdent/skills/skill-studio-en/scripts/skill_studio.py serve --skill-root ./.verdent/skills
```

Use the generic Studio only when the user wants to browse multiple skills. The web UI lists discovered skills, renders `SKILL.md`, builds an execution map from headings and workflow language, highlights risks, shows resource files, and provides an editor for text files.

### Help the User Read a Skill

Use the Studio to explain:

1. Purpose: what the skill is for.
2. Trigger surface: which user phrases should invoke it.
3. Execution map: what the Agent will probably read, decide, and run.
4. Resources: scripts, references, assets, and generated artifacts.
5. Risk areas: shell commands, file writes, external services, authentication, destructive language, or large context loads.
6. Maintenance opportunities: unclear triggers, missing examples, duplicated instructions, brittle commands, or oversized sections.

### Stage Browser Edits

In the HTML UI, let the user edit `SKILL.md` or other text files and click **Stage changes for Agent**. The browser posts changes to the local server, which writes a staged JSON bundle under:

```text
.verdent/skill-studio-en/staged/
```

The UI then displays this instruction for the user:

```text
I have edited my skill; please save it.
```

### Save Staged Changes

When the user returns with “I have edited my skill; please save it” or a similar request:

1. Locate the newest staged bundle:

```bash
python3 ~/.verdent/skills/skill-studio-en/scripts/skill_studio.py latest
```

2. Inspect the staged diff:

```bash
python3 ~/.verdent/skills/skill-studio-en/scripts/skill_studio.py inspect-stage <stage-json-path>
```

3. Read the staged JSON if exact content is needed.
4. Explain the changed files and important diff risks to the user.
5. Apply the changes to the real skill using normal Agent file-edit tools.
6. Validate the skill with the skill-creator validator or packaging script when available.

Never blindly write staged content into the skill. Treat the staged JSON as a proposed patch from the human editor.

## Editing Standards

Preserve skill quality while applying edits:

- Keep `SKILL.md` concise and procedural.
- Keep frontmatter valid YAML with `name` and `description`.
- Keep the description trigger-rich but under 1024 characters.
- Prefer reusable scripts for deterministic repeated work.
- Move long examples, schemas, or methodology into `references/`.
- Avoid adding README, CHANGELOG, or setup docs unless they are core to execution.
- Preserve existing resource paths unless deliberately refactoring.
- Surface dangerous commands or irreversible actions before saving.

## Recommended User Prompts

Use this skill for prompts like:

- “Visualize this skill.”
- “Create a skill reader.”
- “Open Skill Studio.”
- “Help me understand how this skill runs.”
- “Help me edit, modify, or maintain this skill.”
- “I have edited my skill; please save it.”
- “Turn my skill into an HTML page for reading.”
- “Show the entire skill structure and full source.”

## Scripts

Use `scripts/skill_studio.py`:

- `serve`: start the local HTML Skill Studio.
- `render <skill>`: generate one specific skill as a polished editable standalone HTML file.
- `latest`: print the newest staged change bundle.
- `inspect-stage <path>`: summarize staged files and print unified diffs.

The server is local-only by default and binds to `127.0.0.1`.