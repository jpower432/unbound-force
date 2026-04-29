# @unbound-force/workflows

Spec-driven development workflows for AI-assisted
software engineering. Two tiers:

- **Speckit** (strategic): 9-phase pipeline for features
  with 3+ user stories
- **OpenSpec** (tactical): Lightweight workflow for bug
  fixes and small changes

## Install

```bash
opkg install gh@unbound-force/packages/workflows
```

Or add to your project's `openpackage.yml`:

```yaml
dependencies:
- name: "@unbound-force/workflows"
  version: ^0.1.0
```

This also installs `@unbound-force/review-council` as a
dependency (used for spec review and code review gates).

## Speckit Commands (Strategic)

Full pipeline from idea to implementation:

| Command | Phase | Purpose |
|:---|:---|:---|
| `/speckit.constitution` | 1 | Create/update project constitution |
| `/speckit.specify` | 2 | Create feature specification |
| `/speckit.clarify` | 3 | Reduce spec ambiguity |
| `/speckit.plan` | 4 | Generate implementation plan |
| `/speckit.tasks` | 5 | Break plan into ordered tasks |
| `/speckit.analyze` | 6 | Cross-artifact consistency check |
| `/speckit.checklist` | 7 | Quality validation |
| `/speckit.implement` | 8 | Execute tasks |
| `/speckit.taskstoissues` | 9 | Convert tasks to GitHub Issues |
| `/speckit.testreview` | -- | Testability analysis (read-only) |

## OpenSpec Commands (Tactical)

Lightweight workflow for small changes:

| Command | Purpose |
|:---|:---|
| `/opsx-propose` | Create change proposal with plan and tasks |
| `/opsx-explore` | Think through ideas (read-only) |
| `/opsx-apply` | Implement tasks from a change |
| `/opsx-archive` | Archive a completed change |

## When to Use Which

| Situation | Workflow |
|:---|:---|
| 3+ user stories, cross-repo | Speckit (`/speckit.specify`) |
| Bug fix, minor enhancement | OpenSpec (`/opsx-propose`) |
| Not sure | Start with OpenSpec, escalate if scope grows |

## Constitution Check

The `/constitution-check` command validates a project
constitution against the Unbound Force org constitution.
Returns a structured ALIGNED/NON-ALIGNED verdict with
per-principle findings.

## Platforms

Auto-converts to all OpenPackage-supported platforms:
OpenCode, Cursor, Claude Code, Gemini CLI, and 30+ others.

## License

Apache-2.0
