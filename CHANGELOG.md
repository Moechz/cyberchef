# Changelog

All notable user-visible changes to the CyberChef TOS application package.

## 11.6.0-1 — 2026-11-22

Remediation of the App Center listing review (x86_64 `10.19.4-2`, checklist
TOS-APP-CL-20261121-01, items C3 / C4 / C5 / C6 / C7 / T1). No blocking finding was
recorded for either architecture.

### Security (T1)
- Upgraded the container image from `mpepping/cyberchef:v10.19.4` (built 2024-10-24 on
  nginx 1.27.2) to **`mpepping/cyberchef:v11.6.0`**, which is built from upstream
  CyberChef v11.6.0 and ships the current `nginxinc/nginx-unprivileged` base image —
  upstream v11.6.0 exists specifically to "bump the Nginx image to latest to address a
  CVE" and it also carries the current versions of the bundled JavaScript dependencies
  (dompurify, node-forge, follow-redirects, qs, fast-uri, ip-address, pbkdf2, morgan,
  handlebars and others). This supersedes the components named in the scan report.
- The image is a single multi-arch OCI index (linux/amd64 + linux/arm64), so both
  packages run the same upstream build.

### Privacy policy (C3 / C4 / C5)
- Added [`PRIVACY.md`](PRIVACY.md): what is stored and where, retention, user rights and
  the deletion channel, security measures, the complete third-party endpoint table and a
  contact address.
- The policy is reachable from the app: the App Center **Help** link and `config.ini`
  `help` now point at <https://github.com/Moechz/cyberchef/blob/main/PRIVACY.md>.

### Data-collection alignment (C6)
- The description, `important` note and README no longer imply that CyberChef *never*
  talks to the network. They now state that all operations run in the browser and that
  only three opt-in operations make outbound requests: **HTTP request** (to the URL the
  user types), **DNS over HTTPS** (`dns.google.com` / `cloudflare-dns.com`) and
  **Show on map** (`unpkg.com` + `tile.openstreetmap.org`). Verified against the source
  of the shipped version, not copied from older notes.

### User data rights (C7)
- Access / correction / deletion channels documented in `PRIVACY.md` §4 (browser site
  data, the `/Volume1/DockerAppData/cyberchefdocker/` folder, uninstall with "delete
  data", plus an e-mail channel for questions about the listing records).

### Documentation and metadata
- Added [`NOTICE.md`](NOTICE.md): upstream Apache-2.0 attribution, image provenance and
  digests, trademark statement, MIT licence of the packaging.
- `tosapp.json`: `license` corrected to `MIT` (the packaging licence in `LICENSE`);
  `upstream_license` stays `Apache-2.0` (CyberChef). Removed the stale `probe` block,
  which described the base image (`101`/nginx, ports 8000+8080, an unrelated GitHub
  repository) instead of this application.
- README: added Privacy, Notices, Data and uninstall, Build and verification sections.
- Host port unchanged (8052 → 8000).

## 10.19.4-1 — 2026-11-14

### Added
- First release. CyberChef (`mpepping/cyberchef:v10.19.4`, Docker Hub image, unmodified)
  packaged as a TOS 7 Docker application, published on port 8052 and running
  as a non-root user (1000:1000).
- Persistent data is kept under `/Volume1/DockerAppData/cyberchefdocker/`.
