# OpenAI review

On 28 October 2026, OpenAI rejected version 3.0.0 of the [replacement listing](https://platform.openai.com/plugins/manage/plugin_asdk_app_6ac1a331c0248191827893f61e4504e2). One reason was commerce for disallowed offerings, such as digital goods or services. OpenAI also does not allow plugins to serve advertisements. Version 3.0.1 removes paid ads from the OpenAI plugin and has not been uploaded. The original 2.0.0 listing was retained during review.

## Package

- Version 3.0.1 uses `https://api.maevesocial.com/mcp/openai` and its own direct-tool skill.
- The OpenAI plugin offers no paid ads. Its skill does not mention ads, boosts or ad tools, and the backend is removing the ads tools from `/mcp/openai`. Ads remain available to other clients through the shared skill. Deploy that backend change and the tool annotation and description corrections before uploading 3.0.1.
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
| Five positive and three negative review cases | Included in the package; not recorded as passed against the final uploaded version |
| Reviewer access | Verified and supplied privately |
| Developer attestations and submission | Completed for 3.0.0; 3.0.1 not uploaded or submitted |
| Approval and publication | 3.0.0 rejected; 3.0.1 pending |

The supplied [unlisted demo](https://youtu.be/xpMgHQmJu_g) plays and is included in the package. It shows an earlier plugin workflow, not the current version, and has not been checked for paid ads. A current-version walkthrough and full recording review remain follow-ups.

The terms inspected on 12 October limit accounts to Australia, New Zealand and the United States. The owner chose all-country targeting and will resolve that mismatch; it is not recorded as resolved.

Before submitting 3.0.1, deploy the backend changes, upload the exact package, complete the MCP scan and run the review cases against it. Uploading, submitting and publishing are separate actions.
