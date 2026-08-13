# Tasks: AWS Serverless Dev Container

## Phase 0 — Scaffolding
- [x] Rename repo directory to `aws-serverless-devenv`
- [x] `git init`
- [x] Write `memory/constitution.md`
- [x] Write `specs/001-aws-serverless-devcontainer/spec.md`
- [x] Write `specs/001-aws-serverless-devcontainer/plan.md`
- [x] Write `specs/001-aws-serverless-devcontainer/tasks.md` (this file)
- [x] Write `CLAUDE.md`

## Phase 1 — Local build & verification
- [x] Write `.devcontainer/Dockerfile`
- [x] Write `.devcontainer/devcontainer.json`
- [x] Write `requirements.txt`
- [x] Write `.gitignore`
- [x] Write `README.md`
- [x] Open in VS Code, "Dev Containers: Reopen in Container" — confirm clean build (verified via `devcontainer up`/`exec`; found and fixed a build-blocking bug — see plan.md's `moby: false` note)
- [x] Verify `aws --version`
- [x] Verify `sam --version`
- [x] Verify `terraform --version`
- [x] Verify `python --version` is 3.13
- [x] Verify `docker ps` shows host containers (proves socket mount)
- [x] Verify `claude --version`
- [x] Complete `claude` OAuth login (interactive — requires a human in a real VS Code session, not scriptable)
- [x] Fix `~/.aws` mount: switch from whole-directory read-only bind to individual read-only file binds (`config`, `credentials`), with `~/.aws/cli/cache` and `~/.aws/sso/cache` pre-created as writable in the Dockerfile — `aws s3 ls` currently fails with `[Errno 30] Read-only file system` on the cache path
- [x] Verify `aws sts get-caller-identity` succeeds (exercises the cache write path)
- [x] Verify `ssh -T git@github.com` if `ssh-add` was run on host first (optional — not required to complete Phase 1)
- [x] Add `ghcr.io/devcontainers/features/github-cli:1` to `.devcontainer/devcontainer.json`
- [x] Rebuild container, verify `gh --version`
- [x] Authenticate `gh` (`gh auth login`), verify `gh auth status`
- [x] Commit Phase 1 work

## Phase 2 — GitHub automation
- [x] Create `rgfortune/aws-serverless-devenv` GitHub repo (public)
- [x] Push local repo to GitHub
- [x] Write `.github/workflows/build-and-push.yml`: builds remotely on the GitHub-hosted runner from `.devcontainer/Dockerfile` (not a push of the local Phase 1 image), using `docker/setup-qemu-action` + `docker/setup-buildx-action` + `docker/build-push-action` with `platforms: linux/amd64,linux/arm64`, triggered on push to `main` (path-filtered to `.devcontainer/**`) plus `workflow_dispatch` for manual test runs, pushing `:latest` and `:${{ github.sha }}` to `ghcr.io`
- [x] Push to `main`, confirm workflow runs and succeeds (merged via PR #1; run triggered manually via `workflow_dispatch` since the merge only touched `.github/workflows/**`, succeeded in 7m14s)
- [x] Verify `ghcr.io/rgfortune/aws-serverless-devenv:latest` is a multi-platform manifest (`docker manifest inspect` confirms both `linux/amd64` and `linux/arm64` present, plus buildx-attached attestation manifests; anonymously pullable after brief GHCR propagation delay)

## Phase 3 — Polish (only as needed from real use)
- [ ] (left open — see plan.md)
