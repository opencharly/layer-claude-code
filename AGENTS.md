# AGENTS.md — layer-claude-code

Standalone candy repo for the `claude-code` layer — the Claude Code CLI installed
via the official native installer and relocated to `/usr/local/bin/claude`. The
candy lives in `charly.yml` at the repo root: the `download:`/install `plan:`
steps, the `DISABLE_AUTOUPDATER` environment, the CUE-validated tmux terminal
profile, the `check:` assertions, and the embedded `skill:` entity projected into
the marketplace corpus as `/charly-coder:claude-code`.

Canonical files:

- `charly.yml` — the `claude-code:` candy entity and the `claude-code-skill:`
  skill entity.
- `CHANGELOG/` — per-CalVer release history; read it before changing baked
  checks.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-coder:claude-code` — the owning skill. The native-install method, the
  relocation to a system path, and verification. Load before editing or
  troubleshooting the layer.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:` and `download:`, per-distro `distro:` arms,
  package/repo sections, and service declarations). Load before editing any
  entity field or plan step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The candy's `plan:` `check:` steps are the functional evidence; they must stay
  valid on every distro arm they run on. Scope a distro-specific check in the
  command itself — the check runner does not honour runner-level
  `exclude-distro` fields.

## Modify this repo

- Edit the `claude-code:` candy entity AND the `claude-code-skill:` skill entity
  in `charly.yml` together. The skill is the projected usage source, so a
  behaviour change not mirrored in the skill leaves the corpus stale.
- Keep the install staging OUT of `/tmp` (it stages into `${HOME}` as
  `${USER}`) and keep the relocation to `/usr/local/bin/claude` — both are
  required for the image-owned install to survive the deploy-time home-volume
  mount.
- New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill body.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
