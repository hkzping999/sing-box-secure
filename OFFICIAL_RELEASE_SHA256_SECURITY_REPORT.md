# Official Releases + SHA256 Security Refactor

## Goal

This version upgrades the installer to a strict supply-chain policy:

1. Download executable binaries only from official GitHub Releases.
2. Pin binary versions.
3. Verify SHA256 before using the binary.
4. Refuse installation when a SHA256 value is missing or verification fails.
5. Remove third-party GitHub acceleration/proxy control from binary downloads.

## Pinned versions

- sing-box: `1.13.13`
- cloudflared: `2026.5.2`
- jq: `1.7.1`

## Implemented changes

### New secure download layer

Added:

- `get_embedded_sha256()`
- `get_github_release_digest()`
- `secure_download_with_sha256()`
- `secure_download_release_asset()`

### jq

Uses official GitHub Releases:

- `https://github.com/jqlang/jq/releases/download/jq-1.7.1/jq-linux-$JQ_ARCH`

The SHA256 values are embedded for:

- `jq-linux-amd64`
- `jq-linux-arm64`
- `jq-linux-armhf`

### cloudflared

Uses official GitHub Releases:

- `https://github.com/cloudflare/cloudflared/releases/download/2026.5.2/cloudflared-linux-$ARGO_ARCH`

The SHA256 values are embedded for:

- `cloudflared-linux-amd64`
- `cloudflared-linux-arm64`
- `cloudflared-linux-arm`
- `cloudflared-linux-armhf`

The previous `releases/latest` behavior was removed.

### sing-box

Uses official GitHub Releases:

- `https://github.com/SagerNet/sing-box/releases/download/v1.13.13/sing-box-1.13.13-linux-$SING_BOX_ARCH.tar.gz`

The installer refuses to continue unless it can obtain a SHA256 digest for the exact release asset from the official GitHub release API. This is strict verification against the release asset digest and does not allow unverified installs.

### Version control hardening

Removed runtime control from:

- `force_version`
- `api.github.com/repos/SagerNet/sing-box/releases` latest selection
- third-party GitHub proxy fallback

`get_sing_box_version()` now returns only the pinned version.

### Install-time fail closed behavior

After background downloads complete, the installer checks that the verified binaries exist and are executable:

- `$TEMP_DIR/sing-box`
- `$TEMP_DIR/jq`
- `$TEMP_DIR/cloudflared` when Argo is enabled

If a required binary is missing, installation stops.

## Notes

- OpenAI probing remains enabled as requested.
- Existing local template and local qrencode-wrapper behavior is preserved.
- No root runtime installation test was performed in this environment; static syntax validation was performed with `bash -n`.
