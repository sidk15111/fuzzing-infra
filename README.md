# fuzzing-infra

Everything downstream of a merge to `fuzzing-targets`: validation logic,
build-bot orchestration, and the GitHub Actions that call into them.

Deliberately kept separate from `fuzzing-targets`: that repo is open to
any team member (or outside contributor) submitting a fuzz target, and
review there is scoped to "does this harness/Dockerfile make sense." This
repo controls what actually executes on the build bot, so it has a
narrower merge gate (see `CODEOWNERS`) — read access can and should stay
open to the whole team regardless, so this isn't a single-person
bottleneck if whoever's maintaining it moves on.

## What's here

- `scripts/validate_submission.py` — structural + `project.yaml` schema
  validation, called from `fuzzing-targets`' PR workflow. Never executes
  anything a submitter wrote.
- (next) build-bot orchestration: waking a build bot, running the actual
  `docker build` / `build_fuzzers` / `check_build` for a submitted project,
  uploading on success.
- (next) the scheduled full-fleet rebuild.

## Setup still needed (not code — GitHub/GCP config)

- A repo secret in `fuzzing-targets` (`FUZZING_INFRA_READ_TOKEN`) — a
  fine-grained PAT or GitHub App token with read-only access to this repo,
  so `fuzzing-targets`' workflow can check out `scripts/validate_submission.py`.
- `CODEOWNERS` + branch protection here, naming whoever should be required
  to review changes to this repo.
- Whatever gate decides a submission's `Dockerfile`/`build.sh` is safe to
  actually execute (GitHub's first-time-contributor approval, or an
  explicit maintainer trigger) — still an open design decision, not yet
  built.
