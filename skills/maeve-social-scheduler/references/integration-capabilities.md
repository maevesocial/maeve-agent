# Integration capabilities

Quick-glance reference for the live capability catalog returned by `integrations:capabilities`.

## Contents

- Commands
- Response shape
- Settings and media metadata
- Platform matrix
- Agent rules

## Commands

```bash
maeve integrations:list --workspace <workspaceId>
maeve integrations:capabilities --workspace <workspaceId> --integration <integrationId>
maeve integrations:options --workspace <workspaceId> --integration <integrationId> --key <optionKey>
```

Prefer MCP operation `integrations.get_options` through `maeve_read` for dynamic options when connected; `integrations:options` is the CLI fallback. When the option key needs input, such as YouTube region, location search, Instagram audio search or the TikTok music chart, pass a body with `--json <file>`:

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
- `settings.fields`: supported platform settings, with allowed values and any `optionKey`.
- `options`: dynamic option keys available for that integration.
- `capabilities`: curated provider capability data from the connection, when present.

Capabilities include `videoCover`: `null` when the platform publishes no video, otherwise `{ image, frame, postTypes, required, note }`. `image` and `frame` say whether each kind of cover is applied, `postTypes` lists where (for example `post` and `reel`, not `story`), `required` is true when a video needs a cover, and `note` explains a platform that chooses its own frame.

Integration status is one of:

- `connected`: safe to use.
- `requires_reauth`: ask the user to reconnect the social account.
- `disabled`: ask the user to enable it or choose another integration.
- `auth_incomplete`: ask the user to finish provider authorization.

Do not mutate content through an integration unless its status is `connected`.

## Settings and media metadata

`settings.fields` contains provider publish settings only. Do not place media metadata or editor state in `settings`. Per-media metadata goes in `contentMedia`:

- `contentMedia[].crops`: optional crop data keyed by platform.
- `contentMedia[].userTags`: optional user tags where supported.
- `contentMedia[].productTags`: optional product tags where supported.
- `contentMedia[0].cover`: optional video cover; see the shared content model in [platform-content.md](platform-content.md#shared-content-model).

## Platform matrix

| Platform                  | Post types                | Media types                  | Max media | Threads | Requires media | Requires title |
| ------------------------- | ------------------------- | ---------------------------- | --------: | ------- | -------------- | -------------- |
| `x`                       | `post`, `thread`          | `image`, `gif`, `video`      |         4 | yes     | no             | no             |
| `linkedin`                | `post`, `article`, `poll` | `image`, `video`, `document` |        20 | no      | no             | no             |
| `linkedin-page`           | `post`, `article`, `poll` | `image`, `video`, `document` |        20 | no      | no             | no             |
| `instagram`               | `post`, `reel`, `story`   | `image`, `video`             |        20 | no      | yes            | no             |
| `facebook`                | `post`, `reel`, `story`   | `image`, `video`             |        10 | no      | no             | no             |
| `facebook-page`           | `post`, `reel`, `story`   | `image`, `video`             |        10 | no      | no             | no             |
| `threads`                 | `post`, `thread`          | `image`, `video`             |        20 | yes     | no             | no             |
| `tiktok`                  | `post`                    | `image`, `video`             |        35 | no      | yes            | no             |
| `youtube`                 | `post`, `reel`            | `video`                      |         1 | no      | yes            | yes            |
| `pinterest`               | `post`                    | `image`, `video`             |         1 | no      | yes            | yes            |
| `google-business-profile` | `post`                    | `image`                      |         1 | no      | no             | no             |

X also supports `article`, which only the article operations create (`articles.create`, `articles:create`, `POST /articles`), never content create.

The table describes provider capabilities. Content input also caps `contentMedia` at 20 attachments for `instagram`, `linkedin` and `linkedin-page` and 10 for every other platform; apply whichever is lower. Read [platform-content.md](platform-content.md) for per-platform constraints and reverse states.

Unknown platforms fall back to common post types `post`, `reel`, `story`, and `thread`; media types `image`, `video`; max media 10; no required media/title; no thread support.

## Agent rules

- Call `integrations:capabilities` before setting platform-specific fields, and use the live `settings.fields`, `options` and `rules` for the selected integration over any static copy.
- When a settings field has `optionKey`, fetch its options before choosing a value.
- If `requiresMedia` is true, upload or select media and attach it before scheduling or publishing.
- If `requiresTitle` is true, include `publishTitle` before scheduling or publishing.
- Keep `contentMedia` within `maxMedia` and the content attachment cap above, using only the listed `mediaTypes`.
- Choose a supported `postType`, and use the top-level `postType` only. Do not send platform-specific post type override settings.
- Put platform-specific values in `settings` using supported keys from `settings.fields`.
- Do not expose tokens, scopes, stored provider settings, or raw provider payloads; capabilities and options are the safe discovery surface.
