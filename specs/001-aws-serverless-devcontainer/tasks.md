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
- [ ] Complete `claude` OAuth login (interactive — requires a human in a real VS Code session, not scriptable)
- [ ] Verify `ssh -T git@github.com` if `ssh-add` was run on host first (optional — not required to complete Phase 1)
- [ ] Commit Phase 1 work

## Phase 2 — GitHub automation
- [ ] Create `rgfortune/aws-serverless-devenv` GitHub repo
- [ ] Push local repo to GitHub
- [ ] Write `.github/workflows/build-and-push.yml`
- [ ] Push to `main`, confirm workflow runs and succeeds
- [ ] Verify `ghcr.io/rgfortune/aws-serverless-devenv:latest` exists and matches Phase 1 local build

## Phase 3 — Polish (only as needed from real use)
- [ ] (left open — see plan.md)
