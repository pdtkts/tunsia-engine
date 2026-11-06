# tunsia-engine

Public mirror of the browser-engine archives the TUNSIA launcher installs, kept
here so an upstream/shared-CDN overwrite can never strand an engine version.

## Why this repository exists

Upstream (ProxyShard/ShardBrowser) publishes its `runtime.json` manifest pointing
at a handful of **shared CDN keys** — one per platform, e.g. `ShardX-Windows.zip`.
Every engine bump overwrites those keys **in place**. The previous build is gone,
with no archive and no redirect: the Chromium 156 build is already unrecoverable
this way. A launcher that recorded the old etag then silently downloads the new
build under the old version's name.

This repository holds the durable copy: one GitHub release per engine build, with
the archive assets attached. The release tag is the Chromium version.

## What is here

`engine-archives.json` — the registry the TUNSIA launcher reads (via
`raw.githubusercontent.com`), mapping engine → Chromium version → platform to a
mirrored URL, its `sha256`, and the upstream etag the archive was captured from.

Releases — one per captured build:

| Tag | Contents |
| --- | --- |
| `engine-157.0.8103.39` | `ShardX-Windows.zip`, `ShardX-Linux.zip`, `ShardX-Mac-arm64.zip`, `ShardX-Fingerprints.zip` |

Widevine CDM archives are intentionally **not** mirrored: their etags are
byte-stable across engine bumps, so the upstream CDN key stays valid.

## Provenance

Each captured archive is recorded with the S3 multipart etag the CDN served at
capture time and the `sha256` of the bytes. Both are checked against upstream's
`runtime.json` `archives` map before a version is marked installable, so a
capture is only trusted once it matches upstream's own manifest.

These archives are third-party browser-engine builds redistributed unchanged.
The launcher's own source is not here; this repository hosts engine binaries
only.
