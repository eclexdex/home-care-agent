# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project intends to follow [Semantic Versioning](https://semver.org/).

This changelog is honest: entries describe what actually changed, not what a roadmap wishes.

## [Unreleased] - 2026-10-15
### Added
- README banner: screenshot of the real deployed care dashboard (anonymized; re-encoded, camera metadata stripped; vision-QA'd for PII before publishing).
### Removed
- Generated banner + covenant illustrations (tried, dropped in favor of the real product shot).
## [0.2.0] - 2026-10

### Added
- Hardware BOM in `bom/` in three tiers (Minimal / Standard / Comfort), machine-readable `bom/bom.yaml`, plus an anti-BOM: what we deliberately do *not* include — indoor cameras, microphones, wearables-to-charge, smart speakers, cloud bridges, mmWave in the most private rooms. Cost figures are approximate ranges and need verification against current prices.
- Drop-in Hermes skill family in `skills/home-care-agent/` for operators running an agent seat alongside HA.
- `docs/PRIVACY-ONE-PAGER.md` — the covenant as a single printable page for the household.
- `docs/CARE-NET-MODEL.md` — a plain-language model of what the care net sees, stores, and categorically never does.
- `EXAMPLES/digest-sample.md` and `EXAMPLES/alert-sample.md` — fictional samples of the agent's digest and urgent-relay voice, so nobody meets the tone for the first time at 3 a.m.
- `CONTRIBUTING.md`, `SECURITY.md`, and issue + PR templates under `.github/`.
- `CHANGELOG.md` — this file.

### Changed
- README gained a "What's in the repo" map linking the BOM, skill family, docs, examples, and community files. The README links; the detail lives in the files.

### Fixed
- Nothing. No bugs were reported or fixed in this cycle.

## [0.1.0] - 2026-10

### Added
- Repo created with five paste-ready automations in `automations/`: `health_panic.yaml`, `home_away.yaml`, `env_alerts.yaml`, `night_path.yaml`, `door_watch.yaml` — deliberately pre-built so install day is pair-and-paste, not design.
- Install-day runbook in `docs/how-to-install.md`, including the reference hardware cart and the helper entities the automations expect.
- Optional agent layer: `agent/README.md` and `agent/SOUL.template.md` — read-only HA token minted via a non-admin user, verbatim urgent relays, morning digest, housekeeping rules, and the dignity clauses.

### Fixed
- `home_away.yaml`: when the `input_boolean.home_away` helper came up `unavailable`, `unknown`, or `off` (a fresh install always does), the first button press could double-toggle the arming state. The template logic now treats those states as "home," so the first press reliably arms Away. This matches the code comment in `automations/home_away.yaml`.
