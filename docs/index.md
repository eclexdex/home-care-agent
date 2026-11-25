# Home Care Agent — a local-first elder care safety net for Home Assistant

**A panic button that actually reaches somebody, a door watch the resident
arms, freeze and heat-failure alerts, a night path, and a daily family digest
— all running on hardware in the house, with no vendor cloud, no cameras, and
no subscription.** Built for an elderly parent, grandparent, or spouse living
alone who wants to stay in their own home.

This is the companion documentation site for the open-source package
[**eclexdex/home-care-agent**](https://github.com/eclexdex/home-care-agent).
MIT licensed. Every name and chat id in the repository is a placeholder you
fill in for your own household.

> Care, not surveillance. An elder care system built without the resident's
> informed agreement is surveillance wearing a cardigan.

## Start here

| If you want to… | Read |
|---|---|
| understand what you are buying into, in four minutes | [the repository README](https://github.com/eclexdex/home-care-agent#readme) |
| know exactly what this is **not** | [What this is not](https://github.com/eclexdex/home-care-agent#what-this-is-not) |
| buy the right hardware, once | [Bill of materials](https://github.com/eclexdex/home-care-agent/blob/main/bom/BOM.md) |
| install it on install day | [How to install](how-to-install.md) |
| understand the event → response model | [The care-net model](CARE-NET-MODEL.md) |
| print something for the resident to read | [Privacy one-pager](PRIVACY-ONE-PAGER.md) |
| see what the daily digest reads like | [Sample digest](https://github.com/eclexdex/home-care-agent/blob/main/EXAMPLES/digest-sample.md) |

## The five automations

Each file is paste-ready YAML with a `CHANGE THESE BEFORE FIRST USE` header
listing the placeholders.

1. **Health panic button** — one press by the usual chair drives the siren,
   turns on the lights, and pushes an immediate alert to the family's phones.
   This is the reason the project exists.
2. **Home/Away arming** — a door-side button cycles an armed state. The
   resident controls what is armed, physically and locally.
3. **Environment alerts** — freeze and heat-failure warnings (a heat failure
   in winter is a genuine health risk: pipes and people), plus once-only
   battery-low notices.
4. **Night path** — mmWave presence drives a low, warm path so a 3 a.m. trip
   to the kitchen does not end in a fall, with a quiet idle check-in nudge
   instead of a movement log.
5. **Door watch** — siren and alert on door opening **only when the resident
   has armed Away**. Disarmed, it is deaf by design.

## What this is not

- **Not a medical device.** No regulatory clearance, no diagnosis, no
  treatment claim.
- **Not a monitored service.** No subscription, no call centre — the alert
  goes to your family's phones, which is the point.
- **Not a camera system.** No optics, no cloud video AI, no footage of the
  resident anywhere.
- **Not fall detection.** The night path and idle check-in reduce fall
  *risk*; they do not detect a fall.
- **Not a substitute for emergency services.** The panic button's job is to
  make sure a human finds out.

## Why local-first

Elder care technology usually arrives as surveillance: cameras with cloud AI,
vendor apps that phone home, presence data that becomes telemetry somebody
else owns. That trade is not required. The safety logic here runs with the
internet unplugged; alerts leave the house only over channels the family
itself controls. Presence is mmWave yes/no, consumed on the LAN, never
exported, never sold.

## Requirements

- Home Assistant (HAOS recommended) on modest hardware
- A Zigbee 3.0 coordinator under ZHA
- Zigbee panic button, door/window sensors, mmWave presence, temperature and
  humidity sensors, a siren — see the BOM for three build tiers and the
  **anti-BOM** of things deliberately left out (indoor cameras, microphones,
  wearables to charge, smart speakers, cloud bridges)
- Optionally: a local AI agent seat for the daily digest (read-only token,
  forbidden from acting on devices)

## License

MIT — see [LICENSE](https://github.com/eclexdex/home-care-agent/blob/main/LICENSE).
The covenant is the one thing we ask you not to strip from your fork's
conscience.
