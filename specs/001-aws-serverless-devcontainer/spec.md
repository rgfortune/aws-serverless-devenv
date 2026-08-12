# Spec: AWS Serverless Dev Container

## Problem

Development against AWS serverless (Lambda/SAM) in Python currently has no reproducible environment — tooling gets installed ad hoc on the host or accumulates unmanaged in a long-lived Docker image. Need a VS Code-native, reproducible dev environment scoped to this one workflow.

## Goals

- Python 3.13 dev container, built and used via VS Code Dev Containers
- AWS CLI v2 available and configured from host credentials
- SAM CLI with working `sam local invoke` / `sam local start-api` (requires Docker access from inside the container)
- Terraform CLI available (core to this project's IaC workflow, used alongside SAM)
- Claude Code available and authenticatable inside the container
- AWS credentials via read-only host mount (`~/.aws`) — never baked into the image
- git SSH access via forwarded host `ssh-agent` — no private key material mounted or copied in
- Image published to `ghcr.io/rgfortune/aws-serverless-devenv`, built automatically by GitHub Actions on push to `main`

## Non-goals

- Node.js / JavaScript support
- AWS CDK (covered by the separate `aws-cdk-tools` repo)
- Multi-language / general-purpose image
- Standalone `docker-compose` usage outside VS Code Dev Containers
- Semantic-release / CHANGELOG.md ceremony — this is a simple build-on-push image, not a versioned library

## Acceptance criteria

Tied to the phase checkpoints in `plan.md`:

- **Phase 1 done when**: container builds locally via VS Code "Reopen in Container"; `aws --version`, `sam --version`, `terraform --version`, `python --version` (3.13), `docker ps` (host containers visible, proving the socket mount), `claude --version`, `claude` OAuth login, and `ssh -T git@github.com` (proving agent forwarding) all succeed.
- **Phase 2 done when**: a push to `main` triggers the GitHub Actions workflow and produces a working image at `ghcr.io/rgfortune/aws-serverless-devenv:latest` that behaves identically to the Phase 1 local build.
