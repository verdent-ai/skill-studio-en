# Skill Studio English

Skill Studio English is a Verdent skill for visualizing, reading, inspecting, editing, and maintaining Verdent skills through a local English-only HTML interface.

It helps you see what a skill does, when it triggers, how an Agent is likely to execute it, which files it depends on, and how edits should be staged for Agent review before being written back to the real skill.

## What it includes

- `SKILL.md` — the skill instructions and trigger description.
- `scripts/skill_studio.py` — the local Skill Studio renderer and editor server.

## Usage

Render one skill directly as a standalone editable HTML file:

```bash
python3 ~/.verdent/skills/skill-studio-en/scripts/skill_studio.py render <skill-name-or-path> --open
```

Open the generic local Skill Studio browser:

```bash
python3 ~/.verdent/skills/skill-studio-en/scripts/skill_studio.py serve --open
```

Inspect the newest staged edit bundle:

```bash
python3 ~/.verdent/skills/skill-studio-en/scripts/skill_studio.py latest
python3 ~/.verdent/skills/skill-studio-en/scripts/skill_studio.py inspect-stage <stage-json-path>
```

## Safety model

The browser UI does not write edits directly into the real skill. It stages proposed changes as JSON. An Agent should inspect the staged diff, explain relevant risks, and then apply the final file edits using normal code-editing tools.
