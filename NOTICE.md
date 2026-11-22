# Notices and attributions — CyberChef for TerraMaster TOS (`cyberchefdocker`)

This repository contains **only packaging metadata** for the TerraMaster TOS 7
App Center. It does not redistribute CyberChef source code: the package installs the
upstream container image from Docker Hub unmodified.

## Upstream software

| Component | Copyright | Licence | Source |
|---|---|---|---|
| CyberChef | Crown Copyright 2016–2026, GCHQ | Apache-2.0 | <https://github.com/gchq/CyberChef> |
| nginx | Copyright (C) 2002-2026 Igor Sysoev / F5, Inc. | BSD-2-Clause | <https://nginx.org/> |
| nginx-unprivileged image | Copyright nginx, Inc. | Apache-2.0 | <https://github.com/nginx/docker-nginx-unprivileged> |

Full licence texts ship inside the image (`/usr/share/nginx/html/CyberChef_*.zip`
contains the upstream `LICENSE`), and CyberChef's licence header is embedded in the
served JavaScript.

## Container image

| | |
|---|---|
| Reference | `mpepping/cyberchef:v11.6.0` |
| Maintainer | Martijn Pepping (`mpepping/docker-cyberchef`, independent of GCHQ) |
| Build | upstream CyberChef **v11.6.0** compiled with Node 26, served by `nginxinc/nginx-unprivileged:stable-alpine` |
| Built | 2026-11-21 (nginx 1.30.6 base, upstream CyberChef v11.6.0) |
| Platforms | `linux/amd64` and `linux/arm64` (single OCI image index) |
| Used as | unmodified — this package only sets `user`, the published port, `TZ`, a volume and a healthcheck |

The image is pulled from Docker Hub (`registry-1.docker.io`) or the mirror configured on
the NAS. No layer is rebuilt, patched or republished by this packaging.

## Trademarks

- **CyberChef** is the name of the GCHQ project; the name and logo are used here only to
  describe the software that is being installed. This package is an independent
  community submission and is **not affiliated with, endorsed by or supported by GCHQ**.
- **TerraMaster**, **TOS** and **App Center** are trademarks of TerraMaster.
- All other marks belong to their respective owners.

## Licence of this packaging

The packaging files in this repository (`tosapp.json`, `src/config.ini`,
`src/cyberchefdocker.lang`, `src/cyberchefdocker.svg`, `src/docker-compose.yml`,
`scripts/`, `tools/`, documentation) are released under the **MIT licence** — see
[`LICENSE`](LICENSE). That licence does **not** cover CyberChef or the container image,
which remain under Apache-2.0 and their own terms.

## Privacy

What the app stores and which endpoints it can contact: see [`PRIVACY.md`](PRIVACY.md).
