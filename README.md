# aws-serverless-devenv

A VS Code Dev Container for AWS serverless (Lambda/SAM) development in Python, with Terraform for IaC and Claude Code preinstalled.

See [CLAUDE.md](CLAUDE.md) and [memory/constitution.md](memory/constitution.md) for the design principles and rationale, and [specs/001-aws-serverless-devcontainer/](specs/001-aws-serverless-devcontainer/) for the full spec and phased plan.

## What's inside

- Python 3.13
- AWS CLI v2
- AWS SAM CLI
- Terraform CLI
- Docker CLI, wired to the host's Docker daemon (Docker-outside-of-Docker) — so `sam local invoke` / `sam local start-api` work
- Claude Code

## Prerequisites

- Docker Desktop running on the host
- VS Code with the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
- AWS credentials configured at `~/.aws` on the host (bind-mounted read-only into the container — nothing is baked into the image)
- Optional, for git-over-SSH from inside the container (e.g. `terraform init` on private git-hosted modules, `pip install git+ssh://...`): a running `ssh-agent` on the host with your key loaded (`ssh-add`), started *before* opening the container — VS Code forwards it in automatically, no key material is copied in. Skipping this just means in-container git-over-SSH won't work; nothing else is affected, and interactive `git push`/`pull` can still be run from a host terminal instead, since the workspace is a bind mount.

## Usage

1. Clone this repo and open it in VS Code
2. Command Palette → "Dev Containers: Reopen in Container"
3. Once inside, run `claude` to complete the OAuth login (one-time per container rebuild)

## Notes

- Project Python dependencies live in `requirements.txt` and are installed via `postCreateCommand`, not baked into the image — see `memory/constitution.md` principle 3 for why.
- The published image is built by GitHub Actions on every push to `main` and pushed to `ghcr.io/rgfortune/aws-serverless-devenv`.
