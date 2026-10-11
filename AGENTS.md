# Maeve agent plugin

This repository distributes Maeve's social-workflow skill and connection metadata to AI clients. The skill source is `skills/maeve-social-scheduler/`; installed plugin caches are build/distribution artifacts, not sources to edit.

Keep changes scoped to the requested skill or packaging behavior. Use concise triggers and task-specific references, preserve publication and retry safeguards, and do not run live publishing operations to test instruction edits. Commit only when asked, directly to local `main`; pushing or publishing a plugin requires an explicit request. Keep credentials and signed URLs out of output and tracked files.

The backend maintains matching copies in `../maeve-backend/agent-skills/maeve-social-scheduler/`, `.agents/skills/maeve-social-scheduler/`, and `.claude/skills/maeve-social-scheduler/`. Keep those copies aligned when changing the shared skill. `pnpm skills:check` in the backend checks them against this repository.

Check edited skill frontmatter, reference links, and mirror consistency. Client manifests should continue pointing to the existing skill and MCP configuration paths; do not change versions or connection settings as a side effect of editing instructions.
