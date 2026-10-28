---
name: maeve-social-scheduler
description: Create, schedule and publish posts, manage media, read analytics and run paid ads in a Maeve Social workspace through the Maeve MCP, CLI or public API. Use for work inside a Maeve workspace, not for developing Maeve's own code.
---

# Maeve Social

## Choose the connection

Prefer the connected Maeve MCP catalog for supported tasks. If the configured connection needs login, use the client's own connect action. Do not request OAuth codes or tokens in chat. Use the CLI for local files, uncovered workflows, or when no MCP connection is configured; use the public API when neither surface covers the task.

Verify the selected API environment, organization, workspace and integration. A coding-session connector, ChatGPT connection and CLI login may point to different environments. CLI login does not authenticate MCP. For CLI work, run `maeve auth:status` against the intended `--api-url` before reads or writes. Treat a command as available only when it appears in root `maeve --help`; do not use subcommand help as an existence probe. Keep credentials and signed URLs out of output.

## Create and change content

1. For MCP, use `maeve_search` to discover `workspace.list`, execute it with `maeve_read`, then search again with the selected workspace. Load `maeve_details` for every chosen operation before execution, and put `workspaceId` inside the operation `input`. With any connection, read the connected integration's live capabilities and fetch dynamic options when a capability includes `optionKey`.
2. Read the selected platform in [platform-content.md](references/platform-content.md). Use the live tool schema or CLI schema; their arguments are not interchangeable.
3. Create a draft unless the user asked to publish or schedule. MCP executes `content.create_draft` through `maeve_write`; CLI uses `content:create` with omitted or draft `intent`. MCP `content.create_draft` also schedules the new content in the same step when the input has `scheduledAt`, and it asks for no confirmation, so omit `scheduledAt` for a draft. Upload local media through Maeve before attaching its IDs.
4. Resolve the publication date, time and timezone before scheduling; [content-payloads.md](references/content-payloads.md#general-rules) says when a time needs an offset. Confirm any missing intent, destination or external effect before publishing, deleting, sending messages or making broad changes. Existing authorization applies to the same action and targets; do not repeatedly ask for it.
5. Read saved values after changes. Preserve exact captions, per-platform overrides and attachment order. Use documented clearing values; omitted fields generally preserve existing values.

For a confirmation-gated MCP operation, put the exact required `confirmationText` inside the `maeve_write` operation input only when the user authorized that action and target. Add CLI `--yes` only where the command requires it. Review decisions remain human actions; requesting a review may notify people. See [safety-policy.md](references/safety-policy.md) for action-specific requirements.

## Results and recovery

Report the affected IDs, workspace, destination and status, plus scheduled time when relevant. Label the returned `appUrl` **Open in Maeve**; never invent app URLs. Return a non-null HTTPS `permalink` from a `content.get` read as **View on platform**.

A queued response, sent status or provider ID is not proof of native visibility. When the task requires live verification, check the provider author, rendered content and resource identity separately. Supported provider deletion can retain the sent audit in Maeve.

For several drafts, use MCP `content.create_drafts` or one CLI `content:create` per item, each with a stable, distinct idempotency key. Keep every successful ID, replay an identical request with the same key, give a corrected item a new key, and read each result. Never schedule or publish drafts only to check that they were created.

Inspect conflicts before changing input. Don't retry `plan_required`, `forbidden` or `not_found`; report the blocker instead. Respect retry-after limits; do not switch connections or create replacement content to recover uncertain publishing outcomes. The MCP transport is stateless; reconnect through the client after a connection failure and continue from persisted Maeve IDs without rebuilding activation state.

## References

Load only what the task needs:

- [mcp-tools.md](references/mcp-tools.md): connection, catalog discovery, execution, upload, batches, paid ads and recovery.
- [command-reference.md](references/command-reference.md): CLI command groups and sequences.
- [content-payloads.md](references/content-payloads.md): JSON shapes, scheduling times and attachment limits.
- [integration-capabilities.md](references/integration-capabilities.md): capability shape, platform matrix and dynamic options.
- [platform-content.md](references/platform-content.md): platform inputs, clearing and publication evidence.
- [safety-policy.md](references/safety-policy.md): notifications, destructive actions, confirmation fields and the CLI `--yes` list.
- [troubleshooting.md](references/troubleshooting.md): authentication, validation, permissions and provider errors.
