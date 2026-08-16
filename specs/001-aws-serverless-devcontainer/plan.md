# Plan: AWS Serverless Dev Container

## Phase 0 — Scaffolding (current)

Rename repo, `git init`, create `CLAUDE.md`, `memory/constitution.md`, `specs/001-aws-serverless-devcontainer/{spec,plan,tasks}.md`.

## Phase 1 — Local build & verification

Write `.devcontainer/Dockerfile`, `.devcontainer/devcontainer.json`, `requirements.txt`, `.gitignore`, `README.md`. Build and open in VS Code locally ("Reopen in Container"). Verify every capability in spec.md's Phase 1 acceptance criteria. No GitHub Actions yet — this phase is done only when the container works end-to-end on the actual machine, not just "looks right."

### Technical decisions

- Base image: `mcr.microsoft.com/devcontainers/python:3.13`, default `vscode` user (no custom UID/UNIXACCOUNT — see constitution + rationale below)
- `aws-sam-cli` installed via pip in the Dockerfile (system tool)
- `boto3`, `pytest`, `ruff` in `requirements.txt`, installed via `postCreateCommand` (project-level, not baked — constitution principle 3)
- Docker-outside-of-Docker via the `ghcr.io/devcontainers/features/docker-outside-of-docker:1` devcontainer feature, with `"moby": false` — the feature's default `moby` packages are not published for Debian "trixie" (the base image's OS), so the build fails without this override; `moby: false` installs the `docker-ce`-based CLI instead, which is supported
- AWS CLI v2 via the `ghcr.io/devcontainers/features/aws-cli:1` devcontainer feature
- Terraform CLI via the `ghcr.io/devcontainers/features/terraform:1` devcontainer feature
- `~/.aws/config` and `~/.aws/credentials` bind-mounted read-only, individually, at the matching paths under `/home/vscode/.aws` — not the whole directory. AWS CLI v2 writes SSO/STS token caches to `~/.aws/cli/cache` and `~/.aws/sso/cache`; a read-only mount of the entire directory blocks those writes (`[Errno 30] Read-only file system`) and breaks every authenticated command even though `aws --version` still passes. The Dockerfile pre-creates `~/.aws/cli/cache` and `~/.aws/sso/cache` as normal writable directories owned by `vscode`, so only the two credential-bearing files are read-only host state; the cache dirs are container-local and reset on rebuild.
- git SSH auth via host `ssh-agent` forwarding (VS Code does this automatically; requires `ssh-add` on the host — no explicit mount). This is optional: `ssh-add` must run *before* the container is opened, which VS Code can't enforce, so a failed `ssh -T git@github.com` check is an expected, non-blocking outcome, not a Phase 1 failure.
- Claude Code via the native installer (`curl -fsSL https://claude.ai/install.sh | bash`)
- GitHub CLI (`gh`) via the `ghcr.io/devcontainers/features/github-cli:1` devcontainer feature — to drive repo/PR/CI administration for this project.

### Why `vscode` user instead of a custom UID/UNIXACCOUNT

On Docker Desktop for Mac specifically, the file-sharing layer (VirtioFS/gRPC-FUSE) reconciles ownership between host and container transparently, so the UID-matching problem doesn't arise here. Using the default `vscode` user means less Dockerfile maintenance and better compatibility with devcontainer features, which assume that default.

### Why Docker-outside-of-Docker

`sam local invoke` / `sam local start-api` shell out to `docker run` to emulate the real Lambda runtime — they need Docker access from inside the dev container. Docker-outside-of-Docker installs only the Docker CLI in the image and bind-mounts the host's Docker socket, so commands run inside the container are executed by Docker Desktop on the Mac (lighter than nesting a full Docker daemon inside the container). The devcontainer feature also handles matching the container's `docker` group GID to the socket's owning group, so the non-root `vscode` user can actually run `docker`/`sam local` without a permission error.

## Phase 2 — GitHub automation

Create the `rgfortune/aws-serverless-devenv` GitHub repo and adds `.github/workflows/build-and-push.yml`: triggers on push to `main` (path-filtered to `.devcontainer/Dockerfile` only — the Dockerfile has no `COPY`/`ADD`, so it's the only file whose contents reach the built image; `devcontainer.json`/`devcontainer-lock.json` only configure VS Code/the devcontainer CLI and don't affect the build, so changes to them shouldn't trigger one), logs into `ghcr.io` with `GITHUB_TOKEN`, and builds `.devcontainer/Dockerfile`. Pushes `:latest` and `:${{ github.sha }}`.

The build targets **both `linux/amd64` and `linux/arm64`** in a single multi-platform manifest, using `docker/setup-qemu-action`, `docker/setup-buildx-action`, and `docker/build-push-action` with `platforms: linux/amd64,linux/arm64` — QEMU emulation covers the arm64 leg on GitHub's amd64 runners, since this is a low-frequency build (push-to-main only) where emulation overhead doesn't matter. This lets `docker pull ghcr.io/rgfortune/aws-serverless-devenv` run natively on both an Intel Windows laptop and a Mac (Apple Silicon or Intel) without either machine needing to build the image itself.

## Phase 3 — Polish (optional, only if it surfaces from real use)

README refinement, build-cache tuning for faster CI, anything else that comes up from actually using the environment. Deliberately left unspecified rather than pre-planned, per constitution principle 2 (minimal, deliberately scoped).
