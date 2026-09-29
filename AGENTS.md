# AGENTS.md — charly-arch

This repo owns the **Arch pacman package repository for charly** — the artifact
(the published `.pkg.tar.zst` packages + signed pacman database at
`opencharly.github.io/charly-arch`) and the R10 bed (`check-arch-repo`) that
proves the published repo installs. The `charly` binary itself is built in
`opencharly/charly`; this repo packages a released CalVer.

The candy here carries **no `skill:` entity**, so no per-repo corpus page is
projected; the owning guidance is the family skill `/charly-tools:charly`. The
gap is tracked in `opencharly/opencharly#291`.

Canonical files:

- `charly.yml` — the `arch-repo-vm` `kind: vm` template and the
  `check-arch-repo` disposable bed.
- `.github/workflows/build.yml` — the manual package build, sign, install-test,
  and GitHub Pages deploy.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `charly.gpg` — the pacman repo signing key; `index.html` — the Pages landing page.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-tools:charly` — the owning (family) skill. The charly binary's
  per-distro package repos, the `packaging:` metadata source, and the
  released-vs-in-development binary contract. Load before editing the packaging
  or the bed.
- `/charly-distros:arch` — the Arch base box and distro vocabulary. Load before
  reasoning about the Arch guest's pacman behaviour.
- `/charly-vm:vm` — the `charly vm` command family and the `kind: vm` entity
  model (the `arch-repo-vm` template). Load before editing the VM template.
- `/charly-check:check` — the check-bed reference (`disposable: true` beds,
  deploy-scope check authoring, `charly check run <bed>`). Load before editing
  the bed.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate**.
- The manifest validates with `charly box validate` at the repo root.
- The R10 bed is `charly check run check-arch-repo` — it boots the disposable
  Arch VM and installs the packaged charly from the PUBLISHED repo, then also
  proves the `charly-minimal` variant. The artifact build/sign/install-test
  lives in `.github/workflows/build.yml` (manual dispatch with a release
  CalVer).

## Modify this repo

- Package builds are driven from the `opencharly/charly` release; this repo
  does not build the binary. A packaging-metadata change lands in the charly
  repo's `packaging:` section first.
- Keep the `check-arch-repo` bed honest against the PUBLISHED repo — it is the
  R10 acceptance for the Arch leg. Its VM template pins `backend: libvirt` and
  `ssh.port_auto` so concurrent beds never collide.
- New behaviour claims belong in the bed's `plan:` as an observable `check:`
  step.

## Landing

- The authoritative landing mechanics are `/charly-internals:git-workflow` and
  the umbrella `AGENTS.md` in `opencharly/opencharly`; this signpost does not
  restate them.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time).
