# The plugin registers the CLI's MCP server rather than shipping one

Every MCP tool this plugin advertises — scanning text, scanning a project,
redacting PII, auth, status — is implemented in the PromptGuard CLI. What lives
here is a few lines naming the command a host should run
(`promptguard mcp -t stdio`). This repo contains no server, makes no HTTP
request, and has no build step or runtime.

The alternative was a small MCP server in this repo, which would have removed
the plugin's dependency on a separate binary being installed and on `PATH`.
That dependency is a genuine cost: the plugin is inert until the CLI is
installed, and the failure mode — tools silently absent from the host — is one
the plugin cannot report on. It was still the wrong trade. A server here would
be a second implementation of scanning, credential resolution and proxy
handling, in a repo whose entire premise is that it holds no executable code,
and it would drift from the CLI exactly the way every duplicated surface in
this project has drifted before.

## Consequences

"Install the plugin" is two steps, not one, and the README's prerequisites
section is load-bearing rather than boilerplate.

The MCP tool table in the README is a copy of what the CLI exposes, and nothing
checks it. When the CLI adds, removes or renames a tool, that table is wrong
until a person notices — the same class of unchecked-prose risk that the API
reference carries, and the reason this repo's rule is that concrete values are
verified against their source at the time they are written.
