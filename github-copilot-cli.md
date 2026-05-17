
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


Comment activer ?

```
copilot
> /agent
```

```
copilot --agent python-reviewer
> Review @samples/book-app-project/books.py
```

Comment désactiver ?

Désactivé par défaut puisqu'activable à la demande.

## References

Docs : \
https://github.com/github/copilot-cli-for-beginners/blob/main/04-agents-custom-instructions/README.md \
https://agents.md/

Agents développés par la communauté : \
https://awesome-copilot.github.com/agents/



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

Comment activer ?

Les instructions files sont automatiquement activés par défaut.

Comment désactiver ?

```
copilot --no-custom-instructions
```

## References

Doc : https://github.com/github/copilot-cli-for-beginners/blob/main/04-agents-custom-instructions/README.md#instruction-file-formats

Template de fichiers instruction : https://github.com/github/awesome-copilot

# Skills

## Definition

Comme les instruction files, les skills sont aussi chargés automatiquement en fonction du contenu du prompt. Les instruction files et agent files définissent le quoi tandis que les skills définissent le comment et contiennent des instructions à la réalisation d'une tâche bien spécifique. Les deux mécanismes sont complémentaires.


Utiliser la commande `/skills` pour gérér :

| Command | What It Does |
|---------|--------------|
| `/skills list` | Show all installed skills |
| `/skills info <name>` | Get details about a specific skill |
| `/skills add <name>` | Enable a skill (from a repository or marketplace) |
| `/skills remove <name>` | Disable or uninstall a skill |
| `/skills reload` | Reload skills after editing SKILL.md files |

NB : Pas besoin d'activer une skill, celle ci est automatiquement activée si le prompt correspond à sa description.

Où ?

| Location | Scope |
|----------|-------|
| `.github/skills/` | Project-specific (shared with team via git) |
| `~/.copilot/skills/` | User-specific (your personal skills) |


Structure ?

Each skill lives in its own folder with a `SKILL.md` file. You can optionally include scripts, examples, or other resources:

```
.github/skills/
└── my-skill/
    ├── SKILL.md           # Required: Skill definition and instructions
    ├── examples/          # Optional: Example files Copilot can reference
    │   └── sample.py
    └── scripts/           # Optional: Scripts the skill can use
        └── validate.sh
```

> 💡 **Tip**: The directory name should match the `name` in your SKILL.md frontmatter (lowercase with hyphens).

Format ?

Skills use a simple markdown format with YAML frontmatter:

```markdown
---
name: code-checklist
description: Comprehensive code quality checklist with security, performance, and maintainability checks
license: MIT
---

# Code Checklist

When checking code, look for:

## Security
- SQL injection vulnerabilities
- XSS vulnerabilities
- Authentication/authorization issues
- Sensitive data exposure

## Performance
- N+1 query problems (running one query per item instead of one query for all items)
- Unnecessary loops or computations
- Memory leaks
- Blocking operations

## Maintainability
- Function length (flag functions > 50 lines)
- Code duplication
- Missing error handling
- Unclear naming

## Output Format
Provide issues as a numbered list with severity:
- [CRITICAL] - Must fix before merge
- [HIGH] - Should fix before merge
- [MEDIUM] - Should address soon
- [LOW] - Nice to have
```

**YAML Properties:**

| Property | Required | Description |
|----------|----------|-------------|
| `name` | **Yes** | Unique identifier (lowercase, hyphens for spaces) |
| `description` | **Yes** | What the skill does and when Copilot should use it |
| `license` | No | License that applies to this skill |

> 📖 **Official docs**: [About Agent Skills](https://docs.github.com/copilot/concepts/agents/about-agent-skills)

La description de la skill est clé. C'est sur elle que se base Copilot pour décider s'il doit ou non activer celle ci. 

Comment activer ?

Automatiquement activé par défaut. Il est aussi possible d'utiliser une slash commande n'importe où dans le prompt pour forcer son utilisation. Il est même possible d'utiliser plusieurs slash commandes pour tout combiner dans une seule réponse.

```
> /generate-tests Create tests for the user authentication module

> Check @samples/book-app-project/book_app.py with /code-checklist and also run /generate-tests for it
```

Installer des skills peut se faire plus facilement au travers de la commande `/plugin` de copilot ou du CLI github (`gh`).

Un plugin peut contenir des skills, des agents et des configurations MCP fonctionnant ensemble.

Exemple `/plugin` :
```
copilot

> /plugin list
# Shows installed plugins

> /plugin marketplace
# Browse available plugins

> /plugin install <plugin-name>
# Install a plugin from the marketplace

copilot plugin marketplace update # keep your local plugin catalog current
```


## References

Docs : https://github.com/github/copilot-cli-for-beginners/tree/main/05-skills

Skills dévéloppées par la communauté :\
https://awesome-copilot.github.com/skills/\
https://www.skills.sh/\

# Formation en ligne

GitHub Copilot CLI for Beginners : https://github.com/github/copilot-cli-for-beginners
