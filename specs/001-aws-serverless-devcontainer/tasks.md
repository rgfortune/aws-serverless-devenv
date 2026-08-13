# Tasks: AWS Serverless Dev Container

## Phase 0 — Scaffolding
- [x] Rename repo directory to `aws-serverless-devenv`
- [x] `git init`
- [x] Write `memory/constitution.md`
- [x] Write `specs/001-aws-serverless-devcontainer/spec.md`
- [x] Write `specs/001-aws-serverless-devcontainer/plan.md`
- [x] Write `specs/001-aws-serverless-devcontainer/tasks.md` (this file)
- [ ] Write `CLAUDE.md`

## Phase 1 — Local build & verification
- [ ] Write `.devcontainer/Dockerfile`
- [ ] Write `.devcontainer/devcontainer.json`
- [ ] Write `requirements.txt`
- [ ] Write `.gitignore`
- [ ] Write `README.md`
- [ ] Open in VS Code, "Dev Containers: Reopen in Container" — confirm clean build
- [ ] Verify `aws --version`
- [ ] Verify `sam --version`
- [ ] Verify `terraform --version`
- [ ] Verify `python --version` is 3.13
- [ ] Verify `docker ps` shows host containers (proves socket mount)
- [ ] Verify `claude --version` and complete `claude` OAuth login
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
