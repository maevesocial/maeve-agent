# OpenAI review

On 28 October 2026, OpenAI rejected version 3.0.0 of the [replacement listing](https://platform.openai.com/plugins/manage/plugin_asdk_app_6ac1a331c0248191827893f61e4504e2). One reason was commerce for disallowed offerings, such as digital goods or services. OpenAI also does not allow plugins to serve advertisements. Version 3.0.1 removes paid ads from the OpenAI plugin. OpenAI approved it; on 25 November 2026 the Portal showed it as ready to publish and not published. The original 2.0.0 listing is still the public one, on `/mcp`.

## Package

- Version 3.0.1 uses `https://api.maevesocial.com/mcp/openai` and its own direct-tool skill.
- The OpenAI plugin offers no paid ads. Its skill does not mention ads, boosts or ad tools, and `/mcp/openai` offers no ads tools. Ads remain available to other clients through the shared skill. The package declares `commerce: false`.
- On `/mcp/openai`, `create_draft_content` and `create_draft_content_batch` only create drafts, so their names match what they do and they are neither destructive nor open-world; scheduling uses `schedule_content` and `schedule_content_batch`. `create_task` and `create_task_comment` are destructive because they notify people, and read-only tools no longer write review projections. These backend changes are in production.
- The negative review cases no longer include a subscription cancellation, so the package raises no billing topic. A live inbox reply request replaces it.
- The package name remains `app-6a8ccd4370188191932fe94b18d9b3eb`. It is independent of the replacement Portal app ID.
- The listing describes Maeve Social as a social media scheduler and management platform, with the existing icon for the listing and composer.
- Publisher: LILY DIA PTY LTD. The owner selected all supported countries. Subscriptions are purchased only through Maeve's website.
- Dedicated reviewer credentials and instructions are saved in private Portal fields. The account is restricted to one populated review workspace; fresh sign-in, workspace isolation, saved media, Facebook and Threads accounts, and analytics were verified. Credentials and recovery copies stay outside this repository and the ZIP.

## Verification and follow-up

| Check | Status |
| --- | --- |
| Production transport and OAuth | Stateless rollout and scoped authentication verified separately |
| Portal connection | Authorized; domain verified; direct tools discovered |
| Package skill checks | Passed in the Portal for 3.0.1 |
| MCP scan | 3.0.1 scanned on 7 November: 139 tools, with the expected draft and task annotations. The Portal marks `list_instagram_competitors`, `get_instagram_competitor_benchmark` and `list_instagram_competitor_posts` as new tool definitions needing further review, without a reason. On 25 November the MCP tab showed Needs attention: the MCP URL read Endpoint unavailable, no scan had finished since the account was connected, and the three tools were still flagged |
| Five positive and three negative review cases | Run in ChatGPT against production on 7 November 2026 with a developer-mode connector; all eight met their expected behavior. Case 2 now names `list_calendar`, the tool ChatGPT used. Rerun after the backend changes deployed; all eight passed again |
| Reviewer access | Verified for 3.0.0. The owner supplies the credentials privately in the Portal |
| Developer attestations and submission | Completed for 3.0.0; 3.0.1 submitted |
| Approval and publication | 3.0.0 rejected; 3.0.1 approved and ready to publish, not published as of 25 November 2026 |

The [unlisted 3.0.1 demo](https://youtu.be/oJncUHJkV1c) runs 93 seconds without sound. It was recorded in ChatGPT against production on 7 November with the developer-mode connector and the owner's Development workspace, not the reviewer account. It shows four review cases: workspace discovery, this week's schedule, a draft created without scheduling or publishing, and a refused approval on a client's behalf. The draft was deleted afterwards.

The terms inspected on 12 October limit accounts to Australia, New Zealand and the United States. The owner chose all-country targeting and will resolve that mismatch; it is not recorded as resolved.

Approval does not publish. Before publishing 3.0.1, clear the MCP tab's Needs attention state: reconnect the server so a scan finishes, then select **Publish plugin** on the approved version. OpenAI's [submission guide](https://developers.openai.com/plugins/deploy/submission) says a held tool does not block the others: the three flagged competitor tools stay unavailable in ChatGPT until approved, through **Appeal** or a corrected definition and a rescan. After publication OpenAI scans the server daily and tool changes go live without a new package; a package or skill change needs a new version and review. The guide does not say how to retire the 2.0.0 listing, so ask OpenAI support before unpublishing it.
