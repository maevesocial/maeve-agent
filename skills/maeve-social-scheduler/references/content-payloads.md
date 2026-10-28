# Content payloads

Curated from the `maeve-cli` payload schemas. The CLI validates payloads strictly; unknown fields are rejected.

## Contents

- General Rules
- Content Create
- Thread Content
- Schedule Existing Content
- Immediate Publish
- Media
- Reviews
- Inbox
- Grid Planner
- Analytics Report

For platform-specific models and clearing behavior, read [Platform content](platform-content.md).

## General rules

- Use UUID strings for workspace, integration, content, media, approver, batch, folder, label, pillar, format, campaign, and campaign phase IDs.
- Grid planner IDs must be UUIDv4 strings.
- Scheduling times are ISO 8601. MCP reads a time without an offset, such as `2026-05-01T10:00:00`, in the workspace timezone that `workspace.list` returns. maeve-cli 0.16.0 and the public API reject a time without an offset, so add one there, for example `2026-05-01T10:00:00+10:00` or `2026-05-01T00:00:00Z`.
- Upload local media through Maeve first, then attach it with `contentMedia[].mediaId`.
- `contentMedia` accepts up to 20 attachments for `linkedin` and `linkedin-page` and up to 10 for every other platform. maeve-cli 0.16.0 still stops at 10 for every platform. Thread replies keep their own limit of 10 each. Apply the platform `maxMedia` from capabilities when it is lower.
- Base content create/update targets one `integrationId` and one integration-scoped content row. Multi-platform composition should fan out to separate grouped rows.
- For drafts, omit `intent` or include `intent: "draft"`.
- `content:update` uses the create-content shape except `intent` and `scheduledAt`, and at least one field must be present. Use scheduling or revert-to-draft commands to change publication time.

## Content create

Minimal draft:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "captions": {
    "canonical": "Launch post copy"
  },
  "intent": "draft"
}
```

Scheduled create payload, only when the user provided an explicit date/time/timezone. `scheduledAt` is only allowed when `intent` is `schedule`:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "captions": {
    "canonical": "Launch post copy"
  },
  "contentMedia": [
    {
      "mediaId": "00000000-0000-4000-8000-000000000002"
    }
  ],
  "scheduledAt": "2026-05-01T10:00:00+10:00",
  "postType": "post",
  "intent": "schedule"
}
```

Platform-aware payload with taxonomy:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "internalTitle": "Launch planning card",
  "publishTitle": "Launch title",
  "captions": {
    "canonical": "Launch post copy"
  },
  "contentMedia": [
    {
      "mediaId": "00000000-0000-4000-8000-000000000002",
      "order": 0,
      "cover": {
        "thumbOffsetMs": 2500
      }
    }
  ],
  "postType": "reel",
  "pillarIds": ["00000000-0000-4000-8000-000000000003"],
  "formatIds": ["00000000-0000-4000-8000-000000000004"],
  "labelIds": ["00000000-0000-4000-8000-000000000005"],
  "campaignId": "00000000-0000-4000-8000-000000000006",
  "campaignPhaseId": "00000000-0000-4000-8000-000000000007",
  "priority": "medium",
  "intent": "draft"
}
```

Supported content fields:

- `integrationId`: required UUID.
- `internalTitle`: optional internal planning title, max 500 characters. Never published.
- `publishTitle`: optional provider-facing title, max 500 characters. Required before scheduling/publishing when capabilities say `requiresTitle`.
- `notes`: optional rich-text HTML, max 100000 characters. Internal - never published. Use to attach planning context, links, or meeting notes to a content item.
- `captions`: optional object with `canonical` publish text and optional platform overrides.
- `contentMedia`: optional uploaded media relationships, within the attachment limit in General rules.
- `scheduledAt`: optional ISO 8601 time with an offset; see General rules.
- `settings`: optional object for platform fields from capabilities.
- `postType`: optional `post`, `reel`, `story`, `thread`, or `poll` (LinkedIn only; requires the top-level `poll` object).
- `poll`: LinkedIn poll content, only with `postType: "poll"`. Question up to 140 characters, 2 to 4 options up to 30 characters each, `duration` of `ONE_DAY`, `THREE_DAYS`, `SEVEN_DAYS`, or `FOURTEEN_DAYS`, `voteSelectionType: "SINGLE_VOTE"`, `isVoterVisibleToAuthor: true`. Polls cannot carry media, documents, or article content.
- `firstComment`, `shareToFeed`: optional publish behavior fields.
- `contentMedia[].crops`, `contentMedia[].userTags`, `contentMedia[].productTags`, `contentMedia[].cover.coverMediaId`, `contentMedia[].cover.thumbOffsetMs`: optional media metadata where platform capabilities allow it.
- `threadMessages`: optional array for thread-style content, max 20 items.
- `pillarIds`, `formatIds`, `labelIds`: optional UUID arrays.
- `campaignId`, `campaignPhaseId`: optional campaign UUIDs; use `null` to clear campaign links on update.
- `assigneeIds`: optional UUID array.
- `priority`: optional `urgent`, `high`, `medium`, or `low`.
- `intent`: optional `draft`, `schedule`, or `publish_now`. Omitted intent defaults to `draft`.

## LinkedIn poll content

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "captions": { "canonical": "Which format should we publish next?" },
  "postType": "poll",
  "poll": {
    "question": "Which format should we publish next?",
    "options": [{ "text": "Guide" }, { "text": "Checklist" }],
    "duration": "THREE_DAYS",
    "voteSelectionType": "SINGLE_VOTE",
    "isVoterVisibleToAuthor": true
  },
  "intent": "draft"
}
```

## Thread content

Use `postType: "thread"` only when the integration capabilities support threads.

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "captions": {
    "canonical": "Thread starter"
  },
  "postType": "thread",
  "threadMessages": [
    {
      "captions": {
        "canonical": "Second post in the thread"
      }
    },
    {
      "captions": {
        "canonical": "Third post with media"
      },
      "contentMedia": [
        {
          "mediaId": "00000000-0000-4000-8000-000000000002"
        }
      ]
    }
  ],
  "intent": "draft"
}
```

`threadMessages` accepts up to 20 items; each item uses `captions.canonical` and can include `contentMedia`.

## Schedule existing content

CLI command:

```bash
maeve content:schedule --workspace <workspaceId> --id <contentId> --scheduled-at "2026-05-01T10:00:00+10:00"
```

Equivalent payload shape:

```json
{
  "scheduledAt": "2026-05-01T10:00:00+10:00"
}
```

## Immediate publish

Publishing uses an existing content ID, not a JSON payload:

```bash
maeve content:publish --workspace <workspaceId> --id <contentId> --yes
```

Confirm first because this is externally visible.

## Published caption edit

This replaces the live caption of an already-published Facebook, LinkedIn or LinkedIn Page root post when `allowedActions.canUpdatePublishedCaption` is true. On LinkedIn only the text changes, up to 3,000 characters:

```bash
maeve content:published-caption --workspace <workspaceId> --id <contentId> --json published-caption.json --yes
```

Payload shape:

```json
{
  "message": "Updated caption"
}
```

Confirm first because this changes externally visible provider content.

## Media

Upload files first:

```bash
maeve media:upload ./image.png --workspace <workspaceId>
```

Supported upload extensions:

- Images: `.jpg`, `.jpeg`, `.png`, `.gif`, `.webp`, `.avif`, max 50 MB.
- Videos: `.mp4`, `.mov`, max 512 MB.

Update media:

```json
{
  "filename": "campaign-hero.png",
  "altText": "Campaign hero image",
  "isFavorite": true,
  "folderId": null
}
```

Folders and labels:

```json
{ "name": "Launch assets" }
```

```json
{ "name": "Approved", "color": "#22cc88" }
```

Move folder to root:

```json
{ "parentId": null }
```

Bulk media IDs:

```json
{ "mediaIds": ["00000000-0000-4000-8000-000000000001"] }
```

Bulk label payload:

```json
{
  "mediaIds": ["00000000-0000-4000-8000-000000000001"],
  "labelIds": ["00000000-0000-4000-8000-000000000002"]
}
```

Media constraints:

- Update payloads must include at least one field.
- Folder/label names are required when creating.
- Label colors must be hex colors.
- Bulk media IDs require 1 to 100 IDs.
- Label ID lists require 1 to 50 IDs.

## Reviews

A review holds one item per content and ordered team or client stages. The CLI rejects unknown fields.

Create a review with `reviews:create`. `sendNow: true` notifies the first stage:

```json
{
  "items": [{ "contentId": "00000000-0000-4000-8000-000000000001" }],
  "stages": [
    {
      "kind": "team",
      "rule": "any",
      "deadlineDays": null,
      "participants": [{ "userId": "00000000-0000-4000-8000-000000000002" }]
    },
    {
      "kind": "client",
      "rule": "all",
      "deadlineDays": 3,
      "participants": [{ "name": "Client Reviewer", "email": "reviewer@example.com" }]
    }
  ],
  "label": "May launch",
  "inviteNote": "Please review when you have a moment.",
  "sendNow": false
}
```

- `kind` is `team` or `client`, `rule` is `any` or `all`, and `inOrder` is optional.
- Team participants use member `userId` values from `workspaces:members`. Client participants use `name` and `email`.
- Send `deadlineDays` on every stage, as a whole number of days or `null` for no reminder. The API rejects a stage without it.
- `expiresAt` is optional and takes an ISO 8601 time with an offset.
- `reviews:preview` takes `items` and optional `stages`. `reviews:items:add` takes `{ "contentId": "..." }`. `reviews:remind` takes optional `itemIds`.

Decision for `reviews:items:decision`:

```json
{
  "decision": "changes_requested",
  "reason": "Please revise the CTA.",
  "expectedVersionId": "00000000-0000-4000-8000-000000000003",
  "expectedStageId": "00000000-0000-4000-8000-000000000004",
  "expectedConfigRevision": 2,
  "mutationId": "00000000-0000-4000-8000-000000000005"
}
```

- `decision` is `approved` or `changes_requested`. `reason` is optional.
- Read `reviews:get` first. `expectedVersionId` is the item's `currentVersionId`, `expectedStageId` is its `currentStageId`, and `expectedConfigRevision` is the review's `configRevision`.
- Generate a new UUID as `mutationId` for each decision, and reuse it only to replay the same decision.
- `reviews:items:decisions` takes `{ "decisions": [...] }` with the same fields plus `itemId`. `reviews:items:override` needs `reason` and the same expected fields. `reviews:items:revoke` also takes `decisionId` and optional `requestChanges`.

Comment for `reviews:items:comment`:

```json
{
  "reviewItemId": "00000000-0000-4000-8000-000000000007",
  "expectedVersionId": "00000000-0000-4000-8000-000000000003",
  "audience": "team",
  "body": "Looks good to me."
}
```

`audience` is `team` or `client`. Include `body` or `attachmentId`.

Comment attachment init:

```json
{
  "contentId": "00000000-0000-4000-8000-000000000001",
  "reviewId": "00000000-0000-4000-8000-000000000006",
  "reviewItemId": "00000000-0000-4000-8000-000000000007",
  "expectedVersionId": "00000000-0000-4000-8000-000000000003",
  "audience": "team",
  "filename": "feedback.png",
  "contentType": "image/png",
  "fileSize": 123456
}
```

Allowed attachment content types: `image/jpeg`, `image/png`, `image/gif`, `image/webp`.

Reaction for `reviews:items:react`:

```json
{ "emoji": "👍" }
```

`emoji` is one of 👍, ❤️, 😂, 🎉 or 👀. `reviews:items:unreact --emoji` takes the same character.

## Inbox

Public reply:

```json
{
  "content": "Thanks for reaching out.",
  "parentMessageId": "00000000-0000-4000-8000-000000000001"
}
```

Attachment reply:

```json
{
  "attachment": {
    "type": "image",
    "url": "https://cdn.example.com/reply.png"
  }
}
```

Internal note:

```json
{ "content": "Follow up with the team before replying." }
```

Moderation:

```json
{ "action": "hide" }
```

Allowed moderation actions: `hide`, `unhide`, `delete`.

Read-all filter:

```json
{
  "platform": "instagram",
  "status": "open",
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "messageType": "comment"
}
```

Use `{}` only when the user explicitly wants all matching threads marked read.

## Grid planner

Visual-only item:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "kind": "visual_only",
  "mediaIds": ["00000000-0000-4000-8000-000000000002"],
  "coverMediaId": "00000000-0000-4000-8000-000000000002",
  "note": "Plan this visual",
  "settings": { "aspectRatio": 1 }
}
```

Linked content item:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "kind": "linked_post",
  "linkedContentId": "00000000-0000-4000-8000-000000000003"
}
```

Update item:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "note": "Updated planning note",
  "settings": { "aspectRatio": 1 }
}
```

Reorder requires the complete item order for the integration:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "itemIds": ["00000000-0000-4000-8000-000000000004"]
}
```

Replace media:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "mediaIds": ["00000000-0000-4000-8000-000000000002"]
}
```

Set cover:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "mediaId": "00000000-0000-4000-8000-000000000005"
}
```

Promote:

```json
{ "integrationId": "00000000-0000-4000-8000-000000000001" }
```

Grid constraints:

- `visual_only` requires `mediaIds`.
- `linked_post` requires `linkedContentId`.
- `mediaIds` and `itemIds` must be unique.
- `coverMediaId` can be a UUIDv4 string or null.
- `settings.aspectRatio` can be null or a number from 0.1 to 10.
- Replace-media accepts 1 to 10 media IDs.

## Analytics report

PDF report payload:

```json
{
  "integrationIds": ["00000000-0000-4000-8000-000000000001"],
  "days": 30,
  "titlePage": {
    "enabled": true,
    "title": "Monthly report",
    "description": "Performance summary"
  },
  "sections": {
    "demographics": true,
    "topPosts": true
  }
}
```

Rules:

- Include `integrationId` or unique `integrationIds`.
- `integrationIds` can contain 1 to 20 UUIDs.
- `days` is `all` or an integer from 1 to 90.
- `titlePage.title` is max 200 characters.
- `titlePage.description` is max 1000 characters.
