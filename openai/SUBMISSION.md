# OpenAI review

On 28 October 2026, OpenAI rejected version 3.0.0 of the [replacement listing](https://platform.openai.com/plugins/manage/plugin_asdk_app_6ac1a331c0248191827893f61e4504e2). One reason was commerce for disallowed offerings, such as digital goods or services. OpenAI also does not allow plugins to serve advertisements. Version 3.0.1 removes paid ads from the OpenAI plugin. OpenAI approved 3.0.1 and the owner published it on 25 November 2026. The owner subsequently unpublished the original 2.0.0 listing on `/mcp`. The Portal still shows that version as approved.

## Package

- Version 3.0.1 uses `https://api.maevesocial.com/mcp/openai` and its own direct-tool skill.
- The OpenAI plugin offers no paid ads. Its skill does not mention ads, boosts or ad tools, and `/mcp/openai` offers no ads tools. Ads remain available to other clients through the shared skill. The package declares `commerce: false`.
- On `/mcp/openai`, `create_draft_content` and `create_draft_content_batch` only create drafts, so their names match what they do and they are neither destructive nor open-world; scheduling uses `schedule_content` and `schedule_content_batch`. `create_task` and `create_task_comment` are destructive because they notify people, and read-only tools no longer write review projections. These backend changes are in production.
- The negative review cases no longer include a subscription cancellation, so the package raises no billing topic. A live inbox reply request replaces it.
- The package name remains `app-6a8ccd4370188191932fe94b18d9b3eb`. It is independent of the replacement Portal app ID.
- The listing describes Maeve Social as a social media scheduler and management platform, with the existing icon for the listing and composer.

## Directory copy for the next metadata update

Checked against the [Maeve Social homepage](https://maevesocial.com) and the [published ChatGPT listing](https://chatgpt.com/plugins/plugin_asdk_app_6ac1a331c0248191827893f61e4504e2) on 25 November 2026. The live 3.0.1 tagline is “Social media scheduler”. The homepage describes the wider product as organic and paid social media management and names agencies, brands, creators and founders. Version 3.0.2 includes the following copy for review.

**Short description:** Plan and schedule organic social content

**Description:** Maeve Social brings organic and paid social media management into one workspace for agencies, businesses, creators and founders. In ChatGPT, you can plan organic content, turn saved media into drafts, check a client’s content calendar and see which posts performed best. You can also organize tasks and configure inbox auto-replies. Scheduling and publishing require your confirmation. Manage paid campaigns in the Maeve Social web app. A Maeve Social account is required.

Directory copy changes require a new package version and review on the existing listing.
- Publisher: LILY DIA PTY LTD. The owner selected all supported countries. Subscriptions are purchased only through Maeve's website.
- Dedicated reviewer credentials and instructions are saved in private Portal fields. The account is restricted to one populated review workspace; fresh sign-in, workspace isolation, saved media, Facebook and Threads accounts, and analytics were verified. Credentials and recovery copies stay outside this repository and the ZIP.

## Verification and follow-up

| Check | Status |
| --- | --- |
| Production transport and OAuth | Stateless rollout and scoped authentication verified separately |
| Portal connection | Authorized; domain verified; direct tools discovered |
| Package skill checks | Passed in the Portal for 3.0.1 |
| MCP scan | The 7 November scan found 139 tools. After publication, the Portal labeled `list_instagram_competitors`, `get_instagram_competitor_benchmark` and `list_instagram_competitor_posts` Published. The MCP tab still showed Needs attention, with no completed scan since connection and Rescan disabled. |
| Five positive and three negative review cases | Run in ChatGPT against production on 7 November 2026 with a developer-mode connector; all eight met their expected behavior. Case 2 now names `list_calendar`, the tool ChatGPT used. Rerun after the backend changes deployed; all eight passed again |
| Reviewer access | Verified for 3.0.0. The owner supplies the credentials privately in the Portal |
| Developer attestations and submission | Completed for 3.0.0; 3.0.1 submitted |
| Approval and publication | 3.0.1 published and listed publicly on 25 November 2026. Installation from the public listing verified in ChatGPT. |
| Published plugin workspace lookup | On 25 November 2026, a chat opened from the installed 3.0.1 listing returned four workspace names for the connected account. The test used a read-only request. |

The [unlisted 3.0.1 demo](https://youtu.be/oJncUHJkV1c) runs 93 seconds without sound. It was recorded in ChatGPT against production on 7 November with the developer-mode connector and the owner's Development workspace, not the reviewer account. It shows four review cases: workspace discovery, this week's schedule, a draft created without scheduling or publishing, and a refused approval on a client's behalf. The draft was deleted afterwards.

The terms inspected on 12 October limit accounts to Australia, New Zealand and the United States. The owner chose all-country targeting and will resolve that mismatch; it is not recorded as resolved.

OpenAI scans hosted MCP tools daily. Eligible changes go live after automated checks. Changes to package metadata, assets or skills require a new ZIP and review on the existing listing. See the [submission guide](https://developers.openai.com/plugins/deploy/submission). The effect of unpublishing 2.0.0 on existing installations is unverified.
