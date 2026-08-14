# Spec: AWS Serverless Dev Container

## Problem

Development against AWS serverless currently has no reproducible environment — tooling gets installed ad hoc on the host or accumulates unmanaged in a long-lived Docker image. Need a VS Code-native, reproducible dev environment scoped to this one workflow.

## Goals

- Python 3.13 dev container, built and used via VS Code Dev Containers
- AWS CLI v2 available and configured from host credentials
- SAM CLI with working `sam local invoke` / `sam local start-api` (requires Docker access from inside the container)
- Terraform CLI available (core to this project's IaC workflow, used alongside SAM)
- Claude Code available and authenticatable inside the container
- AWS credentials via read-only host-mounted files (`~/.aws/config`, `~/.aws/credentials`) — never baked into the image; AWS CLI's own cache directories (`~/.aws/cli/cache`, `~/.aws/sso/cache`) stay container-local and writable, since SSO/STS token caching needs to write there
- git SSH access via forwarded host `ssh-agent` — no private key material mounted or copied in
- GitHub CLI (`gh`) available and authenticatable inside the container, for repo/PR/CI administration alongside the dev workflow
- Image published to `ghcr.io/rgfortune/aws-serverless-devenv`, built remotely and automatically by GitHub Actions on push to `main`. The published image is multi-platform (`linux/amd64` + `linux/arm64`) so it runs natively via `docker pull` on both Apple Silicon/Intel Macs and Intel Windows machines

## Acceptance criteria

Tied to the phase checkpoints in `plan.md`:

- **Phase 1 done when**: container builds locally via VS Code "Reopen in Container"; `aws --version`, `sam --version`, `terraform --version`, `python --version` (3.13), `docker ps` (host containers visible, proving the socket mount), `claude --version`, `claude` OAuth login, `gh --version`, and `gh auth status` all succeed. Additionally, `aws sts get-caller-identity` must succeed — this exercises the AWS CLI's credential/token cache write path, which `aws --version` does not.
- **Optional, non-blocking**: `ssh -T git@github.com` succeeds if `ssh-add` was run on the host before opening the container (proving agent forwarding). A failure here is expected and does not block Phase 1 completion — it just means the host agent wasn't started first.
- **Phase 2 done when**: a push to `main` triggers the GitHub Actions workflow, which builds the image fresh on GitHub's runners for both `linux/amd64` and `linux/arm64`, and produces a working multi-platform manifest at `ghcr.io/rgfortune/aws-serverless-devenv:latest` that behaves identically to the Phase 1 local build on each platform.
