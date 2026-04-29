# Plan: Migrate Scaffold Distribution to OpenPackage

**Status:** Proposal
**Author:** James Power
**Date:** 2026-04-29

## BLUF

Replace the monolithic `uf init` scaffold with 4
composable OpenPackage packages. Consuming repos pin
versions independently. Contributors install only
what they need. Assets auto-convert to Cursor, Claude
Code, Gemini CLI, and 37 other platforms.

## Problem

| Current State | Impact |
|:---|:---|
| `uf init` deploys 33 files as a single unit | Teams can't adopt review-only or spec-only |
| Go binary required to scaffold | Barrier to entry for non-Go teams |
| Single-platform (OpenCode) | Teams using Cursor/Claude Code must manually adapt |
| Version tied to binary release | Convention pack fix requires full binary release |
| User-owned file logic hardcoded in Go | Custom rules management is bespoke |

## Proposed Architecture

4 independently versioned packages with dependency
resolution:

```
@unbound-force/convention-packs    (rules only)
        ^
@unbound-force/review-council      (code review)
        ^
@unbound-force/workflows           (spec workflows)
        ^
@unbound-force/heroes              (full swarm, pulls all)
```

Consuming repos declare what they need:

```yaml
# Review-only team
dependencies:
- name: "@unbound-force/review-council"
  version: ^2.0.0

# Full swarm team
dependencies:
- name: "@unbound-force/heroes"
  version: ^2.0.0
```

## Package Definitions

### `@unbound-force/convention-packs`

Language-specific coding standards enforced by
review agents.

| Content | Files |
|:---|:---|
| Go rules | `go.md`, `go-custom.md` |
| TypeScript rules | `typescript.md`, `typescript-custom.md` |
| Default rules | `default.md`, `default-custom.md` |
| Content rules | `content.md`, `content-custom.md` |
| Severity rules | `severity.md` |

**Dependencies:** None

### `@unbound-force/review-council`

AI code review via 5+ parallel reviewer personas.

| Content | Files |
|:---|:---|
| Review personas | `divisor-guard.md`, `divisor-architect.md`, `divisor-adversary.md`, `divisor-sre.md`, `divisor-testing.md`, `divisor-curator.md` |
| Content personas | `divisor-scribe.md`, `divisor-herald.md`, `divisor-envoy.md` |
| Commands | `review-council.md`, `review-pr.md` |

**Dependencies:** `@unbound-force/convention-packs`

### `@unbound-force/workflows`

Spec-driven development workflows (Speckit strategic +
OpenSpec tactical).

| Content | Files |
|:---|:---|
| Speckit commands | `speckit.specify.md`, `speckit.plan.md`, `speckit.tasks.md`, `speckit.implement.md`, `speckit.clarify.md`, `speckit.analyze.md`, `speckit.checklist.md`, `speckit.constitution.md`, `speckit.taskstoissues.md` |
| OpenSpec commands | `opsx-propose.md`, `opsx-apply.md`, `opsx-explore.md`, `opsx-archive.md` |
| Constitution agent | `constitution-check.md` |
| Constitution command | `constitution-check.md` |

**Dependencies:** `@unbound-force/convention-packs`

### `@unbound-force/heroes`

Full agent swarm with all heroes, workflow
orchestration, and autonomous pipeline.

| Content | Files |
|:---|:---|
| Hero agents | `cobalt-crush-dev.md`, `muti-mind-po.md`, `mx-f-coach.md`, `gaze-reporter.md`, `gaze-test-generator.md` |
| Autonomous pipeline | `unleash.md`, `forge.md`, `forge-status.md`, `finale.md`, `handoff.md`, `inbox.md` |
| Muti-Mind commands | 8 backlog/sync commands |
| Workflow commands | `workflow-start.md`, `workflow-status.md`, `workflow-list.md`, `workflow-advance.md`, `workflow-seed.md` |
| Gaze commands | `gaze.md`, `gaze-fix.md` |
| Swarm skill | `SKILL.md` |
| MCP config | Dewey + Replicator server entries |

**Dependencies:** `@unbound-force/review-council`, `@unbound-force/workflows`

## What Stays in the `uf` Binary

The CLI binary remains for capabilities that aren't
distributable as Markdown files:

| Command | Why It Stays |
|:---|:---|
| `uf doctor` | Runtime environment checks (Go logic) |
| `uf setup` | Tool installation (subprocess orchestration) |
| `uf sandbox` | Container lifecycle management |
| `uf gateway` | LLM reverse proxy (Go HTTP server) |
| `uf config` | Config file management |

The binary becomes optional infrastructure. The agent
assets are the product; `uf` is a convenience.

## What Gets Removed from the Binary

| Current | After |
|:---|:---|
| `uf init` (scaffold engine) | Replaced by `opkg install` |
| `uf init --divisor` | Replaced by installing `review-council` only |
| `uf init --lang` | Replaced by installing language-specific convention pack |
| `internal/scaffold/` package | Archived or reduced to `uf doctor`/`uf setup` support |
| `internal/scaffold/assets/` (33 embedded files) | Moved to OpenPackage package repos |
| Drift detection tests | Replaced by OpenPackage version sync |

## Platform Reach

OpenPackage auto-converts files for all detected
platforms in the consuming repo. Current `uf init`
targets OpenCode only.

| Platform | Current | After |
|:---|:---|:---|
| OpenCode | Yes | Yes |
| Cursor | No | Yes (auto) |
| Claude Code | No | Yes (auto) |
| Gemini CLI | No | Yes (auto) |
| Codex | No | Yes (auto) |
| Windsurf | No | Yes (auto) |
| Warp | No | Yes (auto) |
| 33+ others | No | Yes (auto) |

## Milestones

### M1: Package Structure

**Goal:** Create the 4 package repos with manifests
and content files extracted from `internal/scaffold/assets/`.

| Checkpoint | Activity |
|:---|:---|
| 1.1 | Create `unbound-force/packages` repo (monorepo with 4 package dirs) |
| 1.2 | Extract agent, command, and rule files from scaffold assets into package structure |
| 1.3 | Write `openpackage.yml` manifests with dependencies and semver |
| 1.4 | Add platform-specific frontmatter overrides where needed (OpenCode `mode: subagent`, Cursor equivalents) |
| 1.5 | Validate with `opkg install --local` in a test repo |

### M2: Cross-Platform Validation

**Goal:** Confirm packages install correctly to
Cursor, Claude Code, and OpenCode.

| Checkpoint | Activity |
|:---|:---|
| 2.1 | Install `review-council` package in a Cursor project, verify agent discovery |
| 2.2 | Install `review-council` package in a Claude Code project, verify slash commands work |
| 2.3 | Install `heroes` package in an OpenCode project, verify full workflow |
| 2.4 | Document any platform-specific frontmatter overrides required |

### M3: Registry Publication

**Goal:** Publish packages to OpenPackage registry
so consuming repos can `opkg install` by name.

| Checkpoint | Activity |
|:---|:---|
| 3.1 | Publish `@unbound-force/convention-packs` v1.0.0 |
| 3.2 | Publish `@unbound-force/review-council` v1.0.0 |
| 3.3 | Publish `@unbound-force/workflows` v1.0.0 |
| 3.4 | Publish `@unbound-force/heroes` v1.0.0 |
| 3.5 | Test install from registry in a clean repo |

### M4: Binary Reduction

**Goal:** Remove scaffold engine from the `uf`
binary. Retain `doctor`, `setup`, `sandbox`,
`gateway`, `config`.

| Checkpoint | Activity |
|:---|:---|
| 4.1 | Remove `internal/scaffold/assets/` embedded files |
| 4.2 | Remove `uf init` command (or repurpose as `opkg install @unbound-force/heroes` wrapper) |
| 4.3 | Update `uf doctor` to check OpenPackage installation instead of scaffold file presence |
| 4.4 | Update `uf setup` to install `opkg` as part of tool chain |
| 4.5 | Update QUICKSTART.md and USAGE.md |

### M5: Consuming Repo Migration

**Goal:** Migrate existing repos (`gaze`, `website`,
`dewey`) to OpenPackage-based installation.

| Checkpoint | Activity |
|:---|:---|
| 5.1 | Add `openpackage.yml` to each consuming repo with version-pinned dependencies |
| 5.2 | Run `opkg install` and verify file placement matches previous `uf init` output |
| 5.3 | Remove stale scaffold markers from existing files |
| 5.4 | Update CI to run `opkg install` instead of `uf init` |

## Risks

| Risk | Mitigation |
|:---|:---|
| OpenPackage is v0.11 (pre-1.0) | Low blast radius -- packages are Markdown files. If `opkg` breaks, files are already installed and functional. Fallback: copy files manually. |
| User-owned file preservation | OpenPackage tracks installed files in `openpackage.index.yml`. Custom files (`*-custom.md`) not in the index are never touched. Verify in M2. |
| MCP config merging | `mcp.jsonc` per-package may conflict when multiple packages declare servers. Test merge behavior in M2. |
| Platform frontmatter divergence | Some platforms need different frontmatter fields (`mode`, `tools`). OpenPackage supports per-platform overrides natively. Document in M1.4. |

## Decision Required

1. **Monorepo vs multi-repo for packages?**
   Recommendation: monorepo (`unbound-force/packages`)
   with 4 dirs. Simpler to maintain, single CI, atomic
   cross-package version bumps.

2. **Keep `uf init` as a thin wrapper?**
   Option A: Remove entirely, document `opkg install`.
   Option B: Keep as `uf init` → `opkg install @unbound-force/heroes` delegate.
   Recommendation: Option B for backward compatibility.

3. **Versioning strategy?**
   Option A: All 4 packages share a version (simpler).
   Option B: Independent versions (more flexible).
   Recommendation: Option A initially, split later if
   release cadences diverge.
