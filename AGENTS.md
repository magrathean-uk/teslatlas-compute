# Teslatlas Compute

> **Frozen on 2026-10-08.** This checkout was imported with its full history into the
> Hub repository as `hub/compute/`, which is now the authoritative copy. Do not edit
> here; it stays only until the App re-pins Compute and the old GitHub repositories
> are archived on the owner's push request.

Shared Rust map kernel (raster kernel, raw-position cleaning), compiled into both the
App and the Hub. Follow `../AGENTS.md` and `../docs/development/MASTER_PLAN.md`; the plan
wins on process, model, scope and commit policy. Run commands through
`../scripts/dev/run.sh teslatlas-compute COMMAND...`.

- Both consumers pin this crate by commit (the App by git rev, the Hub by rev today and
  by workspace path after the move). Output-changing fixes bump `ALGORITHM_VERSION`.
- The crate moves into the `magrathean-uk/teslatlas-hub` Cargo workspace as the
  `compute/` package, keeping its Apache-2.0 licence, after an Authorisations row.
- No dependencies, no unsafe, no network code. Legal files are owner-controlled.
- Short local commit per reviewed batch; push only on an owner request.

## Models

Claude Code (Fable, or Opus 5.5, for cross-cutting work and review; Sonnet 5.5 for routine implementation; Haiku 5.5 for exploration) or Codex `gpt-6.1-sol` (`gpt-6-luna` for swarm exploration). Keep changes small; distinguish local checks from device and live-service evidence.

<!-- clean-development-policy:v4 (clean-development 0.6.0; canonical: bolyki-dev/clean-development/docs/policy-snippet.md) -->
## Clean development (mandatory)

- Storage is routed for you: every zsh loads `~/dev/machine/env.zsh`; hooks pin the mode per session (`$CLEAN_DEV_MODE` is `external` when the 4TB is attached, else `internal`). Do not wrap commands, override or unset the routed variables.
- Temp files go to `$TMPDIR` (session scratch, deleted at session end), throwaway build output to `$CLEAN_DEV_BUILDS`, caches wherever their variables point. Never write caches, builds or temp files into `~`, `~/dev` or this repo outside its git-ignored build directories.
- Internal mode: before any download or build likely over 5 GB, stop and ask G (suggest waiting for the 4TB).
- This repo's `.clean-development.json` (schema v2) lists its git-ignored `buildDirs`/`depDirs`; keep it accurate when the build layout changes.
- No dot-folders in `~` or `~/dev`. `.env` files stay git-ignored; read them only when the task needs them; never commit them.
