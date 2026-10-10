# ha-eufy-sdk-addon

[![Lint](https://github.com/mega-yfue/ha-eufy-sdk-addon/actions/workflows/lint.yaml/badge.svg)](https://github.com/mega-yfue/ha-eufy-sdk-addon/actions/workflows/lint.yaml)
[![Builder](https://github.com/mega-yfue/ha-eufy-sdk-addon/actions/workflows/builder.yaml/badge.svg)](https://github.com/mega-yfue/ha-eufy-sdk-addon/actions/workflows/builder.yaml)
[![license](https://img.shields.io/github/license/mega-yfue/ha-eufy-sdk-addon)](./LICENSE)

The Home Assistant **add-on** wrapper around
[`ha-eufy-sdk-bridge`](https://github.com/mega-yfue/ha-eufy-sdk-bridge). A thin repository the
Supervisor can install: it pulls the bridge's Docker image, surfaces `EUFY_EMAIL` /
`EUFY_PASSWORD` / `EUFY_COUNTRY` as add-on options, and exposes the daemon through **ingress** (which
also provides the auth the raw WS lacks).

Same image runs both ways: HA OS / Supervised install this add-on; HA Container / Core run the bridge
image directly. The [`ha-eufy-sdk`](https://github.com/mega-yfue/ha-eufy-sdk) integration accepts a
URL and uses Supervisor discovery to auto-fill it on the add-on path.

> Runs on **amd64** and **aarch64** (Raspberry Pi 3/4/5 64-bit, x86). armv7 (32-bit) is not supported
> — the bridge's `node:24-alpine` base has no armv7 build.

## Local recording-history test add-on (this fork only)

This fork adds a third, **experimental** entry: **eufy-sdk bridge (recordings test)**.
It uses an on-device Docker build from the official bridge runtime plus two source
modules pinned to our CI-verified
[recording-index change](https://github.com/zetard/ha-eufy-sdk-bridge/pull/1).
It does **not** publish or require a forked Docker registry image.

Install this fork as a *separate* Home Assistant add-on repository:
`https://github.com/zetard/ha-eufy-sdk-addon`. Enable **Advanced Mode**
in your HA profile to see the experimental entry.

**Do not run two Eufy bridge add-ons concurrently.** This test channel may
require a new Eufy login; copy the working configuration privately and keep
the old add-on intact as a rollback route. See
[recordings test DOCS](./eufy_sdk_bridge_recordings/DOCS.md)
before switching.

## Install

**One click** — add this repository to your Home Assistant:

[![Open your Home Assistant instance and show the add-on store with a specific repository URL pre-filled.](https://my.home-assistant.io/badges/supervisor_store.svg)](https://my.home-assistant.io/redirect/supervisor_store/?repository_url=https%3A%2F%2Fgithub.com%2Fmega-yfue%2Fha-eufy-sdk-addon)

Or manually: **Settings → Add-ons → Add-on Store → ⋮ → Repositories**, add
`https://github.com/mega-yfue/ha-eufy-sdk-addon`, then install **eufy-sdk bridge**.

After install, set your `email` / `password` / `country` in the add-on **Configuration** tab and start
it. On first login eufy may ask for **2FA or a captcha** — resolve it from the
[`ha-eufy-sdk`](https://github.com/mega-yfue/ha-eufy-sdk) integration's config flow, which
auto-discovers this add-on. See [DOCS.md](./eufy_sdk_bridge/DOCS.md) for the full option reference.

## Dev / edge channel

This repository ships **two** add-ons: the stable **eufy-sdk bridge** and a **eufy-sdk bridge (dev)**
that tracks the bridge's rolling `:dev` image — for trying changes before a stable release. The dev
add-on is flagged `stage: experimental`, so it only appears in the store when your HA profile has
**Advanced Mode** enabled. Both share the same eufy account and the same ports, so **run one at a
time** (a second bridge on the same account displaces the first's session).

Releases map to channels automatically: a **full GitHub Release** publishes the stable image
(`addon-eufy-sdk-bridge`), a **pre-release** publishes the dev image (`addon-eufy-sdk-bridge-dev`).

## Where it fits

| Repo | Role |
| --- | --- |
| [`eufy-sdk`](https://github.com/mega-yfue/eufy-sdk) | the HA-agnostic library |
| [`ha-eufy-sdk-bridge`](https://github.com/mega-yfue/ha-eufy-sdk-bridge) | WS + HTTP + go2rtc daemon (Docker) |
| **`ha-eufy-sdk-addon`** | **this** — Supervisor add-on wrapper |
| [`ha-eufy-sdk`](https://github.com/mega-yfue/ha-eufy-sdk) | the HACS integration (front door) |

## Contributing

Contributions are welcome — please branch from **`dev`** and open your PR against **`dev`** (not
`main`). See [CONTRIBUTING.md](./CONTRIBUTING.md) for the branch model, CI checks, and how releases
are cut.
