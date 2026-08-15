# charly-arch

The Arch Linux package repository for [charly](https://github.com/opencharly/charly) — the OpenCharly CLI and its composed toolchain, packaged as `.pkg.tar.zst` for `amd64` and `arm64`.

The repository is built by a GitHub Actions workflow (manual dispatch with a charly release CalVer) and published to GitHub Pages. Each build produces the `charly` package plus the named variants `charly-full` and `charly-minimal` (differing in the baked-in plugin set), signs the packages and the pacman database, and install-tests the result before deploying.

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
| `charly` | secrets, feature, vm, doctor, clean, settings, candy |
| `charly-full` | the default set + udev, preempt |
| `charly-minimal` | doctor, clean, settings |

## Triggering a build

The workflow is manual: **Actions → build → Run workflow**, entering the charly release CalVer to package (e.g. `2026.227.1026`). The main repo's release is the source of truth for the binary, the plugins, and the packaging metadata.

## Verification

Each build install-tests the packages from a local `file://` mount of the assembled repository before deploying: it initializes the pacman keyring, adds and locally signs the repo key, installs `charly` with `SigLevel = Required`, asserts `charly version` equals the packaged release, and runs `charly doctor` from a non-project directory to prove the baked plugins dispatch project-less.
