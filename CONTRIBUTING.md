# Contributing

Each skill lives in its own directory at the repo root using a kebab-case slug. The Claude Code / skills.sh invocation is declared in YAML frontmatter, colon-namespaced under `syndly:`.

## Layout

```
.
├── README.md
├── LICENSE
├── CONTRIBUTING.md
└── skills/
    └── <slug>/
        └── SKILL.md
```

## Frontmatter

Every `SKILL.md` starts with a YAML frontmatter block. The `name` field is the colon-namespaced invocation the agent uses:

```yaml
---
name: syndly:<role>
description: One-line summary of what the skill does.
---

# Title

Body content — agent-facing instructions, written in the voice the agent
should use while following the skill.
```

### Conventions

- **Slug** must match the parent directory name exactly. The directory `skills/folder-setup/SKILL.md` pairs with frontmatter `name: syndly:setup` (different convention — the slug is the directory name, the name is the invocation).
- **Invocation** is always colon-namespaced under `syndly:`. Never use a leading `/` in frontmatter; that's how the user invokes it from chat, not how you declare it.
- **Description** must be a single complete sentence. It surfaces in the agent's skill discovery UI.
- **Body** is markdown. Use imperative voice ("Check that X exists", "Ask the user for Y", "On approval, call tool Z"). Where the skill uses destructive MCP tools, gate the call behind a clear user-confirmation step.

## Adding a new skill

1. Create `skills/<your-slug>/SKILL.md`.
2. Add the `name`, `description`, and body per the conventions above.
3. Add a row to the table in `README.md` describing the skill.
4. Open a PR. The Slice 6B sync in the socialqueue repo will pick up the new entry automatically on the next plan cycle.
