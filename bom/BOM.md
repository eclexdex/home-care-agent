# Hardware bill of materials

Three build tiers of generic, off-the-shelf hardware for the care net: **Minimal** (the panic net only), **Standard** (all five automations covered), and **Comfort** (resilience and comfort on top). Every part is local-first: Zigbee 3.0 under ZHA by default, no vendor cloud, no camera AI, nothing that phones home.

**How to read these tables.** Each line names a device *class* — protocol, power, form factor — not a SKU. The class tells you what to shop for; the "e.g. …-class" names are examples, not endorsements, and any product that matches the class is a fine choice. Costs are **approximate retail USD ranges; prices drift — verify current pricing before you buy.**

**Assumptions behind every tier:** the HA host sits on ethernet (not WiFi), the coordinator dongle is on a USB 2.0 port with a short cable, and the two helpers the automations expect (`input_boolean.home_away`, `input_boolean.battery_low_sent`) are created in the HA UI — they need no hardware.

## Tier 1 — Minimal: the panic net

Covers `health_panic.yaml` end to end, plus the freeze/heat-failure alerts and the battery watch from `env_alerts.yaml`. This is the tier that saves a life in the plain way.

| Role | Example device class | Power | Mesh / protocol notes | Approx. cost (USD) | Elderly-appropriateness |
|---|---|---|---|---|---|
| HA host (infra) | Home Assistant Green class, RK3528-class HAOS box, or a generic mini-PC running HAOS — all valid; ethernet preferred | mains + ethernet | Single point of failure for the whole net; keep it cool, dry, and near the network. No vendor cloud in any option. | ~$60–200 | Out of sight — a cupboard, not the resident's line of sight. Nothing to wear or charge. |
| Zigbee 3.0 USB coordinator (infra) | Zigbee 3.0 USB dongle (e.g. SONOFF MG24-class, SkyConnect-class, CONBEE-class) | USB | The only mesh in the net. Channel 11/15/20/25, away from 2.4 GHz WiFi channels 1/6/11. USB 2.0 port, short cable. | ~$20–45 | Invisible infra. |
| Panic button (`health_panic.yaml`) | Zigbee 3.0 wireless button, big-surface / big-rocker single-press (e.g. SNZB-01P-class single-press, or a big-button multi-press configured as single-press) | Coin cell (CR2032-class), 1+ year | Sleepy end device; one press wakes it and transmits. One job, done reliably. Mount chair-side, waist height, within arm's reach. | ~$15–35 | A big plain button that looks like a doorbell, not a medical alert pendant. Nothing to charge. One press. |
| Indoor siren (`health_panic.yaml`) | Zigbee 3.0 indoor siren (alarm class) | AA batteries, or mains class if preferred | Battery class = end device, mains class = router. Loudness is the point — verify it is audible from where the care team sleeps. | ~$20–40 | Reads as a home security alarm, which is what it is. Placed where the family hears it, not where it startles the resident. |
| Temperature sensor (`env_alerts.yaml`) | Zigbee 3.0 temperature sensor (e.g. Aqara temperature/humidity class) | CR2032-class coin cell | End device. Put it in the coldest room the resident sleeps or spends the most time — freeze and heat-failure logic is the winter safety net. | ~$15–30 | Plain; wall-clip or tabletop. Nobody has to interact with it. |
| Front door contact (`env_alerts.yaml`) | Zigbee 3.0 door/window contact (e.g. SNZB-01-class) | CR2032-class coin cell | End device. On the battery watch list. In Standard tier it also feeds `door_watch.yaml` when armed. | ~$15–25 | A small contact on the door frame — unobtrusive, and clearly not a camera. |

**Subtotal: ~$145–375** (approximate retail, verify current pricing).

## Tier 2 — Standard: all five automations

Everything in Minimal, plus the lines below. This is the tier the repo's five YAML files were written for.

| Role | Example device class | Power | Mesh / protocol notes | Approx. cost (USD) | Elderly-appropriateness |
|---|---|---|---|---|---|
| Home/Away button (`home_away.yaml`) | Zigbee 3.0 wireless button, multi-press (big-button class) | Coin cell (CR2032-class) | End device. Door-side, within arm's reach of the exit. | ~$15–35 | A plain door-side button. The resident arms and disarms with a physical button — consent and control stay with the person at home. |
| Night path lighting (`night_path.yaml`) | Dimmable Zigbee bulb (1–3), hallway + kitchen | Mains via socket | If the home already has dimmable lights on this path, this line is zero. The presence sensor drives them low and warm. | ~$10–20 | A warm, low light at 3 a.m. — the opposite of a startle. |
| mmWave presence sensor (`night_path.yaml`) | mmWave presence sensor (Zigbee, USB, or Matter class), hallway placement | Mains (USB or PoE) — prefer mains: doubles as a mesh router | mmWave registers presence the way PIR cannot. Day + night coverage for the path and the idle check-in nudge. Aim at the path, not the bedroom. | ~$30–60 | Invisible, no light on, nothing to wear. The check-in nudge is a "call or knock" prompt — a care affordance, not a movement log. |
| Remaining door/window contacts (`door_watch.yaml` + `env_alerts.yaml`) | Zigbee 3.0 door/window contact class, 2–3 more (back door + 1–2 windows) | Coin cell (CR2032-class) | End devices. Armed-Away logic fires only when the resident has armed — more doors, no more noise. | ~$30–75 | The same unobtrusive contacts as the front door. |
| Humidity or temp/humidity (`env_alerts.yaml`) | Zigbee 3.0 humidity or temp/humidity combo | Coin cell (CR2032-class) | Pairs with freeze logic. Moisture is a comfort signal the digest can carry — not a health verdict. | ~$20–35 | Nobody has to interact with it. |
| Water leak sensor (`env_alerts.yaml` — prepared gap) | Zigbee 3.0 water leak sensor class (under-sink, water-heater) | Coin cell or CR | Note: a leak automation is not in the package yet (known gap). A 10-line HA template fires the same family alert as everything else — wire it the same day you install it. | ~$15–30 | Saves the floor and the heating bill. Unobtrusive. |
| Battery backup / UPS (infra) | USB battery bank sized for the host + coordinator — or skip if the host class ships with a built-in battery (the HA Green class does) | Mains | The net must outlive the power outage, not just the internet cut. Test once a year. | ~$25–60 (skippable) | Quiet resilience. The resident never has to think about it. |
| (Optional) exterior camera (infra) | PoE IP camera, local recording only (NVR / FFmpeg class), no cloud AI, **exterior only** | PoE | The reference cart marks this optional. If you include it: the street, not the home — local storage only, no vendor cloud, no AI. | ~$50–120 (+$15–30 PoE injector) | Optional. A doorbell-class view of the street. If the family is uneasy with any camera, the net works without it. |

**Subtotal: ~$340–810** (approximate retail, verify current pricing).

## Tier 3 — Comfort: resilience + comfort

Everything in Standard, plus the lines below.

| Role | Example device class | Power | Mesh / protocol notes | Approx. cost (USD) | Elderly-appropriateness |
|---|---|---|---|---|---|
| Redundant power (infra) | Second power path: battery backup for the router/switch, a PoE injector as spare, or host class with built-in battery plus a UPS | Mains | One failure mode left — the grid — and this tier removes most of it. | ~$50–120 | Invisibility, squared. |
| Second mmWave presence sensor (`night_path.yaml`) | mmWave presence sensor class, second zone (kitchen, if the first covers the hallway — or vice versa) | Mains (USB or PoE) | Two zones = the check-in nudge stops firing when the resident is simply in the other room. Comfort, not more coverage of the person. | ~$30–60 | The nudge gets quieter and more accurate. Nothing new to wear. |
| Air quality sensor (`env_alerts.yaml`, comfort) | Zigbee 3.0 air quality sensor (CO2 / VOC / PM2.5 class) | USB or coin cell | The digest reports values. The covenant says no health inference — a stale room is a ventilation note, not a diagnosis. | ~$40–80 | A small tabletop sensor. Nobody has to interact with it. |
| Wall tablet (infra) | Used 10–11" Android tablet running the HA dashboard, mounted by the door | USB | A window on the home for the family and on the family for the resident: state, not a movement timeline. The resident can use it for photos and video calls, too. | ~$30–80 used / ~$80–150 new | A home tablet, not a "senior gadget." The care recipient uses it for the things a home tablet is for. |
| Spares (infra) | Spare panic button, spare door contact, spare temp/humidity — one each | Coin cell (CR2032-class) | A dead coin cell at 2 a.m. is a bad night. The spares drawer is part of the net. | ~$45–90 | Reliability without drama. Nobody notices the spares drawer. |

**Subtotal: ~$535–1,310** (approximate retail, verify current pricing).

## Deliberately NOT in this BOM

| Excluded | Why (covenant: care, not surveillance) |
|---|---|
| Indoor cameras | Presence does the care job. Watching the person at home is surveillance, and the covenant forbids it. |
| Microphones (always-on, wake-word) | Audio is the most intimate data there is. A care net that listens has stopped being a care net. |
| Wearables, pendants, anything to charge | Nothing the care recipient wears, charges, or can lose. A dead pendant is a false sense of safety, and a body-worn tracker is a collar, not a safety net. |
| Smart speakers | Always-listening hardware plus a cloud voice assistant. Nothing in the five automations needs one. |
| Cloud bridges, vendor hubs | The net runs Zigbee under ZHA, locally. Anything that phones home is out by definition. |
| mmWave in bedrooms / bathrooms | The most private rooms are excluded. Presence lives on the path — hallway, kitchen — where falls actually happen. |

## Buy nothing that stigmatizes

If a product looks like a medical device, buy a different one. The panic button looks like a doorbell. The siren reads as a home alarm. The tablet is a home tablet. The care recipient is a person, not a patient, and this house is a home, not a clinic. If the hardware makes the person at home feel watched or managed, it is the wrong hardware — and no tier in this BOM is an exception.

## Smoke and CO: out of scope, non-negotiable

The package carries a commented smoke-detector placeholder in `env_alerts.yaml`, and this BOM deliberately does not list smoke or CO sensors. Real, code-compliant smoke and CO detectors — installed per local fire code, tested monthly, batteries changed on schedule — are life safety that this repo does not replace and should never let anyone feel that it does.
