# aws-serverless-devenv

A VS Code Dev Container for AWS serverless (Lambda/SAM) development in Python, with Terraform for IaC and Claude Code available inside the container.

Read [memory/constitution.md](memory/constitution.md) first — it governs every decision in this repo and takes precedence over convenience.

Active spec: [specs/001-aws-serverless-devcontainer/](specs/001-aws-serverless-devcontainer/) (`spec.md` for what/why, `plan.md` for the phased how, `tasks.md` for the current checklist).

**Current phase: Phase 1 — writing the devcontainer files, then local build & verification.** Phase 2 (GitHub Actions build/publish to `ghcr.io`) doesn't start until Phase 1's acceptance criteria in `spec.md` are all met locally.
