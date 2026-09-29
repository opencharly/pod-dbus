# AGENTS.md — pod-dbus

Standalone candy repo for the `dbus` candy — a supervisord-managed D-Bus session
bus exported at a unix-socket address. The candy lives in `charly.yml` at the
repo root.

Canonical files:

- `charly.yml` — the `dbus:` candy entity (description, `require`, `env`,
  `distro`, `service`, `plan`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-infrastructure:dbus-layer` — the closest family skill: the D-Bus
  session-bus candy properties and the notification delivery chain. **This candy
  has no `skill:` entity of its own** — the gap is recorded on
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- `/charly-check:dbus` — the `dbus:` check verb's full method catalog and YAML
  shape (served by `plugin-dbus`).
- `/charly-check:check` — the check/R10 framework: the `check:` step verbs and
  `charly check run <bed>`.
- `/charly-pod:pod` — the `kind: pod` / deploy schema reference (this candy is
  composed into a box; tree-position nesting, volumes, ports).
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs, per-distro sections, service declarations).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The live R10 witness is a composing box's `check` bed; the candy's own `check:`
  steps assert the `dbus-daemon` binary, the providing package, the exported
  `DBUS_SESSION_BUS_ADDRESS`, and the running `dbus` service.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.

## Modify this repo

- Edit the `dbus:` candy entity in `charly.yml`; there is no `skill:` entity in
  this repo (the gap is tracked on opencharly/opencharly#291).
- The bus address (`unix:path=/tmp/dbus-session`) is the contract shared by the
  `env:` field, the service `exec`, and every consumer; keep them in step.
- The per-distro package names diverge (`dbus-daemon` on Fedora, `dbus`
  elsewhere) — a package change must update the `distro:` sections and the
  package `check:` together.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
