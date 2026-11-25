# Safety policy

Use this policy before Maeve mutations. Authorization already given for the same action, targets and external effect remains valid. Ask only when it is missing or those details change.

## Contents

- Agent rules
- Confirmation required
- CLI-enforced `--yes`
- Credential handling
- External visibility
- Generated media
- Bulk actions

## Agent rules

- Prefer drafts. Omit `intent` or set `intent: "draft"` for new content unless the user explicitly asks to schedule or publish.
- Never publish immediately without explicit publish-now intent in the current task. Ambiguous wording such as "post this" is not enough.
- Never schedule without an explicit date and time in the current task or user-provided source of truth. Confirm the timezone when the time has no offset and the workspace timezone may not be the one the user means.
- Discover before mutating: auth, workspace, integration, capabilities, dynamic options for any capability field with `optionKey`, and relevant current state.
- Upload local media through Maeve before attaching it to posts.
- Never use `--yes` until the current task includes explicit confirmation for the exact action.
- Never send inbox replies, moderation actions, approval notifications, client review emails, or bulk read-all without confirmation.
- Never create or enable an inbox auto-reply rule without confirming the rule, account, audience, reply content, and expected external effect.
- Never add a task comment containing member mentions without confirming the mentioned recipients and notification effect.
- Never delete content, failed inbox messages, grid items, media, media folders or labels, or hashtag groups without confirmation.

## Confirmation required

Ensure the user has authorized the action and target before:

- `content:create` with `intent: "publish_now"`, `content:publish`, `content:published-caption`, `content:retry`, `content:resolve-publishing-result`, `content:revert-to-draft`, `content:delete`.
- `content:recurring-occurrence:cancel`, `content:recurring-series:cancel`.
- `content:schedule` if the requested date/time/timezone or target integration is ambiguous.
- `articles:send-to-x-drafts` and MCP operation `articles.send_to_x_drafts`.
- `reviews:create` with `sendNow`, `reviews:submit`, `reviews:send`, `reviews:remind`, `reviews:cancel`, `reviews:items:remove`, `reviews:items:comment`, and any `reviews:items:decision`, `:decisions`, `:override` or `:revoke`.
- MCP operation `review.request` when it sends a client stage at once, and `review.withdraw` or `review.remove_content` through `maeve_write`.
- Paid ads changes and creation: `ads:*` writes and the matching `ads.*` MCP operations. Creation still needs the user's approval on the Maeve review page, or their agreement in chat where the response offers `chatConfirmation`.
- `inbox:reply`, `inbox:moderate`, `inbox:retry-message`, `inbox:delete-failed`, `inbox:read-all`, `inbox:resolve-all`, `inbox:tags:delete`.
- MCP operation `inbox_automation.create_rule` with `enabled: true` requires `inbox_automation_enable:new:<integrationId>`. MCP operation `inbox_automation.update_rule` with `enabled: true` requires `inbox_automation_enable:<ruleId>`. Execute either through `maeve_write`. Neither action supports dangerous auto-confirm.
- MCP operation `task.create_comment` through `maeve_write` when its content mentions workspace members.
- `tasks:delete` and `tasks:checklist:delete`.
- `workbench:rows:remove`.
- `media:delete`, `media:delete-permanent`, `media:bulk-delete`, `media:bulk-delete-forever`, `media:bulk-move`, `media:bulk-label`, `media:bulk-unlabel`.
- `media:folders:delete`, `media:labels:delete`, `media:label-groups:delete`, `hashtags:delete`.
- `integrations:pinterest:create-board`, `campaigns:delete`, `campaigns:phases:delete`, `calendar:notes:delete`, and public Calendar snapshot creation, regeneration or revocation (`calendar:snapshots:create`, `:regenerate`, `:revoke`).
- `strategy:platform:start-version`, `strategy:platform:remove`, `strategy:goal:delete`, `strategy:bet:delete`, and `strategy:bet:resolve`.
- `grid:delete`, `grid:promote`, `grid:reorder`, `grid:remove-cover`.
- `analytics:report`, `analytics:import-history`.

The confirmation prompt should include action, IDs, workspace, integration, scheduled time if any, recipient/reviewer impact if any, and whether the effect is public, destructive, or broad. Do not infer confirmation from older conversation context if the target IDs, integration, or effect changed.

## CLI-enforced `--yes`

This is the complete list of commands that need `--yes`. Append it only after confirmation. Some risky commands above do not require `--yes`; confirm them anyway.

- Content: `content:create` when the payload has `intent: "publish_now"`, `content:publish`, `content:published-caption`, `content:resolve-publishing-result`, `content:recurring-occurrence:cancel`, `content:recurring-series:cancel`.
- Articles: `articles:send-to-x-drafts`, because X's API cannot delete the draft it makes.
- Reviews: `reviews:create` when the file sets `sendNow`, `reviews:submit` when an item's action is `submit` or `publish_now`, `reviews:send`, `reviews:remind`, `reviews:cancel`, `reviews:items:remove`, `reviews:items:comment`, `reviews:items:decision`, `reviews:items:decisions`, `reviews:items:override`, `reviews:items:revoke`.
- Paid ads platform deletion, such as `ads:linkedin:entities:delete`, `ads:meta:entities:delete`, `ads:meta:publications:cleanup`, `ads:meta:boosts:cleanup` and `ads:tiktok:entities:delete`.
- Paid ads changes that start delivery: `ads:campaigns:status` with `ENABLE`, `ads:meta:boosts:activate`, `ads:meta:boosts:resume`, `ads:tiktok:publications:activate`, `ads:linkedin:entities:update` with `confirmActivation: true`, `ads:google:campaigns:change` when it activates or enables a campaign outside a preview, and `ads:meta:ads:create` with `initialStatus` `ACTIVE`.
- Planning: `campaigns:phases:delete`, `integrations:pinterest:create-board`, `calendar:notes:delete`, `calendar:snapshots:revoke`, `strategy:goal:delete`, `strategy:bet:delete`, `strategy:bet:resolve`.
- Analytics: `analytics:report`, `analytics:import-history`.
- Inbox: `inbox:read-all`, `inbox:resolve-all`, `inbox:reply`, `inbox:moderate`, `inbox:retry-message`, `inbox:delete-failed`, `inbox:tags:delete`.
- Media: `media:bulk-delete`, `media:delete-permanent`, `media:bulk-delete-forever`.
- Tasks, grid and Workbench: `tasks:delete`, `tasks:checklist:delete`, `grid:delete`, `grid:promote`, `workbench:rows:remove`.

## Credential handling

- Prefer `MAEVE_API_KEY` and `MAEVE_API_URL` environment variables for automation, and browser login for local human sessions.
- Treat API keys, CLI login tokens, OAuth codes, bearer tokens, refresh tokens, presigned URLs, provider tokens, scopes and raw provider payloads as sensitive. Do not ask the user to paste them into chat, and do not print them from files or command output.
- Keep an MCP API key in the shell environment or client secret storage. Do not put it in `.mcp.json`, `config.toml`, command history, screenshots, files or chat.
- Treat media view/download URLs as sensitive, especially if presigned or temporary.
- If a command output includes sensitive values, summarize the non-sensitive state instead of echoing the raw payload.

## External visibility

Public or externally visible actions include publishing content, retrying failed publishes, sending inbox replies, enabling rules that can send future replies, moderating comments or messages, notifying mentioned task members, sending or resending approval or client-review notifications, and analytics or report exports that leave the local machine or are shared with clients.

If the user asks for one of these but omits business-critical details, ask a targeted question instead of guessing.

## Generated media

- Create, choose, or upload assets separately before scheduling or publishing.
- Inspect uploaded media IDs and integration requirements before attaching media to content.
- Do not represent generated media as approved client assets unless the user says it is approved.
- For platforms requiring media, a title or options, resolve those requirements before scheduling or publishing.

## Bulk actions

- Treat any operation over multiple content, media, inbox, grid, reviewer, or analytics items as broad.
- Confirm the count and explicit ID list or filter before execution.
- Avoid `{}` filters for bulk actions unless the user explicitly confirms the full scope.
