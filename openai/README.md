# OpenAI plugin source

`app-6a8ccd4370188191932fe94b18d9b3eb/` contains version 3.0.0 for the [replacement OpenAI listing](https://platform.openai.com/plugins/manage/plugin_asdk_app_6ac1a331c0248191827893f61e4504e2), submitted and in review as of 12 October 2026. It connects to `https://api.maevesocial.com/mcp/openai`. The retained package name is independent of the Portal app ID; do not overwrite generated bindings or create another listing to change the version.

The repository root remains the package for the existing four-tool `/mcp` connection. Its MCP configurations and shared skill are unchanged by the OpenAI endpoint work. OpenAI has its own skill because its tools take direct arguments and do not use catalog discovery or generic execution.

The OpenAI endpoint registers the existing MCP catalog operations as individual tools using their maintained wire schemas, descriptions and annotations. Calls use the existing dispatcher, workspace permissions, availability checks, confirmations and audit flow. No operation implementation is duplicated.

Both endpoints use stateless Streamable HTTP. The OpenAI endpoint has its own OAuth resource and protected-resource metadata at `/.well-known/oauth-protected-resource/mcp/openai`. Tokens cannot be reused between endpoints; there are no retained transport sessions. Existing `/mcp` clients retain their current resource and four tools.

The backend is deployed to production. The [original published plugin](https://platform.openai.com/plugins/manage/plugin_asdk_app_6a8ccd4370188191932fe94b18d9b3eb) remains on `/mcp` during review. Test against the replacement's resource and exact uploaded version. Its latest MCP scan did not complete; approval and publication remain pending. Do not submit the repository-root package for this OpenAI configuration.

The package includes five positive and three negative review cases. See the [review record](SUBMISSION.md) for verification and follow-up. Reviewer credentials stay in private Portal fields, never this repository or a ZIP.
