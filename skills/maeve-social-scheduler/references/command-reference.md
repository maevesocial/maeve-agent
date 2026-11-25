# Command reference

Curated from the `maeve-cli` 0.19.0 command surface. Use root `maeve --help` to verify that a command exists; do not use subcommand help as an existence probe. Commands shown with `--yes` need it; [safety-policy.md](safety-policy.md#cli-enforced---yes) has the complete list.

## Contents

- Discovery
- Safe reads
- Task board
- Workbench
- Draft and low-risk mutations
- Scheduling and publishing
- Destructive or broad actions
- Approvals and client reviews
- Analytics, reports and history imports
- Instagram competitor research
- Older CLI versions

## Discovery

Start here before any mutation:

```bash
maeve auth:status
maeve auth:login
maeve auth:whoami
maeve auth:logout
maeve workspaces:list
maeve workspaces:members --workspace <workspaceId>
maeve integrations:list --workspace <workspaceId>
maeve integrations:capabilities --workspace <workspaceId> --integration <integrationId>
maeve integrations:options --workspace <workspaceId> --integration <integrationId> --key <optionKey>
```

If the user named no workspace, choose one only when a single workspace is clearly the target; otherwise ask. Option bodies for `integrations:options --json <file>` are in [integration-capabilities.md](integration-capabilities.md#commands).

## Safe reads

These commands read state and are usually safe to run after auth/workspace discovery:

```bash
maeve content:list --workspace <workspaceId>
maeve content:get --workspace <workspaceId> --id <contentId>
maeve content:versions --workspace <workspaceId> --id <contentId>
maeve content:roots:list --workspace <workspaceId>
maeve content:roots:get --workspace <workspaceId> --id <rootId>
maeve articles:list --workspace <workspaceId>
maeve articles:get --workspace <workspaceId> --id <contentId>
maeve content:failed-count --workspace <workspaceId>
maeve media:list --workspace <workspaceId>
maeve media:list --workspace <workspaceId> --state deleted
maeve media:get --workspace <workspaceId> --id <mediaId>
maeve media:download-url --workspace <workspaceId> --id <mediaId>
maeve media:view-url --workspace <workspaceId> --id <mediaId>
maeve media:folders:list --workspace <workspaceId> --parent-id root
maeve media:folders:get --workspace <workspaceId> --id <folderId>
maeve media:folders:path --workspace <workspaceId> --id <folderId>
maeve media:labels:list --workspace <workspaceId>
maeve taxonomy:labels --workspace <workspaceId>
maeve taxonomy:pillars --workspace <workspaceId>
maeve taxonomy:formats --workspace <workspaceId>
maeve hashtags:list --workspace <workspaceId>
maeve analytics:summary --workspace <workspaceId> --days 30
maeve analytics:health --workspace <workspaceId> --integration <integrationId>
maeve analytics:posts --workspace <workspaceId> --integration <integrationId> [--post-type post,reel] [--media-format video,carousel]
maeve analytics:posts-aggregate --workspace <workspaceId> [--post-type reel] [--media-format video]
maeve analytics:content-performance --workspace <workspaceId> --integration-ids <ids> --dimension mediaFormats --days 30 [--post-type post,reel] [--media-format image,video,carousel]
maeve analytics:post --workspace <workspaceId> --id <contentId>
maeve analytics:demographics --workspace <workspaceId> --integration <integrationId>
maeve analytics:import-history:status --workspace <workspaceId> --integration <integrationId>
maeve competitors:list --workspace <workspaceId>
maeve competitors:posts --workspace <workspaceId> --competitor <igUserId|@username> --sort views --format reels --limit 50
maeve competitors:benchmark --workspace <workspaceId> --competitor <igUserId|@username> --days 90
maeve inbox:threads --workspace <workspaceId>
maeve inbox:messages --workspace <workspaceId> --thread <threadId>
maeve inbox:stats --workspace <workspaceId>
maeve inbox:tags --workspace <workspaceId>
maeve grid:list --workspace <workspaceId> --integration <integrationId>
maeve media:usage-history --workspace <workspaceId> --id <mediaId>
maeve media:labels:usage --workspace <workspaceId>
maeve media:labels:health --workspace <workspaceId>
maeve media:label-groups:list --workspace <workspaceId>
maeve campaigns:list --workspace <workspaceId> --include-phases
maeve campaigns:get --workspace <workspaceId> --id <campaignId>
maeve campaigns:content --workspace <workspaceId> --id <campaignId>
maeve campaigns:phases:list --workspace <workspaceId> --id <campaignId>
maeve calendar:feed --workspace <workspaceId> --start "2026-05-01T00:00:00+10:00" --end "2026-06-01T00:00:00+10:00"
maeve calendar:notes:list --workspace <workspaceId> --start 2026-05-01 --end 2026-05-31
maeve calendar:snapshots:list --workspace <workspaceId>
maeve calendar:snapshots:preview --workspace <workspaceId> --json calendar-snapshot.json
maeve strategy:foundation --workspace <workspaceId>
maeve strategy:platforms --workspace <workspaceId>
maeve strategy:platform:history --workspace <workspaceId> --platform instagram
maeve strategy:goals --workspace <workspaceId>
maeve strategy:goals:metrics --workspace <workspaceId>
maeve strategy:goal --workspace <workspaceId> --id <goalId>
maeve strategy:goal:progress --workspace <workspaceId> --id <goalId>
maeve strategy:bets --workspace <workspaceId>
maeve strategy:bets:metrics --workspace <workspaceId>
maeve strategy:bets:leaderboard --workspace <workspaceId>
maeve strategy:bet --workspace <workspaceId> --id <betId>
maeve strategy:retros --workspace <workspaceId>
maeve strategy:retros:settings --workspace <workspaceId>
maeve strategy:retro --workspace <workspaceId> --id <retroId>
maeve boosts:list --workspace <workspaceId>
maeve boosts:get --workspace <workspaceId> --id <boostId>
maeve boosts:performance --workspace <workspaceId> --id <boostId>
maeve boosts:ad-accounts --workspace <workspaceId> --integration <integrationId>
maeve boosts:boosted-content-ids --workspace <workspaceId>
maeve boosts:by-content --workspace <workspaceId> --content <contentId>
maeve tasks:list --workspace <workspaceId>
maeve tasks:get --workspace <workspaceId> --id <taskId> --include-comments --include-activity
maeve tasks:for-post --workspace <workspaceId> --post-id <contentId>
maeve tasks:linkable-posts --workspace <workspaceId> --query "launch" --stage drafting
maeve tasks:comments --workspace <workspaceId> --id <taskId>
maeve tasks:activity --workspace <workspaceId> --id <taskId> --limit 50
```

Campaigns and strategy reads are general workspace context. The `boosts:*` reads require the Basic plan, and the CLI leaves the `/v1/boosts` writes off because they spend ad budget. Paid ads, including Meta boosts, run through the `ads:*` group, generated from the shared ads operations: `ads:meta:boosts:create` returns a pending approval and its review URL and creates nothing until the user approves on the Maeve review page; repeat the same request with the approval ID afterwards. `ads:meta:boosts:activate`, `:pause`, `:resume`, `:reconcile` and `:cleanup` manage a created boost. Use root `maeve --help` for the full `ads:*` list.

Standard plan reads:

```bash
maeve content:pending-approval-count --workspace <workspaceId>
maeve approval-workflows:list --workspace <workspaceId>
maeve reviews:list --workspace <workspaceId> --status active
maeve reviews:inbox --workspace <workspaceId> --group your_turn
maeve reviews:summary --workspace <workspaceId>
maeve reviews:get --workspace <workspaceId> --review <reviewId>
maeve reviews:history --workspace <workspaceId>
maeve reviews:history:content --workspace <workspaceId> --content <contentId>
maeve reviews:history:get --workspace <workspaceId> --review <reviewId>
maeve reviews:items:activity --workspace <workspaceId> --review <reviewId> --item <itemId>
```

## Task board

```bash
maeve tasks:create --workspace <workspaceId> --json task.json
maeve tasks:update --workspace <workspaceId> --id <taskId> --json task-update.json
maeve tasks:move --workspace <workspaceId> --id <taskId> --json task-move.json
maeve tasks:delete --workspace <workspaceId> --id <taskId> --yes
maeve tasks:comments:create --workspace <workspaceId> --id <taskId> --json task-comment.json
maeve tasks:checklist:create --workspace <workspaceId> --id <taskId> --json checklist-item.json
maeve tasks:checklist:update --workspace <workspaceId> --id <taskId> --item <itemId> --json checklist-update.json
maeve tasks:checklist:move --workspace <workspaceId> --id <taskId> --item <itemId> --json checklist-move.json
maeve tasks:checklist:delete --workspace <workspaceId> --id <taskId> --item <itemId> --yes
```

- Task mutations require an Editor-or-higher workspace role. Deleted tasks cannot be restored.
- Update payloads carry only changed fields. Assignee and label arrays are full replacement sets. `dueAt` is a `YYYY-MM-DD` date.
- Link a task to content with `postId` on create or update; `tasks:linkable-posts` finds content that can be linked.

## Workbench

Workbench pages and content tables, such as a monthly plan:

```bash
maeve workbench:pages:list --workspace <workspaceId> --type content_table
maeve workbench:pages:get --workspace <workspaceId> --page <pageId>
maeve workbench:rows:list --workspace <workspaceId> --page <pageId> --limit 50
maeve workbench:pages:create --workspace <workspaceId> --title "October"
maeve workbench:rows:add --workspace <workspaceId> --page <pageId> --post <postId>
maeve workbench:rows:set-cells --workspace <workspaceId> --page <pageId> --row <placementId> --expected-version <version> --json cells.json
maeve workbench:rows:remove --workspace <workspaceId> --page <pageId> --row <placementId> --expected-version <version> --yes
```

- Writes require Workbench manage access. `workbench:pages:create` always creates a content table; ask before creating one when no existing title fits.
- `workbench:rows:add --post` takes the root `postId` from `content:roots:list`, not the content ID from `content:create`. A post can be on a table once.
- Row writes take the row `version` from the latest `workbench:rows:list`; on a 409, list again and resend with the new version.
- `workbench:rows:set-cells` replaces the whole cell map keyed by column ID from `workbench:pages:get`.
- `workbench:rows:remove` cannot be undone and keeps the post.

## Draft and low-risk mutations

Prefer draft creation unless the user explicitly asks to schedule or publish:

```bash
maeve content:create --workspace <workspaceId> --json create-content.json
maeve content:update --workspace <workspaceId> --id <contentId> --json update-content.json
maeve content:versions:restore --workspace <workspaceId> --id <contentId> --version-number <number> --expected-version-id <versionId> --expected-updated-at <timestamp>
maeve content:roots:update --workspace <workspaceId> --id <rootId> --json root-update.json
maeve articles:create --workspace <workspaceId> --integration <xIntegrationId> --title "Title" --body article.html [--cover <mediaId>]
maeve articles:update --workspace <workspaceId> --id <contentId> [--title "Title"] [--body article.html --body-version <bodyVersion>] [--cover <mediaId> | --remove-cover]
maeve content:metadata --workspace <workspaceId> --id <contentId> --json content-metadata.json
maeve content:notes --workspace <workspaceId> --id <contentId> --notes "<p>Planning notes</p>"
maeve media:inspect-audio --workspace <workspaceId> --id <mediaId>
maeve media:upload ./image.png --workspace <workspaceId>
maeve media:update --workspace <workspaceId> --id <mediaId> --json media-update.json
maeve media:restore --workspace <workspaceId> --id <mediaId>
maeve media:bulk-restore --workspace <workspaceId> --json media-ids.json
maeve media:folders:create --workspace <workspaceId> --json media-folder.json
maeve media:folders:update --workspace <workspaceId> --id <folderId> --json media-folder.json
maeve media:folders:move --workspace <workspaceId> --id <folderId> --json media-folder-move.json
maeve media:labels:create --workspace <workspaceId> --json media-label.json
maeve media:labels:update --workspace <workspaceId> --id <labelId> --json media-label.json
maeve media:labels:attach --workspace <workspaceId> --id <mediaId> --json media-label-ids.json
maeve media:labels:detach --workspace <workspaceId> --id <mediaId> --json media-label-ids.json
maeve media:labels:merge --workspace <workspaceId> --id <sourceLabelId> --json media-label-merge.json
maeve media:label-groups:create --workspace <workspaceId> --json label-group.json
maeve media:label-groups:update --workspace <workspaceId> --id <groupId> --json label-group.json
maeve media:label-groups:reorder --workspace <workspaceId> --json label-group-order.json
maeve media:label-groups:add-labels --workspace <workspaceId> --id <groupId> --json label-ids.json
maeve media:label-groups:ungroup-labels --workspace <workspaceId> --json label-ids.json
maeve campaigns:create --workspace <workspaceId> --json campaign.json
maeve campaigns:update --workspace <workspaceId> --id <campaignId> --json campaign.json
maeve campaigns:phases:create --workspace <workspaceId> --id <campaignId> --json campaign-phase.json
maeve campaigns:phases:replace --workspace <workspaceId> --id <campaignId> --json campaign-phases.json
maeve campaigns:phases:update --workspace <workspaceId> --id <campaignId> --phase-id <phaseId> --json campaign-phase.json
maeve calendar:notes:create --workspace <workspaceId> --json calendar-note.json
maeve calendar:notes:update --workspace <workspaceId> --id <noteId> --json calendar-note.json
maeve calendar:notes:move --workspace <workspaceId> --id <noteId> --json calendar-note-move.json
maeve strategy:platform:upsert --workspace <workspaceId> --platform instagram --json platform-strategy.json
maeve strategy:goal:estimate --workspace <workspaceId> --json goal.json
maeve strategy:goal:create --workspace <workspaceId> --json goal.json
maeve strategy:foundation:update --workspace <workspaceId> --json foundation-update.json
maeve strategy:goal:update --workspace <workspaceId> --id <goalId> --json goal-update.json
maeve strategy:goal:log-progress --workspace <workspaceId> --id <goalId> --json goal-progress.json
maeve strategy:goal:complete --workspace <workspaceId> --id <goalId>
maeve strategy:goal:reopen --workspace <workspaceId> --id <goalId>
maeve strategy:bet:estimate --workspace <workspaceId> --json bet.json
maeve strategy:bet:create --workspace <workspaceId> --json bet.json
maeve strategy:bet:update --workspace <workspaceId> --id <betId> --json bet-update.json
maeve strategy:bet:predict --workspace <workspaceId> --id <betId> --json bet-prediction.json
maeve strategy:bet:unpredict --workspace <workspaceId> --id <betId>
maeve hashtags:create --workspace <workspaceId> --json hashtags.json
maeve hashtags:update --workspace <workspaceId> --id <hashtagGroupId> --json hashtags.json
maeve inbox:note --workspace <workspaceId> --thread <threadId> --json inbox-note.json
maeve inbox:tags:create --workspace <workspaceId> --name "VIP" --color "#2563eb"
maeve inbox:tags:update --workspace <workspaceId> --tag <tagId> --name "New name"
maeve inbox:tags:reorder --workspace <workspaceId> --json inbox-tag-ids.json
maeve inbox:tag-thread --workspace <workspaceId> --thread <threadId> --tag <tagId>
maeve inbox:read --workspace <workspaceId> --thread <threadId>
maeve inbox:unread --workspace <workspaceId> --thread <threadId>
maeve inbox:resolve --workspace <workspaceId> --thread <threadId>
maeve inbox:reopen --workspace <workspaceId> --thread <threadId>
maeve grid:create --workspace <workspaceId> --json grid-item.json
maeve grid:update --workspace <workspaceId> --item <itemId> --json grid-update.json
maeve grid:replace-media --workspace <workspaceId> --item <itemId> --json grid-media.json
maeve grid:set-cover --workspace <workspaceId> --item <itemId> --json grid-cover.json
```

`strategy:foundation:update` replaces whole parts (`audience`, `objective`, `positioning`, `voice`) and leaves out parts as saved. Read with `strategy:foundation` first, edit the part, then send every field of that part; a part missing a field is rejected.

There is no CLI command to set a content workflow status (idea, drafting, and so on); that is set in the app. The CLI only filters by it, with `content:list --workflow-status` / `--workflow-statuses`.

## Scheduling and publishing

These need explicit user intent. Scheduling also needs date, time, and timezone:

```bash
maeve content:schedule --workspace <workspaceId> --id <contentId> --scheduled-at "2026-05-01T10:00:00+10:00"
maeve content:intended-time --workspace <workspaceId> --id <contentId> --scheduled-at "2026-05-01T10:00:00+10:00"
maeve content:intended-time --workspace <workspaceId> --id <contentId> --clear
maeve content:publish --workspace <workspaceId> --id <contentId> --yes
maeve content:published-caption --workspace <workspaceId> --id <contentId> --json published-caption.json --yes
maeve content:retry --workspace <workspaceId> --id <contentId>
maeve content:resolve-publishing-result --workspace <workspaceId> --id <contentId> --json publishing-result.json --yes
maeve content:revert-to-draft --workspace <workspaceId> --id <contentId>
maeve articles:send-to-x-drafts --workspace <workspaceId> --id <contentId> --yes
```

`articles:send-to-x-drafts` writes a draft to the account's X drafts, which X's API cannot delete. Article bodies are HTML files whose images reference Media Room IDs; see the X section of [platform-content.md](platform-content.md).

`content:published-caption` edits already-published provider content. `content:resolve-publishing-result` records only an outcome already verified on the connected platform; it does not publish or retry. `content:retry` can publish externally, so confirm it even though the CLI does not ask for `--yes`. `content:revert-to-draft` cancels scheduling for every scheduled edition of the same root; use its optional JSON scope preconditions when the affected editions need to be fixed explicitly. `content:schedule` and `content:intended-time` take the same optional `--json` preconditions, and `--clear` instead of `--scheduled-at` removes the time. `content:schedule`, `content:publish` and `content:retry` send an idempotency key; after a timeout, retry with the same `--idempotency-key` so the action runs once.

`content:schedule`, `content:intended-time` and `content:create` read a time without an offset in the workspace timezone; the public API rejects a `scheduledAt` without one. Use `content:intended-time` only for a planned publish time that does not schedule the content.

## Destructive or broad actions

Confirm before running any of these:

```bash
maeve content:delete --workspace <workspaceId> --id <contentId>
maeve media:delete --workspace <workspaceId> --id <mediaId>
maeve media:folders:delete --workspace <workspaceId> --id <folderId>
maeve media:labels:delete --workspace <workspaceId> --id <labelId>
maeve media:label-groups:delete --workspace <workspaceId> --id <groupId> --json label-group-delete.json
maeve media:bulk-move --workspace <workspaceId> --json media-bulk-move.json
maeve media:bulk-label --workspace <workspaceId> --json media-bulk-labels.json
maeve media:bulk-unlabel --workspace <workspaceId> --json media-bulk-labels.json
maeve campaigns:delete --workspace <workspaceId> --id <campaignId>
maeve integrations:pinterest:create-board --workspace <workspaceId> --integration <integrationId> --json pinterest-board.json --yes
maeve calendar:snapshots:create --workspace <workspaceId> --json calendar-snapshot.json
maeve calendar:snapshots:regenerate --workspace <workspaceId> --id <snapshotId> --json calendar-snapshot.json
maeve strategy:platform:start-version --workspace <workspaceId> --platform instagram
maeve strategy:platform:remove --workspace <workspaceId> --platform instagram
maeve hashtags:delete --workspace <workspaceId> --id <hashtagGroupId>
maeve inbox:resolve-message --workspace <workspaceId> --message <messageId>
maeve inbox:reopen-message --workspace <workspaceId> --message <messageId>
maeve grid:reorder --workspace <workspaceId> --json grid-reorder.json
maeve grid:remove-cover --workspace <workspaceId> --item <itemId>
maeve media:bulk-delete --workspace <workspaceId> --json media-ids.json --yes
maeve media:delete-permanent --workspace <workspaceId> --id <mediaId> --yes
maeve media:bulk-delete-forever --workspace <workspaceId> --json media-ids.json --yes
maeve campaigns:phases:delete --workspace <workspaceId> --id <campaignId> --phase-id <phaseId> --yes
maeve calendar:notes:delete --workspace <workspaceId> --id <noteId> --yes
maeve calendar:snapshots:revoke --workspace <workspaceId> --id <snapshotId> --yes
maeve strategy:goal:delete --workspace <workspaceId> --id <goalId> --yes
maeve strategy:bet:delete --workspace <workspaceId> --id <betId> --yes
maeve strategy:bet:resolve --workspace <workspaceId> --id <betId> --json bet-resolution.json --yes
maeve inbox:read-all --workspace <workspaceId> --json inbox-read-all.json --yes
maeve inbox:resolve-all --workspace <workspaceId> --json inbox-resolve-all.json --yes
maeve inbox:reply --workspace <workspaceId> --thread <threadId> --json inbox-reply.json --yes
maeve inbox:moderate --workspace <workspaceId> --message <messageId> --json inbox-moderate.json --yes
maeve inbox:retry-message --workspace <workspaceId> --message <messageId> --yes
maeve inbox:delete-failed --workspace <workspaceId> --message <messageId> --yes
maeve inbox:tags:delete --workspace <workspaceId> --tag <tagId> --yes
maeve content:recurring-occurrence:cancel --workspace <workspaceId> --occurrence <occurrenceId> --yes
maeve content:recurring-series:cancel --workspace <workspaceId> --series <seriesId> --effective-occurrence <occurrenceId> --yes
maeve grid:delete --workspace <workspaceId> --item <itemId> --yes
maeve grid:promote --workspace <workspaceId> --item <itemId> --json grid-promote.json --yes
```

Content, campaigns, inbox tags and tasks have delete commands with no CLI restore path. Media uses a separate lifecycle: `media:delete` and `media:bulk-delete` move items to the Bin and return `deleteAfter`, `media:restore` and `media:bulk-restore` bring them back, and the permanent-delete commands remove binned media irreversibly. Scheduled-post usage blocks deletion with `MEDIA_ATTACHED_TO_SCHEDULED_POST`. A bulk label toggle is composed from `media:bulk-label` and `media:bulk-unlabel`.

`media:label-groups:delete` takes `{ "mode": "delete-labels" }` or `{ "mode": "ungroup-labels" }` to say what happens to the labels in the group.

Recurring series and occurrence IDs come from `recurringMetadata` on `content:list`. Cancelling a series removes every not-yet-published occurrence from the effective occurrence forward.

`grid:reorder` needs the full item ID order for the integration.

## Approvals and client reviews

Team and client approval runs through staged reviews. A review holds ordered team or client stages and one item per content. Standard plan commands:

```bash
maeve reviews:resolve --workspace <workspaceId> --json routing.json
maeve reviews:preview --workspace <workspaceId> --json review.json
maeve reviews:create --workspace <workspaceId> --json review.json
maeve reviews:update --workspace <workspaceId> --review <reviewId> --json settings.json
maeve reviews:stages --workspace <workspaceId> --review <reviewId> --json stages.json
maeve reviews:items:add --workspace <workspaceId> --review <reviewId> --json item.json
maeve reviews:items:react --workspace <workspaceId> --review <reviewId> --item <itemId> --activity <activityId> --json reaction.json
maeve reviews:items:unreact --workspace <workspaceId> --review <reviewId> --item <itemId> --activity <activityId> --emoji 👍
maeve reviews:items:comment-attachments:init --workspace <workspaceId> --review <reviewId> --item <itemId> --json attachment.json
maeve reviews:items:comment-attachments:complete --workspace <workspaceId> --review <reviewId> --item <itemId> --attachment <attachmentId>
maeve reviews:items:comment-attachments:abort --workspace <workspaceId> --review <reviewId> --item <itemId> --attachment <attachmentId>
```

`reviews:resolve` previews which approval workflow each channel routes to. `reviews:update` and `reviews:stages` need the expected configuration revision from the latest read.

These may notify people, record a decision or publish content, so confirm first:

```bash
maeve reviews:create --workspace <workspaceId> --json review.json --yes
maeve reviews:submit --workspace <workspaceId> --json submit.json --yes
maeve reviews:send --workspace <workspaceId> --review <reviewId> --yes
maeve reviews:remind --workspace <workspaceId> --review <reviewId> --json reminder.json --yes
maeve reviews:cancel --workspace <workspaceId> --review <reviewId> --yes
maeve reviews:items:remove --workspace <workspaceId> --review <reviewId> --item <itemId> --yes
maeve reviews:items:comment --workspace <workspaceId> --review <reviewId> --item <itemId> --json comment.json --yes
maeve reviews:items:decision --workspace <workspaceId> --review <reviewId> --item <itemId> --json decision.json --yes
maeve reviews:items:decisions --workspace <workspaceId> --review <reviewId> --json decisions.json --yes
maeve reviews:items:override --workspace <workspaceId> --review <reviewId> --item <itemId> --json override.json --yes
maeve reviews:items:revoke --workspace <workspaceId> --review <reviewId> --item <itemId> --json revoke.json --yes
```

`reviews:submit` sends saved content through the routed workflow and takes an optional `--request-id` for safe retries. [safety-policy.md](safety-policy.md#cli-enforced---yes) says when `reviews:create` and `reviews:submit` need `--yes`. Record decisions, overrides or revocations only when the user is the reviewer and asks for it; include `reason` for a rejection or override. Review payload shapes are in [content-payloads.md](content-payloads.md#reviews).

## Analytics, reports and history imports

Analytics reads are safe. PDF generation needs the Standard plan, and full Instagram post history import spends the account's Instagram API allowance, so both need `--yes`:

```bash
maeve analytics:report --workspace <workspaceId> --provider instagram --json analytics-report.json --output report.pdf --yes
maeve analytics:import-history --workspace <workspaceId> --integration <integrationId> --yes
maeve analytics:import-history:status --workspace <workspaceId> --integration <integrationId>
```

- `analytics:summary` takes `--days <1-90|all>`.
- `analytics:posts` lists one account's posts; `analytics:posts-aggregate --integration-ids <ids>` compares accounts.
- `analytics:content-performance` groups by `--dimension <postTypes|mediaFormats|pillars|formats|labels|labelGroups>` and returns no rows without `--integration-ids`.
- `--post-type` and `--media-format` filter all three commands.
- Check `analytics:import-history:status` before starting an import. PDF report generation can be expensive.

## Instagram competitor research

`competitors:list`, `competitors:posts` and `competitors:benchmark` are read-only; the returned fields are described in [mcp-tools.md](mcp-tools.md#instagram-competitor-research).

- `competitors:posts --competitor <igUserId|@username>`: sort with `--sort recent|likes|comments|views|engagement|rising|discussed|reach`, filter with `--format reels|feed` or `--standouts`, and page with `--limit` (up to 60) and `--offset` against `total`.
- `competitors:benchmark` takes `--days <7-1095>`.
- Adding or removing a tracked account stays in the app. Do not suggest a CLI command for it.

## Older CLI versions

Everything above describes `maeve-cli` 0.19.0. Upgrade an older install with `npm install -g maeve-cli@latest`, or run commands through `npx maeve-cli@latest`. Minimum versions:

- 0.13.0: the CLI examples in [platform-content.md](platform-content.md).
- 0.16.0: Workbench commands.
- 0.17.0: competitor commands, and schedule times without an offset. 0.16.0 or older rejects a `scheduledAt` without an offset.
- 0.18.0: a browser login lasts 90 days from its last use. 0.17.0 or older stops using a login on the date saved when it was made.
- 0.19.0: `analytics:content-performance` and the `--post-type` and `--media-format` filters.
