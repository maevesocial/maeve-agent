# Directory status

Checked on 12 October 2026. All four rows were checked again on 25 November 2026.

| Listing | Version and endpoint | Status |
| --- | --- | --- |
| Claude plugin | 1.1.3, `/mcp` | Published 28 October 2026; serving commit `18db3de`; newer commits refused by directory policy until 25 November, when the findings were fixed; awaiting the next scheduled check |
| Claude MCP connector | Four tools, `/mcp` | Published and live |
| Original OpenAI plugin | 2.0.0, `/mcp` | Unpublished on 25 November 2026. Version remains approved. |
| Replacement OpenAI plugin | 3.0.1, `/mcp/openai` | Published and listed publicly on 25 November 2026 |

Both endpoints run stateless Streamable HTTP in production. A fresh Claude connection passed all four tools, including creating and reading back one draft, with access restricted to the selected workspace. Nothing was scheduled or published. The CLI uses REST and does not need an update for this transport change.

Each submission has a separate reviewer account restricted to a populated review workspace. Credentials belong only in private reviewer fields and secure operator storage.

## Follow-up

- The replacement’s MCP tab shows Needs attention. Its tool table labels the three Instagram competitor tools Published. See the [OpenAI review record](../openai/SUBMISSION.md).
- Claude recommends title annotations on the four tools. Review the write-tool description against its policy attestation before making the next server metadata update; these changes have not been deployed.
- The directory's automatic policy check has refused every commit since 7 November 2026 (`76de464`, `68e4351`, `5c5c1d9`), so no skill change since 28 October reaches directory installs. It also flagged the live version on 4 November, which keeps serving. The blocking findings were files outside the allowed list and using a credential from the user's machine. On 25 November the root `AGENTS.md` became `CONTRIBUTING.md`, `CLAUDE.md` and `examples/codex-mcp-config.toml` were removed, and the README and skill stopped naming the API key environment variable. The directory checks the repository about every 6 hours; confirm the new commit in the submission's Versions tab. If the files finding remains, validate a new submission from the same repository to see which file it names; the remaining candidates are the other clients' manifest folders, which would mean submitting a subfolder that holds only the Claude plugin.
- Verified status for the organization was requested on 25 November 2026 (case `CASE-E9A1CB8E`). `maevesocial.com` is proven by a DNS TXT record, which must stay in place. If it is granted, `AGENTS.md` and `CLAUDE.md` can return.
- The Claude connector is published. Confirm that Claude pairs it with the existing plugin; the same owner and production URL do not prove pairing.
- Directory branding is separate from repository ownership. The Claude plugin still identifies its author as `jared-james`; OpenAI shows LILY DIA PTY LTD. Moving the repository to `maevesocial` does not itself update those fields.

The root package supports the four-tool endpoint. The separate `openai/` package supports direct tools. Repository publication, server deployment and directory approval are independent release steps.
