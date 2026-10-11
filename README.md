# Coder Workspace Lab

My playground for experimenting with [Coder](https://coder.com): self-hosted, Terraform-defined development environments.

The goal is to explore how far reproducible cloud dev environments can go: one-click workspaces with the right tools, IDE integration and AI coding agents already set up.

## Templates

| Template | Description |
| --- | --- |
| [`docker-dev-env`](templates/docker-dev-env) | Docker-based workspace with a persistent home volume, VS Code Desktop, Claude Code and Codex CLI |

## Getting started

Prerequisites: a running Coder deployment, the [`coder` CLI](https://coder.com/docs/install) logged in, and Docker available to the Coder server.

```sh
git clone https://github.com/robisa693/coder-workspace-lab.git
cd coder-workspace-lab/templates/docker-dev-env

# Push the template to your Coder deployment
coder templates push docker-dev-env

# Create a workspace from it
coder create my-workspace --template docker-dev-env
```

See each template's README for its variables and details.

## Roadmap

Things I want to try next:

- [ ] Custom workspace image with my own toolchain pre-installed
- [ ] Dotfiles integration
- [ ] Rich parameters (CPU/memory, image choice) at workspace creation
- [ ] Kubernetes-based template
- [ ] Dev Containers (`devcontainer.json`) support
