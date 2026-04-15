# Agent skills (engineering workflows)

This repository is a **collection of [Cursor Agent Skills](https://cursor.com/docs)**—markdown playbooks that guide an AI coding assistant through real engineering workflows. Each folder is one skill: a `SKILL.md` file (with optional YAML front matter) plus any supporting scripts or reference docs.

Skills here are organized by **phase of work**—from refining an idea through spec, planning, implementation, testing, review, CI, docs, and shipping—so you can attach the right process to the task at hand.

## What’s inside

| Folder | Focus |
|--------|--------|
| `using-agent-skills` | Meta-skill: how to discover and invoke the right skill for a task |
| `idea-refine` | Shaping vague ideas before building |
| `spec-driven-development` | Specs and requirements-led development |
| `planning-and-task-breakdown` | Turning plans into actionable tasks |
| `incremental-implementation` | Steady, safe implementation habits |
| `frontend-ui-engineering` | UI and frontend work |
| `api-and-interface-design` | APIs and interfaces |
| `context-engineering` | Curating agent context (rules, docs, code) |
| `source-driven-development` | Implementation aligned with source-of-truth docs |
| `test-driven-development` | Tests-first and testing practice |
| `browser-testing-with-devtools` | Browser-based testing with devtools |
| `debugging-and-error-recovery` | Debugging and recovery |
| `code-review-and-quality` | Review and quality |
| `code-simplification` | Simplifying and tightening code |
| `security-and-hardening` | Security considerations |
| `performance-optimization` | Performance work |
| `git-workflow-and-versioning` | Branches, commits, versioning |
| `ci-cd-and-automation` | CI/CD and automation |
| `documentation-and-adrs` | Documentation and ADRs |
| `deprecation-and-migration` | Deprecations and migrations |
| `shipping-and-launch` | Shipping and launch |

*(Each skill’s `SKILL.md` is the source of truth for scope and when to use it.)*

## How to use these skills

1. **In Cursor**: Configure your environment so these skills are on the [skills path](https://cursor.com/docs) your agent reads (for example, symlink or copy this repo into the folder Cursor expects for user or project skills).
2. **Pick a skill** that matches your current phase (or start from `using-agent-skills` for a discovery map).
3. **Let the agent follow** the steps and constraints in that skill’s `SKILL.md` for the session or task.

## Layout

```
<skill-name>/
  SKILL.md          # Main instructions (often with YAML front matter)
  ...               # Optional scripts, examples, or reference markdown
```

## License

Add a `LICENSE` file if you publish this repo publicly; until then, treat contents as yours to use and share as you prefer.
