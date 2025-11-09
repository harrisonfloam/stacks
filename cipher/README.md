# Cipher MCP Server

A containerized MCP server for [Cipher](https://github.com/campfirein/cipher), a self-hosted, open-source AI memory layer for coding agents; specialized for use with GitHub Copilot in Visual Studio Code.

## Customizations

VS Code will automatically manage the lifecycle of the server, but initial Docker image builds require an internet connection. Estimated OpenAI API costs with moderate usage (~300 interactions/month) are $2-4.

- **LLM**: Uses `gpt-4o-mini`, served by OpenAI. Generate your own OpenAI [API Key](https://platform.openai.com/settings/organization/api-keys) and configure [Usage Limits](https://platform.openai.com/settings/organization/limits). Configure other LLM providers by following the [Cipher documentation](https://docs.byterover.dev/cipher/configuration).
- **Storage**: Uses `ChromaDB`, running in a Docker container and persisted via Docker named volume.
- **Embedding**: Uses `text-embedding-3-small`, served by OpenAI.
- **Build**: Orchestrated by Docker Compose using the official [chromadb/chroma](https://hub.docker.com/r/chromadb/chroma) and [campfirein/cipher](https://github.com/campfirein/cipher/pkgs/container/cipher) images.
- **System Prompt**: Provide your own custom instructions in [cipher.yml](cipher.yml) as needed.
- **More configuration**: Controlled by [cipher.yml](cipher.yml) and environment variables. See the [Cipher documentation](https://docs.byterover.dev/cipher/configuration).

## Setup

Clone the repository from GitHub and open it in VS Code.

```bash
git clone https://github.com/harrisonfloam/stacks
```

Then add the following entries to your [VS Code MCP config](command:workbench.mcp.openUserMcpJson), then ask GitHub Copilot to use Cipher.

>Hotkey: <kbd>Cmd / Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd> + <kbd>*MCP: Open User Configuration*</kbd>

If you aren't prompted to configure your OpenAI API Key and repository location, you can [manually start the server](command:workbench.mcp.listServer).

>Hotkey: <kbd>Cmd / Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd> + <kbd>*MCP: List Servers*</kbd> + <kbd>*cipher-mcp*</kbd> + <kbd>*Start Server*</kbd>

```json
{
  "servers": {
    "cipher-mcp": {
      "type": "stdio",
      "command": "docker",
      "args": [
        "compose",
        "-f",
        "${input:cipher_stack_path}/docker-compose.yml",
        "run",
        "--rm",
        "cipher"
      ],
      "env": {
        "OPENAI_API_KEY": "${input:openai_api_key}"
      }
    }
  },
  "inputs": [
    {
        "id": "openai_api_key",
        "type": "promptString",
        "description": "OpenAI API Key",
        "password": true
    },
    {
        "id": "cipher_stack_path",
        "type": "promptString",
        "description": "Path to cipher stack directory (where docker-compose.yml lives)",
        "default": "~/dev/stacks/cipher"
    },
  ]
}
```
