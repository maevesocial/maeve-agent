# Contributing

This repository distributes Maeve's social-workflow skill and connection metadata to AI clients. The skill source is `skills/maeve-social-scheduler/`; installed plugin caches are build/distribution artifacts, not sources to edit.

These are the instructions for people and coding agents working here. The Claude plugin directory packages the repository root and refuses any `AGENTS.md` or `CLAUDE.md`, `.claude/skills`, `.claude/rules`, package manager files, scripts, and files that are not Markdown, text, JSON, YAML or an image, so keep those out. Agents do not load this file by themselves: read it before editing. Name no environment variable that holds a credential in the README or the skill; link to the maeve-cli documentation instead.

Keep changes scoped to the requested skill or packaging behavior. Use concise triggers and task-specific references, preserve publication and retry safeguards, and do not run live publishing operations to test instruction edits. Commit only when asked. A commit request lands the work on local `main`: from a T3 Code worktree, commit to its branch and merge it into `main` in the same turn; pushing or publishing a plugin requires an explicit request. Keep credentials and signed URLs out of output and tracked files.

The backend maintains matching copies in `../maeve-backend/agent-skills/maeve-social-scheduler/`, `.agents/skills/maeve-social-scheduler/`, and `.claude/skills/maeve-social-scheduler/`. Keep those copies aligned when changing the shared skill. `pnpm skills:check` in the backend checks them against this repository.

Check edited skill frontmatter, reference links, and mirror consistency. Client manifests should continue pointing to the existing skill and MCP configuration paths; do not change versions or connection settings as a side effect of editing instructions.
