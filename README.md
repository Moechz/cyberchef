# cyberchef-for-tos

> TerraMaster TOS 7 App Center — **Docker application** package for
> [CyberChef](https://github.com/gchq/CyberChef).

## What this is

A four-file TOS Docker application archive (`cyberchefdocker.tar.gz`) that installs
CyberChef on TOS 7 through the App Center:

- `config.ini` — TOS application metadata (`application_type: docker`, id `cyberchefdocker`)
- `cyberchefdocker.lang` — 23-language superset (TOS requires 14)
- `cyberchefdocker.svg` — application icon (274 bytes, 2 SVG elements)
- `docker-compose.yml` — one service, image `mpepping/cyberchef:v11.6.0`

The repository also carries the documents the store review asks for — they are linked
from the App Center but not packed into the archive:
[`PRIVACY.md`](PRIVACY.md) (privacy policy) and [`NOTICE.md`](NOTICE.md)
(licences, image provenance, trademarks).

## How it works

| | |
|---|---|
| Container image | `mpepping/cyberchef:v11.6.0` (Docker Hub, used unmodified; amd64 + arm64) |
| Open | `http://<NAS-IP>:8052/` (container port 8000) |
| Runs as | non-root, `user: "1000:1000"` |
| Persistent data | `/data` → `/Volume1/DockerAppData/cyberchefdocker/data` (reserved; the image does not write to it) |
| Logs | `docker logs cyberchefdocker` (stdout/stderr) |

CyberChef is a static browser application: the container only serves HTML/JS/CSS. Every
recipe operation executes as JavaScript **inside your browser** — input and output never
reach the container, and the container stores no documents.

## Privacy

Short version: **no telemetry, no analytics, no account, no developer-operated server.**
The developer receives nothing; the NAS never transmits user data on its own.

- Recipes and options live in your browser's `localStorage`; files you open stay in browser memory.
- Server side there is only the container's HTTP access log (stdout) and an empty data folder.
- Outbound requests happen **only** when you use one of CyberChef's network operations:
  **HTTP request** (to the URL you type), **DNS over HTTPS** (`dns.google.com` or
  `cloudflare-dns.com`) and the **Show on map** button (`unpkg.com` for Leaflet,
  `tile.openstreetmap.org` for tiles). Nothing else — no CDN fonts, no update check.

The full policy — what is stored where, retention, your access/correction/deletion
channels, security measures and the complete endpoint table — is in
[`PRIVACY.md`](PRIVACY.md), also served at
<https://github.com/Moechz/cyberchef/blob/main/PRIVACY.md>.

## Data and uninstall

| Path | Purpose | Lifecycle |
|---|---|---|
| `/Volume1/DockerAppData/cyberchefdocker/data` | Application data folder reserved by the package. CyberChef is stateless, so it stays empty | Kept on uninstall unless you tick "delete data" |

- Recipes are **not** in that folder — they are in your browser. Clearing the browser's
  site data removes them.
- Uninstall: App Center → uninstall (tick "delete data"), or stop the container and remove
  `/Volume1/DockerAppData/cyberchefdocker/`. Nothing else is written on the NAS.

## Notices

CyberChef is Crown Copyright 2016–2026, GCHQ, licensed **Apache-2.0**. The image
`mpepping/cyberchef` is maintained by Martijn Pepping and is used unmodified; it builds
upstream v11.6.0 and serves it with `nginxinc/nginx-unprivileged` (Apache-2.0) on nginx
(BSD-2-Clause). This packaging itself is **MIT** (see `LICENSE`). "CyberChef" is a GCHQ
project name; this is an independent community submission, not affiliated with or
endorsed by GCHQ or TerraMaster. Full attributions, digests and trademark notes:
[`NOTICE.md`](NOTICE.md).

## Build

```bash
scripts/build.sh                       # -> out/cyberchefdocker.tar.gz + .sha256
bash scripts/build-dual.sh 11.6.0-1    # both architectures + official check
scripts/build.sh 11.6.0-1 aarch64      # one architecture explicitly
```

The build stages the four files, substitutes the version/platform placeholders,
removes CRLF/BOM/AppleDouble, then runs `tools/verify.py`, a review-standards self-check
(archive layout, JSON validity, required fields, 14+ language coverage, version
consistency, Docker-Hub-only images, fixed tag, reserved/recommended host ports,
non-root user, per-service healthcheck/TZ/restart, data path below the application data
root, never the `/Volume*` wildcard, comment-free sequence blocks, `x-app-meta` position,
no literal secrets and the SVG size/element limits).

## Local verification

```bash
./TOSAppSelfTestingTool -c out/cyberchefdocker.tar.gz   # official spec check
bash test/install-and-verify.sh                         # on-device E2E (needs root)
```

> Docker applications cannot be side-loaded through the App Center's manual
> install page (it accepts `.deb` only); the official self-testing tool's `-i`
> mode or `docker compose` are the only ways to install one for testing.

## Store submission checklist

1. Public GitHub repo (code + docs).
2. Release tag `v11.6.0-1` with `cyberchefdocker_x86_64.tar.gz`,
   `cyberchefdocker_aarch64.tar.gz` and their `.sha256` files.
3. Developer platform → version `11.6.0-1` per architecture → automated validation → review.
4. Submission remarks: "Rectified per review comments" (C3/C4/C5/C6/C7/T1).

## Limitations

- Pure client-side tool: no server-side history and no sharing between devices.
- Some operations intend to reach the internet (HTTP request, DNS over HTTPS, map tiles);
  they fail in an air-gapped network.
- The UI is plain HTTP; use a TLS reverse proxy for remote access.
- Very large inputs are limited by browser memory.

---

Packaged for TOS 7 by Moechz. Upstream image used unmodified from Docker Hub.
