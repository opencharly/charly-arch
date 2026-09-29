# charly-arch

The Arch Linux package repository for [charly](https://github.com/opencharly/charly) — the OpenCharly CLI and its composed toolchain, packaged as `.pkg.tar.zst` for `amd64` and `arm64`.

This repo owns the artifact **and the R10 bed that proves it**: the
`check-arch-repo` deploy boots a disposable Arch cloud-image VM, adds the
published `[charly]` pacman repo, installs the packaged `charly`, and asserts the
installed binary's version equals the version the package manager recorded.

> The repository subdirectories use the GOARCH names `amd64`/`arm64` (matching the charly release assets). The `.PKGINFO` `arch` field inside each package is the pacman arch (`x86_64`/`aarch64`), and the package filenames use the pacman arch — only the documented `Server` URL uses the literal `amd64`/`arm64` subdirectory.

## Add the repository

```sh
pacman-key --add https://opencharly.github.io/charly-arch/charly.gpg
pacman-key --lsign-key <KEYID>
```

Append to `/etc/pacman.conf`:

```ini
[charly]
Server = https://opencharly.github.io/charly-arch/amd64
SigLevel = Required
```

Then:

```sh
pacman -Sy
pacman -S charly
```

For `arm64` hosts, use `Server = https://opencharly.github.io/charly-arch/arm64`.

## Direct install

Download the `.pkg.tar.zst` for your architecture and install it with `pacman -U`:

- amd64: `https://opencharly.github.io/charly-arch/amd64/charly-amd64.pkg.tar.zst`
- arm64: `https://opencharly.github.io/charly-arch/arm64/charly-arm64.pkg.tar.zst`

## Variants

| Package | Plugin set |
|---|---|
| `charly` | secrets, feature, vm, doctor, clean, settings, candy, mcp, review, pipeline (10) |
| `charly-full` | secrets, udev, preempt, feature, vm, doctor, clean, settings, candy, mcp, review, pipeline (12) |
| `charly-minimal` | doctor, clean, settings (3) |

## Triggering a build

The build workflow is manual: **Actions → build → Run workflow**, entering the
charly release CalVer to package (e.g. `2026.227.1026`). The main repo's release
is the source of truth for the binary, the plugins, and the packaging metadata.
Each build assembles the repo for both `amd64` and `arm64`, signs the packages
and the pacman database, and install-tests the result before deploying to GitHub
Pages.

## Verification

- **CI install-test** (inside the build workflow): initializes the pacman
  keyring, adds and locally signs the repo key, installs `charly` with
  `SigLevel = Required`, asserts `charly version` equals the packaged release,
  asserts the default-variant `plugin-<word>` set is served by the shared
  `charly-lib` host, and runs `charly doctor` from a non-project directory.
- **R10 bed** `check-arch-repo`: `charly check run check-arch-repo` boots the
  disposable Arch VM, installs the packaged `charly` from the PUBLISHED repo,
  asserts the version match, then removes `charly` and installs the
  `charly-minimal` variant and asserts its plugin set.

## Layout

- `charly.yml` — the `arch-repo-vm` template and the `check-arch-repo` bed.
- `.github/workflows/build.yml` — the manual package build + Pages deploy.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `charly.gpg` — the pacman repo signing key.
- `index.html` — the Pages landing page.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-tools:charly` — the charly binary and its per-distro package repos.
- Arch distro: `/charly-distros:arch` — the Arch base box and vocabulary.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder.
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella.
