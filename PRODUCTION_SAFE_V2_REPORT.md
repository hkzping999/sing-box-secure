# sing-box production safe v2 changes

This package is based on the uploaded script and applies production-focused hardening.

## Main changes

1. Pinned runtime binary versions:
   - sing-box: 1.13.13
   - cloudflared: 2026.5.2
   - jq: 1.8.1

2. Official release downloads only:
   - SagerNet/sing-box official GitHub Releases
   - cloudflare/cloudflared official GitHub Releases
   - jqlang/jq official GitHub Releases

3. Embedded SHA256 verification:
   - sing-box linux amd64 / arm64 / armv7, including glibc and musl variants
   - cloudflared linux amd64 / arm64 / arm / armhf
   - jq linux amd64 / arm64 / armhf
   - installation aborts if a digest is missing or does not match

4. Removed dynamic supply-chain decisions:
   - no releases/latest for cloudflared
   - no force_version fetch
   - no GitHub API release digest dependency
   - no third-party GitHub proxy detection

5. WARP custom route rule localization:
   - removed GitHub API checks for SagerNet/sing-geosite and MetaCubeX/meta-rules-dat
   - rule_set selections are converted to built-in domain_suffix rules
   - supported local sets: openai, gemini, claude, google, youtube, telegram, netflix, discord, github, twitter

6. systemd preflight validation:
   - sing-box systemd service contains ExecStartPre=${WORK_DIR}/sing-box check -C ${WORK_DIR}/conf

7. Stability policy:
   - quick/default install remains VMess + WS
   - advanced protocols remain available and can be added incrementally after installation

## Checks performed

- bash -n sing-box.sh: passed
- high-risk string scan: no matches for third-party proxies, remote rule-set APIs, stats, online QR services, no-check-certificate, fixed WARP key, or releases/latest.

## Notes

OpenAI reachability probing is intentionally retained per request. It uses HTTPS certificate verification and does not use --no-check-certificate.
