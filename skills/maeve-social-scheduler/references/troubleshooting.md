# Troubleshooting

Use this when CLI commands or MCP operations fail or return validation or provider errors.

## Contents

- Auth failures
- Missing workspace
- Missing or unusable integration
- JSON validation errors
- Timezone errors
- Platform requirement errors
- Provider errors
- Plan or role errors
- Rate limits and transient errors
- Output and debugging

## Auth failures

Run:

```bash
maeve auth:status
maeve auth:whoami
```

Credential precedence is:

1. `--api-key`
2. `--api-key-env <name>`
3. `MAEVE_API_KEY`
4. Stored CLI login token

For automation, prefer env vars. For local sessions, use:

```bash
maeve auth:login
```

Browser login tokens are stored per API URL and last 90 days from their last use, so a terminal in regular use stays signed in.

For workspace-scoped keys stored under a named environment variable, do not paste the key value into commands or chat. Pass the variable name:

```bash
maeve --api-key-env MAEVE_API_KEY_WORKSPACE auth:status
maeve --api-key-env MAEVE_API_KEY_WORKSPACE media:labels:list --workspace <workspaceId>
```

When `--api-key-env` is provided, that named variable must exist. The CLI will not silently fall back to `MAEVE_API_KEY`.

For hosted MCP, the fix is the MCP client's own connect step, never the CLI. The steps, token lifetimes and API-key fallback are in [mcp-tools.md](mcp-tools.md#connection).

If `maeve --help` shows `media:tags:*` instead of `media:labels:*`, or a login expires despite regular use, the installed CLI is stale. Upgrade as described in [command-reference.md](command-reference.md#older-cli-versions).

## Missing workspace

Run:

```bash
maeve workspaces:list
```

If a workspace is missing, the current credential likely does not belong to the expected organization or lacks access.

## Missing or unusable integration

Run `integrations:list` and `integrations:capabilities`, then act on the integration status as listed in [integration-capabilities.md](integration-capabilities.md#response-shape).

If capabilities says an option key is unsupported, refetch capabilities for the exact integration ID; option keys are platform-specific.

## JSON validation errors

Common fixes:

- Ensure payload files are valid JSON, not JavaScript.
- Ensure IDs are UUID strings; grid planner IDs must be UUIDv4.
- Include at least one field for update payloads.
- For create content, include `integrationId` and at least one useful draft field such as `captions.canonical`, `internalTitle`, `publishTitle`, or `contentMedia`.
- Keep `captions.canonical` and caption overrides at or below 25000 characters.
- Use `contentMedia` for content attachments: up to 20 for `instagram`, `linkedin` and `linkedin-page`, 10 for every other platform, and no more than the platform `maxMedia` from capabilities.
- For approval/client-review comments, include `body` or `attachmentId`.
- For inbox replies, include `content` or `attachment`.
- For grid create, include `mediaIds` for `visual_only` or `linkedContentId` for `linked_post`.
- For analytics reports, include `integrationId` or unique `integrationIds`.

## Timezone errors

MCP and the CLI read a `scheduledAt` without an offset in the workspace timezone. The public API and an [older CLI](command-reference.md#older-cli-versions) reject it. Add an offset such as `+10:00` or UTC `Z`:

```json
{ "scheduledAt": "2026-05-01T10:00:00+10:00" }
```

Do not use vague dates such as "tomorrow morning" in payloads.

## Platform requirement errors

Run `integrations:capabilities` for the integration before retrying, then fix the payload against the rules in [integration-capabilities.md](integration-capabilities.md#agent-rules) and the platform's section of [platform-content.md](platform-content.md).

## Provider errors

- X rejects a post whose text matches a recent post with 403 "duplicate content". Text-only threads with identical messages always trip this; vary the text per message.
- Instagram rejects an unresolvable `audio_id` with subcode 2207065 and the misleading message "audio_id is not a valid parameter for the media type, REELS". It usually means the audio ID does not exist; source IDs from the `instagram-audio` option key.
- Pinterest provider code 15 and a `video_cover_not_applied` publish warning are covered in [platform-content.md](platform-content.md).

## Plan or role errors

Approval history, client reviews, approval decisions and comments, pending approval counts, and analytics PDF reports require the Standard plan. Boosts and paid ads require the Basic plan, except the TikTok publication reads, reconcile and pause that keep recovery open. Each also needs a workspace role that allows it.

Report the plan, role or missing resource instead of retrying. Retry rules for every error type are in [mcp-tools.md](mcp-tools.md#recovery).

## Rate limits and transient errors

- Do not immediately loop retries.
- Preserve the command, workspace, integration, content/media/message ID, provider/platform, and error code/message in the response.
- For provider auth errors, recheck integration status.
- For transient provider errors, ask whether to retry later or use `content:retry`/`inbox:retry-message` only after confirmation.

## Output and debugging

Successful commands write JSON to stdout. Errors write structured JSON to stderr and exit non-zero.

For list commands, use filters to reduce noise:

```bash
maeve content:list --workspace <workspaceId> --status scheduled
maeve inbox:threads --workspace <workspaceId> --read unread
maeve media:list --workspace <workspaceId> --type image --favorite
```
