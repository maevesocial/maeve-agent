# Directory status

Checked on 12 October 2026.

| Listing | Version and endpoint | Status |
| --- | --- | --- |
| Claude plugin | 1.1.2, `/mcp` | Published; serving commit `9d86275` |
| Claude MCP connector | Four tools, `/mcp` | Submitted under the plugin's existing owner; in review |
| Original OpenAI plugin | 2.0.0, `/mcp` | Retained during replacement review |
| Replacement OpenAI plugin | 3.0.0, `/mcp/openai` | In review; not published |

Both endpoints run stateless Streamable HTTP in production. A fresh Claude connection passed all four tools, including creating and reading back one draft, with access restricted to the selected workspace. Nothing was scheduled or published. The CLI uses REST and does not need an update for this transport change.

Each submission has a separate reviewer account restricted to a populated review workspace. Credentials belong only in private reviewer fields and secure operator storage.

## Follow-up

- OpenAI's latest MCP scan did not complete. Its skill checks passed, authorization is valid and the domain is verified. See the [OpenAI review record](../openai/SUBMISSION.md) for test and demo limitations.
- Claude recommends title annotations on the four tools. Review the write-tool description against its policy attestation before making the next server metadata update; these changes have not been deployed.
- Confirm that Claude pairs the new connector with the existing plugin after approval. The submission uses the same owner and production URL.
- Directory branding is separate from repository ownership. The Claude plugin still identifies its author as `jared-james`; OpenAI shows LILY DIA PTY LTD. Moving the repository to `maevesocial` does not itself update those fields.

The root package supports the four-tool endpoint. The separate `openai/` package supports direct tools. Repository publication, server deployment and directory approval are independent release steps.
