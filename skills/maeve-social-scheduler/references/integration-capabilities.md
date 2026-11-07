# Integration capabilities

Quick-glance reference for the live capability catalog returned by `integrations:capabilities`.

## Contents

- Commands
- Response shape
- Settings and media metadata
- Platform matrix
- Platform settings and options
- Agent rules

## Commands

List integrations:

```bash
maeve integrations:list --workspace <workspaceId>
```

Get safe publishing requirements:

```bash
maeve integrations:capabilities --workspace <workspaceId> --integration <integrationId>
```

Fetch dynamic options returned by capabilities. Prefer MCP operation `integrations.get_options` through `maeve_read` when connected; use the CLI fallback when MCP is unavailable:

```bash
maeve integrations:options --workspace <workspaceId> --integration <integrationId> --key <optionKey>
```

Use `--json <file>` for option bodies:

```json
{ "regionCode": "AU" }
```

Supported option body fields:

- `regionCode`: optional string, max 2 characters.
- `query`: optional string, max 255 characters.
- `audioType`: required for `instagram-audio`; use `music` or `original_sound`.
- `countryCode`: required chart country for `tiktok-cml-tracks`.
- `genre`: optional TikTok chart genre, default `ALL`.
- `dateRange`: optional `1DAY`, `7DAY`, `30DAY` or `90DAY`, default `7DAY`.

## Response shape

Capabilities returns:

- `integration`: `id`, `platform`, `username`, `name`, and `status`.
- `content`: `postTypes`, `mediaTypes`, `maxMedia`, `supportsThreads`, `requiresMedia`, and `requiresTitle`.
- `rules`: live plain-text platform publishing rules from the backend. Treat this as the source of truth for platform-specific requirements.
- `settings.fields`: supported platform settings.
- `options`: dynamic option keys available for that integration.
- `capabilities`: curated provider capability data from the connection, when present.

Integration status is one of:

- `connected`
- `requires_reauth`
- `disabled`
- `auth_incomplete`

Do not mutate content through an integration unless its status is `connected`.

## Settings and media metadata

`settings.fields` contains provider publish settings only. Do not place media metadata or editor state in `settings`.

Use `contentMedia` for per-media metadata:

- `contentMedia[].crops`: optional crop data keyed by platform.
- `contentMedia[].userTags`: optional user tags where supported.
- `contentMedia[].productTags`: optional product tags where supported.
- `contentMedia[0].cover.coverMediaId`: optional Media Room image for a video's cover (JPEG, PNG or WebP; Maeve converts it per platform).
- `contentMedia[0].cover.thumbOffsetMs`: optional frame of the video, in milliseconds from the start; it must be within the video. When both are set, the image wins.

Capabilities include `videoCover`: `null` when the platform publishes no video, otherwise `{ image, frame, postTypes, required, note }`. `image` and `frame` say whether each kind of cover is applied, `postTypes` lists where (for example `post` and `reel`, not `story`), `required` is true when a video needs a cover, and `note` explains a platform that chooses its own frame.

## Platform matrix

| Platform                  | Post types                | Media types                  | Max media | Threads | Requires media | Requires title |
| ------------------------- | ------------------------- | ---------------------------- | --------: | ------- | -------------- | -------------- |
| `x`                       | `post`, `thread`          | `image`, `gif`, `video`      |         4 | yes     | no             | no             |
| `linkedin`                | `post`, `article`, `poll` | `image`, `video`, `document` |        20 | no      | no             | no             |
| `linkedin-page`           | `post`, `article`, `poll` | `image`, `video`, `document` |        20 | no      | no             | no             |
| `instagram`               | `post`, `reel`, `story`   | `image`, `video`             |        10 | no      | yes            | no             |
| `facebook`                | `post`, `reel`, `story`   | `image`, `video`             |        10 | no      | no             | no             |
| `facebook-page`           | `post`, `reel`, `story`   | `image`, `video`             |        10 | no      | no             | no             |
| `threads`                 | `post`, `thread`          | `image`, `video`             |        20 | yes     | no             | no             |
| `tiktok`                  | `post`                    | `image`, `video`             |        35 | no      | yes            | no             |
| `youtube`                 | `post`, `reel`            | `video`                      |         1 | no      | yes            | yes            |
| `pinterest`               | `post`                    | `image`, `video`             |         1 | no      | yes            | yes            |
| `google-business-profile` | `post`                    | `image`                      |         1 | no      | no             | no             |

X also supports `article`, which only the article operations create (`articles.create`, `articles:create`, `POST /articles`), never content create.

The table describes provider capabilities. Content input also has an [attachment limit](content-payloads.md#general-rules); apply whichever is lower. Read [Platform content](platform-content.md) for current model constraints and reverse states.

Unknown platforms fall back to common post types `post`, `reel`, `story`, and `thread`; media types `image`, `video`; max media 10; no required media/title; no thread support.

## Platform settings and options

Read `settings.fields`, `options` and `rules` from live capabilities for the selected integration. They list each platform's settings, allowed values and option keys, and they replace any static copy. [Platform content](platform-content.md) covers how to use them per platform.

## Agent rules

- Call `integrations:capabilities` before platform-specific settings.
- Read and follow the live `rules` string returned by capabilities; do not rely on static prose for platform-specific publishing requirements.
- When a settings field has `optionKey`, execute MCP `integrations.get_options` through `maeve_read` before choosing a value, or use CLI `integrations:options` as the fallback.
- If `requiresMedia` is true, attach uploaded media before scheduling or publishing.
- If `requiresTitle` is true, include `publishTitle` before scheduling or publishing.
- Respect both `maxMedia` and the [attachment limit](content-payloads.md#general-rules) when building `contentMedia`.
- Use the top-level `postType` only. Do not send platform-specific post type override settings.
- Do not expose tokens, scopes, stored provider settings, or raw provider payloads; capabilities and options are the safe discovery surface.
