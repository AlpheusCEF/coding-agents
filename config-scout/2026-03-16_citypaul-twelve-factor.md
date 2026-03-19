# Config Scout Report: citypaul twelve-factor PR

**Source**: https://github.com/citypaul/.dotfiles/pull/93
**Date**: 2026-03-16
**Previous analysis**: none (first analysis of this specific PR; repo previously analyzed as part of obra-superpowers lineage)
**Local setup version**: 9fa6d51

## Executive Summary

This PR adds a twelve-factor app skill (411 lines) and a twelve-factor-audit agent (342 lines) to citypaul's dotfiles. The content is well-structured and follows the same frontmatter conventions we use. However, the twelve-factor methodology targets deployable services (APIs, workers, web apps), which is outside AlpheusCEF's current focus on code quality disciplines for CLI tools and developer infrastructure. The most valuable takeaway is not the content itself but the **compliance audit agent pattern** — a read-only agent that scans a codebase against a methodology checklist and produces a structured report.

## Source Overview

The PR is a focused addition to citypaul's `.dotfiles` repo (which distributes Claude Code configs via stow/symlinks). It adds:

- **Skill**: `twelve-factor/SKILL.md` — 411 lines covering all 12 factors with TypeScript code examples, brownfield adoption priority order, and testing integration guidance
- **Agent**: `twelve-factor-audit.md` — 342-line audit agent that uses Grep/Glob/Read to scan a Node.js/TypeScript codebase and produce a compliance report with file/line citations
- **Integrations**: `/setup` command updated to detect 12-factor patterns (Dockerfile, Procfile, .env.example, etc.)

Philosophy: practical, TypeScript-first, deployment-readiness focused. Greenfield must follow all factors; brownfield gets a prioritized 5-step adoption order.

## Comparison Matrix

| Dimension | Overlap | External Only | Local Only | Conflicts |
|-----------|---------|---------------|------------|-----------|
| Testing | Both TDD-first | — | Testing anti-patterns, mutation testing, tdd-guardian agent | None |
| Type Safety | Both strict TS | — | py-enforcer, Go coverage | None |
| Architecture | Both have design skills | 12-factor deployment patterns | Hexagonal, DDD, effective-design-overview | None |
| Workflow | Both use agent frontmatter conventions | /setup 12-factor detection | progress-guardian, config-scout, release workflow | None |
| Code Style | Both functional/immutable | — | Systematic debugging, skill-hygiene | None |
| Deployment/Ops | — | Full 12-factor coverage, audit agent | — | None |
| Agent Design | Same frontmatter fields | Compliance audit pattern | Discipline enforcement pipeline, agent memory, orchestration | None |

## Detailed Findings

### Overlaps

**Agent frontmatter conventions**: Both repos use the same fields — `name`, `description`, `tools`, `model`, `color`. The twelve-factor-audit agent uses `model: sonnet` and `tools: Read, Grep, Glob, Bash`, matching our read-heavy agent pattern for analysis-only agents.

**TDD integration**: The skill includes a section on testing 12-factor patterns (config validation, shutdown behavior, backing services) through behavior-driven tests. This aligns with our TDD-first philosophy.

**TypeScript-first examples**: All code examples use strict TypeScript with Zod schemas at boundaries, `Pick<Config>` for minimal surface, no `any` types — consistent with our type safety guidelines.

### Novel in External Config

**1. Compliance Audit Agent Pattern** (Relevance: medium | Effort: moderate)
The twelve-factor-audit agent is a clean example of a "methodology compliance" agent — it takes a known checklist (12 factors), defines Grep/Glob search patterns for each item, includes false-positive guidance per factor, and produces a structured report. This pattern could be templated for other compliance checks (e.g., OWASP, accessibility, licensing). We don't have any audit-style agents — our agents enforce disciplines during development, not assess compliance after the fact.

Where it would go: New agent template pattern in agents.md, or a future audit-focused agent.

**2. Brownfield Adoption Priority Order** (Relevance: low | Effort: trivial)
The skill prescribes a specific order for incremental adoption: Config → Logs → Disposability → Backing Services → Stateless Processes. This "prioritized adoption path" pattern could be useful for any skill that targets existing codebases — e.g., our hexagonal-architecture skill could benefit from a "where to start in a brownfield codebase" section.

Where it would go: hexagonal-architecture skill, if we decide brownfield guidance is needed.

**3. /setup Integration for Pattern Detection** (Relevance: low | Effort: trivial)
The PR updates the `/setup` command to detect 12-factor patterns (Dockerfile, docker-compose, Procfile, .env.example, Kubernetes manifests) and recommend the skill accordingly. This is a good example of skill discoverability — the setup command acts as a router that points users to relevant skills based on what it finds in the project. We don't have a `/setup` command.

Where it would go: N/A unless we add a setup command.

**4. Structured Audit Report Format** (Relevance: medium | Effort: trivial to adopt)
The audit agent produces reports with: factor summary table (compliant/partial/non-compliant), detailed findings with file paths and line numbers, code suggestions, and a prioritized action plan. This is a well-designed output format for any compliance-style agent.

Verbatim format worth noting:
```
| Factor | Status | Key Finding |
|--------|--------|-------------|
| III. Config | ⚠️ Partial | 3 hardcoded values found |
```

Where it would go: Template for future audit agents.

### Local Strengths

AlpheusCEF has significantly more depth in:
- **TDD enforcement**: tdd-guardian with 5-step sacred cycle, spirit-over-ritual, delete-and-restart mandate — none of which the external source has
- **Agent ecosystem maturity**: 8 agents with memory, orchestration sequences, decision framework (agent vs command vs skill), and config-scout for cross-pollination
- **Meta-tooling**: skill-hygiene, config-scout, decision journal — tools for maintaining the instruction set itself
- **Debugging**: systematic-debugging skill with RCA-first mandate
- **Multi-language**: Python (py-enforcer) and Go coverage, not just TypeScript

### Philosophical Differences

No conflicts. The twelve-factor skill operates in a domain (deployment/operations) that our setup doesn't address. If we ever add deployment guidance, this skill's approach (practical, example-heavy, prioritized adoption) would be compatible with our philosophy.

One minor difference in framing: the audit agent uses `model: sonnet` with no `maxTurns` field. Our agents all have `maxTurns` (15-20) since the shanraisshan-full-repo analysis. This is a minor omission in their config, not a philosophical difference.

## Recommendations

### Quick Wins

None. The content is well-crafted but targets a domain outside our current scope.

### Worth Investigating

1. **Compliance audit agent pattern** — The twelve-factor-audit agent is a clean template for "scan a codebase against a methodology checklist." If we ever need audit-style agents (OWASP, accessibility, licensing, dependency hygiene), this pattern — per-factor search patterns, false-positive guidance, structured report — is worth adapting. No action now, but note the pattern exists.

### Nice to Have

1. **Brownfield adoption paths in skills** — The prioritized adoption order (Config → Logs → Disposability → ...) is a useful structural element for any skill targeting existing codebases. Our hexagonal-architecture skill could benefit from a similar "where to start" section for teams migrating incrementally.

2. **Structured report format for agents** — The factor summary table with status indicators and the file/line citation format are clean patterns we could adopt if we build audit-style agents.

### Noted but Skipped

1. **Twelve-factor skill content** — High quality but addresses deployable services (APIs, web apps, workers). AlpheusCEF builds CLI tools and developer infrastructure, not services. If scope ever expands to service development, this would be the first skill to adapt.

2. **Twelve-factor-audit agent** — Same domain mismatch. Well-structured but not applicable to our current work.

3. **`/setup` command integration** — We don't have a `/setup` command and don't need one for our current scope.

## Raw Notes

- This is the first PR-level analysis (vs. full repo). The format works well for focused changes — no batch mode needed, comparison is fast and direct.
- citypaul's repo has grown from 9 to 10 agents and 15 to 16 skills since our last indirect exposure to it. The growth is coherent — each addition has a clear domain.
- The twelve-factor skill at 411 lines is on the larger side. Our skill-hygiene guidelines would probably push for splitting server-specific factors (port binding, concurrency, disposability) into a separate "server-ops" section, keeping the core skill focused on the universally-applicable factors (config, deps, logs, backing services).
- The agent's search patterns are thorough — e.g., for config compliance it greps for `process.env` outside config files, `hardcoded` connection strings, and missing `.env.example`. These patterns demonstrate good audit agent design: know what to search for AND know what false positives look like.
- No `maxTurns` on the new agent, which we'd add per our convention (15 for analysis-only agents).
- No `memory` field, which is appropriate — a compliance audit doesn't need cross-session memory.
