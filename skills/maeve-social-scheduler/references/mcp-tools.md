# MCP operation catalog

Use this reference for hosted MCP setup, operation discovery, execution, upload, batch recovery, and connection recovery.

## Contents

- Connection
- Four-tool surface
  - Discovery pattern
- Common operation IDs
- Operation notes
  - Instagram competitor research
  - Inbox automation
  - Workbench placement
  - Outside the catalog
- Paid ads
- Confirmation
- Upload sequence
- Batch operations
- Recovery

## Connection

- Prefer the MCP client's own browser connection. Claude Code: run `/mcp`, choose `maeve`, and approve Maeve in the browser. Codex: run `codex mcp login maeve` and approve Maeve in the browser. Other clients: use their **Authenticate** action for `maeve`. The client handles the redirect itself, so do not paste the authorization URL into chat or ask for it back.
- An API key is only for a client that does not support browser authentication, or for server-side automation. The operator sets it up; see [safety-policy.md](safety-policy.md#credential-handling).
- CLI login does not authenticate MCP: `maeve auth:login` signs in the CLI only and does not create an MCP token.
- MCP access tokens last 6 hours and refresh through 90-day rotating refresh tokens. If a client cannot refresh, was revoked, or asks for approval again, run the same connect step again.
- Organization admins can revoke active AI tool connections, including browser-approved MCP grants, in Maeve **Settings -> Developer**.
- Confirm the API environment, organization, workspace, and integration independently. Different connections can target different environments.

## Four-tool surface

The `/mcp` surface exposes only the authorized subset of these four names. The separate `/mcp/openai` surface lists one tool per operation and uses its own skill; this reference covers `/mcp`.

| Tool            | Purpose                                                                                                                                                     |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `maeve_search`  | Find a bounded set of authorized operations by intent. It returns canonical operation IDs and the required executor, not schemas.                           |
| `maeve_details` | Load one authorized operation's input/output schemas, permissions, availability, side effects, confirmation, retry guidance, examples, and next operations. |
| `maeve_read`    | Execute one read operation as `{ operationId, input }`.                                                                                                     |
| `maeve_write`   | Execute one write or dangerous operation as `{ operationId, input }`. The selected operation still enforces its own confirmation and permissions.           |

An OAuth grant without read scope can expose only `maeve_write`; a read-only grant exposes `maeve_search`, `maeve_details`, and `maeve_read`. Toolsets authorize operations; they do not create top-level tool names.

Do not guess an operation schema from its canonical ID or from a CLI command. Search, load details for the chosen operation, then use the returned executor and exact input schema. A prior search or details call is guidance, not authorization.

### Discovery pattern

1. Search for workspace discovery and load details for `workspace.list`. Run it through `maeve_read` with an empty operation input.
2. Keep the selected `workspaceId`, then search again with that workspace and the user's actual intent.
3. Load `maeve_details` for the selected operation with the same workspace context.
4. If details says `available: false`, report `unavailableReason` and do not attempt execution. Resolve required plan, platform, or account conditions instead.
5. Execute through the returned `executor`, passing the canonical ID and an `input` object that matches the details schema exactly. Put `workspaceId` inside `input`, not beside it.
6. Follow returned `nextOperations` only when they advance the user's request. Load their details before execution.

Search is bounded. Refine the query when `hasMore` is true or the desired operation is absent. An operation can remain hidden because of credential scope, toolset grants, organization policy, workspace access, role capability, plan, or platform support. Do not switch credentials to bypass that result.

## Common operation IDs

These stable IDs are useful search targets. Details remains authoritative for their current schemas and executors.

| Workflow                          | Read operations                                                                                                                                                                                                                                                                                                                      | Write operations                                                                                                                                                                                                                                                                                                                                                                                   |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Workspace and integration context | `workspace.list`, `workspace.members.list`, `integrations.list`, `integrations.get_capabilities`, `integrations.get_options`, `taxonomy.list`, `hashtag_groups.list`, `campaigns.list`, `grid.items.list`                                                                                                                            | `integrations.create_pinterest_board`                                                                                                                                                                                                                                                                                                                                                              |
| Content                           | `content.root.list`, `content.root.get`, `content.list`, `content.get`, `content.get_counts`                                                                                                                                                                                                                                         | `content.root.update`, `content.create_draft`, `content.create_drafts`, `content.update_draft`, `content.update_metadata`, `content.update_notes`, `content.set_intended_publish_time`, `content.schedule`, `content.schedule_batch`, `content.revert_to_draft`, `content.publish_now`, `content.retry`, `content.update_published_caption`, `content.resolve_publishing_result`, `content.delete` |
| X Articles                        | `articles.list`, `articles.get`                                                                                                                                                                                                                                                                                                      | `articles.create`, `articles.update`, `articles.send_to_x_drafts`                                                                                                                                                                                                                                                                                                                                  |
| Media Room                        | `media.list`, `media.get`, `media.get_usage_history`, `media.list_folders`, `media.get_folder`, `media.get_folder_path`, `media.list_labels`                                                                                                                                                                                         | `media.inspect_audio`, `media.import`, `media.upload`, `media.complete_upload`, `media.upload_batch`, `media.complete_upload_batch`, `media.folder.create`, `media.folder.update`, `media.folder.move`, `media.label.create`, `media.labels.attach`, `media.labels.detach`, `media.update`, `media.organize`, `media.delete`, `media.restore`, `media.folder.delete`                               |
| Review                            | `workspace.members.list`, `approval_workflows.list`                                                                                                                                                                                                                                                                                  | `review.request`, `review.withdraw`, `review.remove_content`                                                                                                                                                                                                                                                                                                                                       |
| Tasks                             | `task_board.list`, `task.get`, `task.list_for_post`                                                                                                                                                                                                                                                                                  | `task.create`, `task.update`, `task.move`, `task.create_comment`, `task.create_checklist_item`, `task.update_checklist_item`, `task.move_checklist_item`, `task.delete_checklist_item`, `task.delete`                                                                                                                                                                                              |
| Workbench content tables          | `workbench.page.list`, `workbench.content_table.get`, `workbench.content_table.list_rows`                                                                                                                                                                                                                                            | `workbench.page.create`, `workbench.content_table.add_row`, `workbench.content_table.set_row_cells`, `workbench.content_table.remove_row`                                                                                                                                                                                                                                                          |
| Calendar                          | `calendar.list`, `calendar.notes.list`, `calendar.snapshots.list`, `calendar.snapshot.preview`                                                                                                                                                                                                                                       | `calendar.note.create`, `calendar.note.update`, `calendar.note.move`, `calendar.note.delete`, `calendar.snapshot.create`, `calendar.snapshot.regenerate`, `calendar.snapshot.revoke`                                                                                                                                                                                                               |
| Analytics                         | `analytics.get_summary`, `analytics.get_aggregate`, `analytics.get_health`, `analytics.get_demographics`, `analytics.get_posts`, `analytics.get_posts_aggregate`, `analytics.get_content_performance`, `analytics.get_post`, `instagram_competitors.list`, `instagram_competitors.get_benchmark`, `instagram_competitors.list_posts` | None                                                                                                                                                                                                                                                                                                                                                                                               |
| Inbox automation                  | `inbox_automation.list_rules`, `inbox_automation.get_rule`, `inbox_automation.list_executions`                                                                                                                                                                                                                                       | `inbox_automation.create_rule`, `inbox_automation.update_rule`, `inbox_automation.delete_rule`                                                                                                                                                                                                                                                                                                     |
| Paid ads                          | `ads.accounts.list`, `ads.capabilities.get`, then the `ads.*` reads for the account                                                                                                                                                                                                                                                  | `ads.*` changes, drafts, campaign and boost creation, and recovery; creation waits for approval on the Maeve review page                                                                                                                                                                                                                                                                           |

## Operation notes

X Articles are long-form X posts with their own `articles.*` operations, not `content.create_draft`; their rules are in the X section of [platform-content.md](platform-content.md#x).

Strategy operations use the `strategy.*` namespace. Search for the requested Foundation, platform, Goal, Bet, prediction, progress, or Retro workflow instead of loading the whole namespace.

### Instagram competitor research

Competitor research is read-only, and the CLI's `competitors:list`, `competitors:posts` and `competitors:benchmark` return the same fields.

- `instagram_competitors.list` returns the tracked accounts with each `igUserId` and `username`, the plan's `limit` and `historyDays`, and the user's own connected accounts in `eligibleConnections`.
- `instagram_competitors.list_posts` takes one `igUserId` and returns each post's full `caption`, `mediaType` and `mediaProductType` (`REELS` for a reel), `permalink`, `postedAt`, `likeCount`, `commentsCount` and `viewCount` (reels only, a string, paid views included), with `total`, `usualEngagement` and `followersCount`.
- Sort with `sort` (`recent`, `likes`, `comments`, `views`, `engagement`, `rising`, `discussed`, `reach`), filter with `format` (`reels` or `feed`) or `standouts: true`, and page with `limit` (up to 60) and `offset`. For "top reels by views", send `sort: views` and `format: reels`.
- `instagram_competitors.get_benchmark` summarizes followers, growth, cadence, formats, engagement, top posts and hashtags over `days`.
- Only posts inside the plan's history window are returned. Adding or removing a tracked account stays in the app.

### Inbox automation

`inbox_automation.*` configures Instagram and Facebook auto-reply rules and reads execution history. An Instagram rule can carry `flow` (public replies, an opening DM, a follow check, an email question, a reminder, and the link DM with up to 3 link buttons), `trackLinkClicks`, and `isDraft`. The plain reply body fields are also valid but cannot be sent together with `flow`. Email addresses collected by the email question stay in the app and are never returned. Rules default disabled; enabling or deleting a rule requires exact confirmation.

### Workbench placement

`content.create_draft` can place new content directly on a table with `workbenchPageId` and optional `sortOrder`. If placement fails after creation, keep the returned `contentId` and finish with `workbench.content_table.add_row`; do not create replacement content. Row cell replacement and removal require the latest row `version`. Re-read after a stale-version conflict. Removing a row requires exact confirmation and leaves the content intact.

### Outside the catalog

Live inbox messaging, approval decisions, ad account connection, billing, credentials, integration connect/disconnect, recurring cancellation, and PDF reports remain outside the MCP catalog. Use the CLI or public API only where they support the task and the user has authorized the effect.

## Paid ads

Paid ads use the `ads.*` operations.

- Read `ads.accounts.list`, then `ads.capabilities.get` before anything else on that account.
- A campaign or boost creation request returns a pending approval and a review URL. Share the URL so the user can approve it on the Maeve review page; `ads.approvals.get` only reads its status.
- When the response also has `chatConfirmation`, the workspace lets the user approve in chat: show them `approval.summary`, ask, and only after they agree send the same request again with the `approvalId` and that `chatConfirmation`.
- Starting delivery needs the exact `confirmationText` from details after the user agrees, sent inside the operation's input (a refusal returns it in `details.expectedConfirmationText`). Show what will start delivering and its budget first. This covers `ads.campaigns.update_status` with `ENABLE`, `ads.meta.boosts.activate` and `resume`, `ads.tiktok.publications.activate`, `ads.linkedin.entities.update` with `confirmActivation: true`, `ads.meta.ads.create` with `initialStatus: ACTIVE`, and `ads.google.campaigns.change` that turns something on or sets anything to `ENABLED`.
- Platform deletes such as `ads.meta.entities.delete`, `ads.linkedin.entities.delete` and `ads.tiktok.entities.delete` also need the exact `confirmationText` and are never auto-confirmed.

Google Ads:

- Read with the `ads.google.*` operations (campaigns, settings, ad groups, ads, asset groups, search terms, products and measurement), capabilities and refresh, and with `ads.reports.get_client_report` and `ads.reports.export_csv`, which take `campaignId` for one campaign.
- Results come in brief: `ads.google.campaigns.list` and `ads.google.asset_groups.list` unless `view: "full"`, a change's summary unless `includeSnapshots: true`, and `ads.reports.get_client_report` without daily rows with `includeDaily: false`.
- Change an existing Search campaign, or a Performance Max campaign without a Merchant Center link, with `ads.google.campaigns.change` after `ads.google.campaigns.get_edit_context`: preview with `preview: true`, then send the same input with the same `requestKey`. Read a `creating` or `unknown` result with `ads.google.requests.get` and never send a replacement.
- The same operation pauses and turns on a campaign, as `ads.capabilities.get` `management.statusChange.operation` says. Name a negative keyword to remove by `text`, `matchType` and `scope`.
- Changes to a campaign that is on, such as removing an ad schedule or a negative keyword or raising a bid or budget, take effect at once with no confirmation text, so preview them and tell the user first.
- Create a Search or Performance Max campaign with `ads.google.campaigns.create`: read `ads.google.creation.get_options`, find locations with `ads.google.locations.search` and audience signals with `ads.google.audience_segments.search`, upload any new image with `ads.google.images.upload` (no approval), check it with `ads.google.campaigns.validate`, then create. The first request creates nothing and returns the pending approval; the same request with the `approvalId` creates it once, paused.
- Other Google campaign types are created in the Maeve dashboard.
- Send campaign names as Maeve shows them; a rename keeps Maeve's tag.
- Other shared operations refuse a Google account and name the operation to use.

## Confirmation

`maeve_write` is a generic executor, not a blanket confirmation. Inspect `maeve_details.confirmation` and `sideEffects` for the selected operation.

- Ask for authorization when intent, destination, audience, or external effect is missing. Existing authorization for the same action and targets remains valid.
- When confirmation is required, pass `confirmationText` inside the operation's `input`. Use the exact value described by details or returned by a `confirmation_required` error. Never construct a broader token or substitute another resource ID.
- A preview-first operation such as `media.organize` must be previewed against the final bounded selection. Commit only with the exact confirmation returned for that preview.
- Never assume dangerous auto-confirm is available. If details says the action never auto-confirms, exact text is mandatory. A server policy cannot make an irreversible action safe to replay.
- Review decisions remain human actions. Creating or sending a review request can notify people and still needs the user's intended recipients and content scope.

## Upload sequence

Accepted formats and size limits for `media.upload`, `media.upload_batch` and `media.import` are in [content-payloads.md](content-payloads.md#media).

For a public HTTPS link or a file the user attached in ChatGPT, run `media.import` through `maeve_write` instead. Put the link in `input.url`, or pass the attachment unchanged as the top-level `file` field. Maeve downloads the file, verifies the format from the bytes (including Office container inspection for Word and PowerPoint documents), and returns the new media ID, so no PUT is needed. Never build a `file` object from a name, ID, or earlier message.

MCP uploads of local files require both operation calls and an external byte transfer:

1. Load details for `media.upload`, then call it through `maeve_write` with file name, MIME type, size, and workspace context.
2. Keep the returned upload session fields private. PUT the exact local file bytes to the returned URL before expiry, using the required headers.
3. Load details for `media.complete_upload`, then execute it through `maeve_write` with the returned session identity.
4. Read `media.get` before attaching the new media ID to content.

If the current client cannot read the local file or make the PUT request, use `maeve media:upload` instead. The four MCP tools add no local file-system access. The only operation that fetches a link is `media.import`, which downloads one public HTTPS URL or chat attachment into the Media Room on the server. Never report an initialized upload as completed. Replaying initialization does not renew an expired URL; inspect the current session and create a new upload session only when the old transfer cannot complete.

## Batch operations

`content.create_drafts` and `content.schedule_batch` accept up to 100 items. `media.upload_batch` and `media.complete_upload_batch` accept up to 50. Load details for the exact operation before constructing a batch.

- Give each item a stable, distinct idempotency key. Replay an identical request with the same key and unchanged item order, and give a corrected item a new key. Reusing a key with changed input is a conflict.
- Treat results independently by `index`. Retain every successful resource ID and per-item replay key even when neighbours fail.
- Replaying the same key and manifest returns the recorded successes and failures. It does not make a failed item succeed or renew upload URLs.
- Media upload batches still require one byte PUT per initialized item before completion.

## Recovery

- For validation errors, use `details.validationErrors`, correct only the rejected field, and preserve successful IDs.
- Don't retry `plan_required`, `forbidden` or `not_found`. Report the plan, permission, role or missing resource instead, and do not retry with a different payload unless the user changes workspace or account.
- For `rate_limited`, wait for `retryAfterSeconds` when present. Do not switch connections or create replacement content.
- For conflict or uncertain mutation results, follow the operation's details retry contract. Read the affected resource before retrying an idempotent or unsafe write.
- Do not retry or create replacement content while a publishing outcome is uncertain.
- The MCP transport is stateless. After a connection failure, reconnect through the client and continue from persisted Maeve IDs. The client handles discovery and initialization; no server session or activation state needs rebuilding. A connection failure does not erase saved content or media and does not prove that a write failed.
- Authentication loss uses the client's reconnect flow. CLI login cannot repair MCP OAuth.
