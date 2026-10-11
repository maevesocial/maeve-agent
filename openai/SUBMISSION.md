# OpenAI 3.0.0 review

Checked on 12 October 2026: the [replacement listing](https://platform.openai.com/plugins/manage/plugin_asdk_app_6ac1a331c0248191827893f61e4504e2) is submitted and in review, not published. The original 2.0.0 listing remains available during review.

## Submitted package

- Version 3.0.0 uses `https://api.maevesocial.com/mcp/openai` and its own direct-tool skill.
- The package name remains `app-6a8ccd4370188191932fe94b18d9b3eb`. It is independent of the replacement Portal app ID.
- The listing describes Maeve Social as a social media scheduler and management platform, with the existing icon for the listing and composer.
- Publisher: LILY DIA PTY LTD. The owner selected all supported countries. Subscriptions are purchased only through Maeve's website.
- Dedicated reviewer credentials and instructions are saved in private Portal fields. The account is restricted to one populated review workspace; fresh sign-in, workspace isolation, saved media, Facebook and Threads accounts, and analytics were verified. Credentials and recovery copies stay outside this repository and the ZIP.

## Verification and follow-up

| Check | Status |
| --- | --- |
| Production transport and OAuth | Stateless rollout and scoped authentication verified separately |
| Portal connection | Authorized; domain verified; direct tools discovered |
| Package skill checks | Passed in the Portal |
| MCP scan | Latest scan did not complete; Portal requests a rescan |
| Five positive and three negative review cases | Included in the package; not recorded as passed against the final uploaded version |
| Reviewer access | Verified and supplied privately |
| Developer attestations and submission | Completed by the owner; listing is in review |
| Approval and publication | Pending |

The supplied [unlisted demo](https://youtu.be/xpMgHQmJu_g) plays and is included in the package. It shows an earlier plugin workflow, not version 3.0.0. A current-version walkthrough and full recording review remain follow-ups.

The terms inspected on 12 October limit accounts to Australia, New Zealand and the United States. The owner chose all-country targeting and will resolve that mismatch; it is not recorded as resolved.

Resolve the incomplete scan and any reviewer feedback before publication. Do not cancel or replace the submitted version merely to update this record. Uploading, submitting and publishing are separate actions.
