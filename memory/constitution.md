# Constitution

Non-negotiable principles for this repo. Every spec and plan under `specs/` must comply with these; if a plan would violate one, the plan is wrong, not the constitution.

1. **Local-before-CI** — every capability (AWS CLI, SAM, Terraform, GitHub CLI, Docker-outside-of-Docker, Claude Code) must be verified working in a locally-built container before it's wired into the GitHub Actions build.

2. **Minimal image, deliberately scoped** — this image serves one workflow: writing and testing AWS serverless solutions (Lambdas are primarily Python), including their Terraform-provisioned infra. Tools belong here only if they're core to that workflow, not "might be handy." Anything outside that scope gets its own purpose-specific image.

3. **Baked image vs. postCreate boundary** — system-level environment tooling (interpreter, AWS CLI, SAM CLI, Terraform, GitHub CLI, Docker CLI, Claude Code) is baked into the Dockerfile/devcontainer features, because it's expensive to install, rarely changes, and must be identical for anyone pulling the image. Project-level Python packages (`requirements.txt`) install via `postCreateCommand` instead, so dependency changes never require an image rebuild + push.

4. **No baked-in secrets** — AWS credentials and SSH keys are always host-mounted or forwarded, never copied into an image layer.

5. **Single source of truth** — `devcontainer.json` + `Dockerfile` fully define the environment; no undocumented manual setup steps.

6. **Reproducible from clean clone** — anyone can clone the repo and get a working environment with no undocumented prerequisites beyond Docker Desktop + VS Code.

7. **Feature branches** — spec/plan/tasks/implementation work never happens directly on `main`. This applies whether it's a brand-new `specs/NNN-feature-name/` directory or a substantive revision to a later phase of an *existing* spec (e.g. correcting Phase 2 of `specs/001-aws-serverless-devcontainer/`) — a new branch is created before any spec, plan, or implementation files are written or edited. Branch naming reuses the spec's number prefix plus a short slug for the change (e.g. `001-phase2-remote-build`). Trivial checkbox/status updates to `tasks.md` reflecting already-verified work are exempt.
