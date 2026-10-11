# OpenAI 3.0.0 submission preparation

Target the replacement listing `plugin_asdk_app_6ac1a331c0248191827893f61e4504e2`. The original 2.0.0 listing stays published during review. The package name already saved in the replacement draft remains `app-6a8ccd4370188191932fe94b18d9b3eb`; it is not the Portal app binding.

## Package

- Version: 3.0.0, using the production `/mcp/openai` endpoint and direct-tool skill.
- Publisher: LILY DIA PTY LTD, confirmed by the owner.
- Listing, support, privacy and terms URLs were inspected on 12 October 2026. The privacy policy covers external assistants and permission-scoped sharing.
- Reuse the existing 512 by 512 Maeve PNG for the listing and composer. No new artwork is required.
- Five positive cases and three negative cases are in `plugin.json`. Do not count them as passed until run against the saved replacement version.
- Subscriptions are purchased through Maeve's website; the plugin does not collect payment details or manage subscriptions.

## Review prerequisites

The owner explicitly selected all supported countries and will resolve the published terms mismatch. The package uses an empty `publication.countries` list to remove country restrictions. The terms inspected on 12 October limit accounts to Australia, New Zealand and the United States; do not edit those terms or attest that the mismatch is resolved on the owner's behalf.

The owner supplied the existing unlisted recording at https://youtu.be/xpMgHQmJu_g (Studio filename `2026-08-25 09-19-35.mp4`, duration about 3 minutes 48 seconds). Playback works and the URL is retained in `review.demo_recording_url`. Sampled scenes show the earlier ChatGPT plugin, scheduled-content reads, a draft request and scheduling in the Apple Review workspace. This is historical workflow evidence, not a recording of version 3.0.0 or proof that its review cases passed. A current-version walkthrough and complete recording review remain outstanding.

Use a dedicated reviewer account with sample data and suitable plan access. It needs one review workspace, sample Media Room images, analytics access and a connected account supporting text-only drafts. Store credentials and login instructions only in secure Portal fields. Do not use a real customer's account or commit credentials here.

The demo must show the actual replacement tools and skill in ChatGPT. Rehearse workspace discovery, scheduled-content reads, media selection, analytics with honest missing-data handling, and draft-only creation followed by readback. Finish with an unsupported billing or account-connection request. Keep credentials and unrelated content off screen. Record the real interaction, verify playback, host it for reviewers and add its verified URL to `review.demo_recording_url` before rebuilding the submission ZIP.

## Verification status

| Check | Status |
| --- | --- |
| Production backend transport and OAuth acceptance | Passed separately; see backend MCP release reference |
| Existing production MCP discovery and workspace reads | Passed |
| Five positive cases against the saved OpenAI 3.0.0 package | Not run |
| Three negative cases against the saved OpenAI 3.0.0 package | Not run |
| Reviewer sign-in and fixture prerequisites | Not verified |
| Demo recording and reviewer-accessible URL | Existing unlisted recording plays; current-version coverage and full review remain outstanding |
| Replacement Portal connection and tool scan | Existing 2.0.1 draft configured and authorized for Personal / Development read/write only; direct tools discovered, scan pending |
| Developer legal/policy attestations | Must be completed by the authorized developer |

Source validation, uploading a draft, submitting for review and publishing an approved version are separate outcomes. An archive with missing review materials is a preparation artifact, not a submission-ready release.
