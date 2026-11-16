# Configure MCP Integrations

[Model Context Protocol (MCP)](https://modelcontextprotocol.io/) allows AI coding agents to access additional tools, services, and contextual information through MCP servers.

MCP integrations are **optional** and should only be introduced when they provide a clear benefit. Some integrations require changes to individual projects, while others connect directly to external services.

## Security Considerations

MCP servers extend the agent's capabilities and should be treated as potentially untrusted components.

- Enable only the integrations needed for the current task or project.
- Prefer project-specific configurations over globally available integrations.
- Review the permissions and capabilities exposed by each MCP server.
- Keep authentication credentials outside synchronized project directories.
- Avoid exposing production systems, sensitive data, or unnecessary administrative capabilities.
- Remove unused integrations and revoke credentials when no longer needed.

> [!WARNING]
> MCP servers may execute commands, access local files, communicate with external services, or modify data. Running an agent inside a VM does not eliminate these risks, particularly when MCP integrations provide access to resources outside the VM.

## Configuration

Consult the documentation of the chosen MCP server and the AI coding agent for installation and configuration instructions.

Verify the integration's permissions and behavior in a non-sensitive environment before using it with real projects.
