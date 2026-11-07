# OpenAI review

On 28 October 2026, OpenAI rejected version 3.0.0 of the [replacement listing](https://platform.openai.com/plugins/manage/plugin_asdk_app_6ac1a331c0248191827893f61e4504e2). One reason was commerce for disallowed offerings, such as digital goods or services. OpenAI also does not allow plugins to serve advertisements. Version 3.0.1 removes paid ads from the OpenAI plugin and has not been uploaded. The original 2.0.0 listing was retained during review.

## Package

- Version 3.0.1 uses `https://api.maevesocial.com/mcp/openai` and its own direct-tool skill.
- The OpenAI plugin offers no paid ads. Its skill does not mention ads, boosts or ad tools, and `/mcp/openai` offers no ads tools. Ads remain available to other clients through the shared skill. The package declares `commerce: false`.
- On `/mcp/openai`, `create_draft_content` and `create_draft_content_batch` only create drafts, so their names match what they do and they are neither destructive nor open-world; scheduling uses `schedule_content` and `schedule_content_batch`. `create_task` and `create_task_comment` are destructive because they notify people, and read-only tools no longer write review projections. Deploy these backend changes before uploading 3.0.1.
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
| Package skill checks | Passed in the Portal for 3.0.0; not run for 3.0.1 |
| MCP scan | Latest 3.0.0 scan did not complete; rescan after uploading 3.0.1 |
| Five positive and three negative review cases | Run in ChatGPT against production on 7 November 2026 with a developer-mode connector; all eight met their expected behavior. Case 2 now names `list_calendar`, the tool ChatGPT used. Rerun after the backend changes deploy and against the uploaded package |
| Reviewer access | Verified and supplied privately |
| Developer attestations and submission | Completed for 3.0.0; 3.0.1 not uploaded or submitted |
| Approval and publication | 3.0.0 rejected; 3.0.1 pending |

The supplied [unlisted demo](https://youtu.be/xpMgHQmJu_g) plays and is included in the package. Frames sampled on 7 November show no ads or paid features, but it shows the 2.0.0 workflow in August, not the review cases. A 3.0.1 walkthrough of the eight review cases with the reviewer account remains a follow-up.

The terms inspected on 12 October limit accounts to Australia, New Zealand and the United States. The owner chose all-country targeting and will resolve that mismatch; it is not recorded as resolved.

Before submitting 3.0.1, deploy the backend changes, upload the exact package, complete the MCP scan and run the review cases against it. Uploading, submitting and publishing are separate actions.
