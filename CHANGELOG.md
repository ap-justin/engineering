# Changelog

Semver-ish: new agent/capability → minor, prompt fix → patch, orchestration-contract break → major.

## v0.2.0 — grilling awareness
- `engineering-team` PM now knows the `/grilling` skill and uses judgment (Step 2.5): for non-trivial work it grills, or asks the user whether to, before Plan/build. Trivial edits always skip. Not a hard gate.
- Placement: greenfield after scoping; brownfield after `Explore` (informed, not blind). Grill output supersedes design-director's single clarifying question.

## v0.1.0 — initial team
- Lead: `engineering-team` skill (main-thread PM) with greenfield + brownfield modes, stack detection & routing, gap recommendation (mint-a-specialist), official-sources enforcement.
- Specialists: `design-director`, `sveltekit-builder`, `react-router-builder`, `postgres-architect`, `taste-reviewer`, `code-reviewer`.
- `SOURCES.md` — official MCP/skill/plugin registry (Context7, Svelte MCP, Better Auth MCP, `vercel:*`/`sanity:*` skills, Cloudflare MCP).
- Reuses built-ins (`Explore`, `Plan`) and skills (`/code-review`, `/tdd`, `/diagnosing-bugs`, `/verify`, `/run`).
- Repo symlinked into `~/.claude/agents` and `~/.claude/skills/engineering-team`.
