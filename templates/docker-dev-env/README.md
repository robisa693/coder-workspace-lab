---
display_name: Docker Dev Environment
description: Docker-based Coder workspace with VS Code Desktop, Claude Code and Codex CLI
icon: /icon/docker.png
tags: [docker, container, ai]
---

# Docker Dev Environment

Provision Docker containers as [Coder workspaces](https://coder.com/docs/user-guides/workspace-management), preloaded with an IDE and AI coding agents. Based on Coder's official Docker starter template.

## Prerequisites

### Workspace image

The container image determines what tools, languages, and runtimes are available in the workspace out of the box, so it has a major impact on the developer experience.

Some options to consider:

- [`codercom/example-base:ubuntu`](https://github.com/coder/images/tree/main/images/base) (default): minimal and lightweight, but may not include many tools developers expect by default
- [`codercom/example-universal:ubuntu`](https://github.com/coder/images/tree/main/images/universal): catch-all image with many languages and tools available, but larger and slower to pull

More language-specific images (Go, Java, Node.js, and more) are available in [coder/images](https://github.com/coder/images), and the [devcontainers/images](https://github.com/devcontainers/images) collection is another good source of ready-made development images.
You can also build your own image to pre-bake the exact tools your team needs.
Visit [Coder's image management docs](https://coder.com/docs/admin/templates/managing-templates/image-management) for additional guidance.

### Infrastructure

The VM you run Coder on must have a running Docker socket and the `coder` user must be added to the Docker group:

```sh
# Add coder user to Docker group
sudo adduser coder docker

# Restart Coder server
sudo systemctl restart coder

# Test Docker
sudo -u coder docker ps
```

## Architecture

This template provisions the following resources:

- Docker container running `codercom/example-base:ubuntu` (ephemeral)
- Docker volume (persistent on `/home/coder`)
- Coder agent with dashboard metadata (CPU, RAM, disk, host load, swap)

This means, when the workspace restarts, any tools or files outside of the home directory are not persisted. To pre-bake tools into the workspace (e.g. `python3`), modify the container image. Alternatively, individual developers can [personalize](https://coder.com/docs/user-guides/workspace-dotfiles) their workspaces with dotfiles.

> **Note**
> This template is designed to be a starting point! Edit the Terraform to extend the template to support your use case.

### Modules

Defined in [`modules.tf`](modules.tf), pulled from the [Coder Registry](https://registry.coder.com):

| Module | Purpose |
| --- | --- |
| [`vscode-desktop`](https://registry.coder.com/modules/coder/vscode-desktop) | One-click "Open in VS Code Desktop" button |
| [`claude-code`](https://registry.coder.com/modules/coder/claude-code) | Installs [Claude Code](https://claude.com/claude-code) in the workspace |
| [`codex`](https://registry.coder.com/modules/coder-labs/codex) | Installs OpenAI's Codex CLI in the workspace |

### Template variables

| Variable | Description |
| --- | --- |
| `docker_socket` | Optional Docker socket URI (defaults to the local socket) |
| `claude_code_oauth_token` | Sensitive. Token for Claude Code, generate with `claude setup-token` |
| `openai_api_key` | Sensitive. API key for Codex CLI |

Secrets are never committed. Pass them when pushing the template:

```sh
coder templates push docker-dev-env \
  --variable claude_code_oauth_token="$CLAUDE_CODE_OAUTH_TOKEN" \
  --variable openai_api_key="$OPENAI_API_KEY"
```

### Changing the image

Edit the `image` attribute of `docker_container.workspace` in [`main.tf`](main.tf) and run `coder templates push` to update workspaces.
