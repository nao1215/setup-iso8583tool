# setup-iso8583tool

[![GitHub Marketplace](https://img.shields.io/badge/GitHub%20Marketplace-setup--iso8583tool-blue?logo=github)](https://github.com/marketplace/actions/setup-iso8583tool)
[![Test](https://github.com/nao1215/setup-iso8583tool/actions/workflows/test.yml/badge.svg)](https://github.com/nao1215/setup-iso8583tool/actions/workflows/test.yml)

GitHub Action to install the [iso8583tool](https://github.com/nao1215/iso8583tool) CLI.

It downloads a prebuilt release binary instead of building from source, so your
workflows stay fast - no Go setup, no `go build`. Works on Linux, macOS, and
Windows (amd64 / arm64).

## Quick start

```yaml
- uses: nao1215/setup-iso8583tool@v0
- run: iso8583tool sample
```

That's it - `iso8583tool` is now on `PATH`.

## Pin a version

```yaml
- uses: nao1215/setup-iso8583tool@v0
  with:
    version: v0.8.0 # default: latest
```

For reproducible CI, pin an exact version instead of `latest`, so a new
iso8583tool release cannot change what your workflow installs.

## Inputs

| Name                 | Default               | Description                                                        |
| -------------------- | --------------------- | ------------------------------------------------------------------ |
| `version`            | `latest`              | Release to install: `latest`, `v0.8.0`, or `0.8.0`.                |
| `github-token`       | `${{ github.token }}` | Token for API requests / downloads (avoids rate limiting).         |
| `install-dir`        | `$HOME/.iso8583tool/bin` | Where to install the binary. Added to `PATH`.                   |
| `verify-checksum`    | `true`                | Verify the archive against `checksums.txt` (SHA-256).              |
| `verify-attestation` | `false`               | Verify build provenance with `gh attestation verify` (opt-in).     |
| `add-to-path`        | `true`                | Add the install directory to `PATH`. Set `false` for outputs only. |

## Outputs

| Name          | Description                                        |
| ------------- | -------------------------------------------------- |
| `version`     | Installed version (e.g. `v0.8.0`).                 |
| `bin-path`    | Absolute path to the `iso8583tool` binary.         |
| `install-dir` | Directory the binary was installed into.           |

## Verification behavior

The action fails (rather than silently continuing) when a verification you
enabled cannot be completed:

- `verify-checksum: true` (default): the install fails if `checksums.txt`
  cannot be downloaded, if the archive is missing from it, or if the SHA-256
  does not match. Set `verify-checksum: false` to skip the check entirely.
- `verify-attestation: true`: the install fails if the `gh` CLI is unavailable,
  if neither `GH_TOKEN` nor `GITHUB_TOKEN` is set, or if
  `gh attestation verify` fails. It is opt-in and skipped by default.

The action derives release asset names
(`iso8583tool_<version>_<os>_<arch>.<ext>`) from iso8583tool's release assets.
If iso8583tool's release naming changes, this action must be updated to match.

## Maintainer release flow

When you want to cut a new Marketplace release:

1. Run the `PrepareRelease` workflow from the Actions tab with `vX.Y.Z` (or `X.Y.Z`).
2. Open the draft GitHub release it creates, tick `Publish this Action to the GitHub Marketplace`, then publish it.
3. After publish, `SyncReleaseTags` automatically moves the floating `vX` and `vX.Y` tags (for example `v0` and `v0.1`) to that release.

That keeps the Marketplace listing current while preserving the immutable full
release tag (`v0.8.0`, `v0.8.1`, ...).

## License

[MIT](./LICENSE) © CHIKAMATSU Naohiro
