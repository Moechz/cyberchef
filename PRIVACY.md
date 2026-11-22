# Privacy Policy — CyberChef for TerraMaster TOS (`cyberchefdocker`)

**Last updated:** 22 November 2026
**Data controller / contact:** Moechz — moechz@163.com (developer of this TOS package,
not of the upstream CyberChef project)
**Applies to:** version `11.6.0-1` and later of the TOS application `cyberchefdocker`,
the container image it runs (`mpepping/cyberchef:v11.6.0`) and this repository.

## 1. Summary

- **The app runs entirely on your NAS and in your own browser.** The developer operates
  no server, receives no data and has no way to access your NAS.
- **No telemetry, no analytics, no tracking, no account, no advertising.** The packaged
  build contains no Google Analytics / gtag / pixel snippet (upstream ships one for its
  public demo site only, and it is not part of the build served here).
- **CyberChef is a client-side tool.** Every operation of a recipe is executed as
  JavaScript inside your browser tab. The input you paste or open, the recipe and the
  output never leave the browser unless *you* add one of the three network operations
  listed in §6.
- **The container is a plain static file server.** It serves HTML/JS/CSS/images from the
  image, has no database, no user accounts, no upload endpoint and no folder it writes
  documents into.
- **The app contacts nothing on its own.** No update check, no heartbeat, no licence
  call. Outbound requests happen only when you actively use the network operations.

## 2. What is stored, and where

| Data | Where it lives | Written by |
|---|---|---|
| Recipes, options and your last session state | The browser's `localStorage` for the origin `http://<NAS-IP>:8052` — on **your computer**, not on the NAS | Your browser |
| Files you open or drop into the app | Processed in browser memory only; never uploaded, never written to the NAS | Your browser |
| Output you want to keep | Only when you use a download/save button — saved to your computer | Your browser |
| `/Volume1/DockerAppData/cyberchefdocker/data` | An empty data folder reserved by the package so the app has its own TOS data directory. The CyberChef image does not read or write it | — |
| HTTP access log (client IP, timestamp, request path, user agent) | The container's stdout/stderr only — readable with `docker logs cyberchefdocker` | nginx inside the container |

There is **no server-side storage of user content, no session store, no database and no
cookie set by the app**. Nothing listed above is sent to the developer.

## 3. Retention period

- **Browser data:** kept until **you** clear it — *Settings → clear storage* in CyberChef,
  the browser's "clear site data" for the NAS address, or deleting the browser profile.
  There is no automatic expiry.
- **Container logs:** held by the Docker daemon (the `json-file` driver by default, with
  its own rotation settings) until you restart/recreate the container or prune the logs.
  They stay on your NAS and are never transmitted.
- **Application data folder:** remains empty. It is kept on uninstall unless you tick
  "delete data" (or remove the folder yourself).

## 4. Your rights and the deletion channel

You have full control because every piece of data stays on hardware you own.

| Right | How |
|---|---|
| **Access** | Recipes/options: open CyberChef and look at *Save recipe / Load recipe* or your browser's site-data view for the NAS address. Files: your own files on your own computer. Server side: there is nothing to access beyond the container log (`docker logs cyberchefdocker`). |
| **Correct** | Edit or delete a recipe in the CyberChef UI, or clear the stored state in *Settings*. |
| **Delete** | Clear the site data for the NAS address in your browser; delete the folder `/Volume1/DockerAppData/cyberchefdocker/`; or uninstall the app with "delete data" ticked. Deleting the browser data removes the recipes; deleting the folder removes the (empty) application data directory; uninstalling removes the container and image. |
| **Ask the developer** | Email **moechz@163.com** with the subject "CyberChef data request" and a reply follows within 30 days. This channel covers the publishing records (GitHub repository, store listing) and any question about this policy. Note that the developer holds **no copy of your recipes, input or output** and therefore cannot read, restore or delete them for you. |

## 5. Security measures

- The container runs as the unprivileged user **uid 1000 (gid 1000)**, never root, with
  no `privileged` flag, no `cap_add`, no host network and no Docker socket.
- Exactly one port is published: **host 8052 → container 8000**, HTTP.
- The image tag is **pinned** (never `:latest`); this release moves the image from
  `v10.19.4` to `v11.6.0` so the nginx base image and the bundled JavaScript
  dependencies carry the current security fixes.
- The image's own files are read-only to the running user; the app has no write path
  into your data, so a compromise of the static server cannot corrupt user files.
- Because the UI is plain HTTP, put it behind a TLS reverse proxy if you reach it from
  outside your LAN, and keep the package updated through the App Center.

## 6. Third-party sharing and API disclosure

The developer shares nothing and operates no endpoint. The container ships no
third-party SDK, no crash reporter and no analytics.

CyberChef's catalogue is local — hashing, encoding, encryption, compression, parsing and
format conversion all run in the browser. A small number of operations are network
features by design; they only run if **you** add them to a recipe (or click the map
button), and each one sends only the data described below. The list was taken from the
source of the exact version shipped here (CyberChef v11.6.0).

| Operation / feature | Endpoint | What is sent | Destination / region |
|---|---|---|---|
| **HTTP request** (`HTTPRequest.mjs`, shown as "HTTP request") | The arbitrary URL **you** type into the operation | Exactly the request you configure: method, headers and, for POST, the input as the body. Nothing else. | The site you chose — wherever it is hosted |
| **DNS over HTTPS** (`DNSOverHTTPS.mjs`, "DNS over HTTPS") | `https://dns.google.com/resolve` (Google Public DNS) **or** `https://cloudflare-dns.com/dns-query` (Cloudflare 1.1.1.1) — chosen by the "Resolver" option, Google is the default | The hostname and record type you ask it to resolve, plus the ordinary HTTP metadata of any web request (your browser's IP address, user agent) | Google LLC, United States / Cloudflare, Inc., global anycast |
| **Show on map** (`ShowOnMap.mjs`, the map button on coordinate output) | `https://unpkg.com/leaflet@1.9.4/dist/leaflet.js` and `…/leaflet.css`, then `https://tile.openstreetmap.org/{z}/{x}/{y}.png` | The Leaflet library request, then the map tiles for the area you display (which reveals the coordinates you are looking at), plus your browser's IP address and user agent | unpkg (Cloudflare CDN, United States) / OpenStreetMap Foundation, United Kingdom |
| **Image pull** (install and update only) | `registry-1.docker.io` and its CDN, or the registry mirror configured on your NAS | An anonymous image pull — no account, no NAS data | Docker Hub, United States / your mirror |

Nothing else in the package makes a network connection: no fonts, scripts, stylesheets or
icons are loaded from a CDN at page load — the whole UI is served by the container from
your NAS.

**Opting out.** Ignore those three operations (or remove them from the recipe) and the app
never makes an outbound request. You can also block `dns.google.com`,
`cloudflare-dns.com`, `unpkg.com` and `tile.openstreetmap.org` in your browser or DNS —
the rest of CyberChef keeps working; only those features fail.

## 7. Children

The app is a general-purpose data tool with no account and no user-generated content
sharing; it collects no personal data and is not directed at children.

## 8. Changes to this policy

This policy is versioned together with the package. Material changes are described in
`CHANGELOG.md` and in the release note shown by the App Center, and the "Last updated"
date above is bumped. The current text always lives at
<https://github.com/Moechz/cyberchef/blob/main/PRIVACY.md>.

## 9. Contact

Moechz — **moechz@163.com**
Issue tracker for this package: <https://github.com/Moechz/cyberchef/issues>
Upstream project (not the data controller for this package):
<https://github.com/gchq/CyberChef>
