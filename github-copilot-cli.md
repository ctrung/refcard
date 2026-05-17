
# Agents

## Définition

Un agent est une version spécialisée de Copilot.

Pour ajouter un agent : créer un fichier avec une extension `.agent.md` et contenant un YAML frontmatter (metadata) et un contenu markdown.

Exemple :
```
---
name: my-reviewer
description: Code reviewer focused on bugs and security issues
---

# Code Reviewer

You are a code reviewer focused on finding bugs and security issues.

When reviewing code, always check for:
- SQL injection vulnerabilities
- Missing error handling
- Hardcoded secrets
```

Format YAML frontmatter

| Property | Required | Description |
|----------|----------|-------------|
| `name` | No | Display name (defaults to filename) |
| `description` | **Yes** | What the agent does - helps Copilot understand when to suggest it |
| `tools` | No | List of allowed tools (omit = all tools available). See tool aliases below. |
| `target` | No | Limit to `vscode` or `github-copilot` only |

List complète des options supportées : https://docs.github.com/copilot/reference/custom-agents-configuration


Où ?


| Location | Scope | Best For |
|----------|-------|----------|
| `.` | Local-specific | Local agents specific to the project (eg. for experimenting) |
| `.github/agents/` | Project-specific | Team-shared agents with project conventions |
| `~/.copilot/agents/` | Global (all projects) | Personal agents you use everywhere |


Comment les appeler ?

```
copilot
> /agent
```

```
copilot --agent python-reviewer
> Review @samples/book-app-project/books.py
```


## References

Doc : https://github.com/github/copilot-cli-for-beginners/blob/main/04-agents-custom-instructions/README.md

Template de fichiers agent : https://github.com/github/awesome-copilot



# Instruction files

## Définition

Contrairement aux agents que l'on active à la demande, les instructions files sont automatiquement pris en compte par Copilot.


Où ?

| File | Scope | Notes |
|------|-------|-------|
| `AGENTS.md` | Project root or nested | **Cross-platform standard** - works with Copilot and other AI assistants |
| `.github/copilot-instructions.md` | Project | GitHub Copilot specific |
| `.github/instructions/*.instructions.md` | Project | Granular, topic-specific instructions |
| `CLAUDE.md`, `GEMINI.md` | Project root | Supported for compatibility |


Exemple : 
```
.github/
└── instructions/
    ├── python-standards.instructions.md
    ├── security-checklist.instructions.md
    └── api-design.instructions.md
```

Comment désactiver ?

Les instructions files étant automatiquement activés par défaut, on peut les désactiver avec une option.

```
copilot --no-custom-instructions
```



## References

Doc : https://github.com/github/copilot-cli-for-beginners/blob/main/04-agents-custom-instructions/README.md#instruction-file-formats

Template de fichiers instruction : https://github.com/github/awesome-copilot
