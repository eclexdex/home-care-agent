# How to install (install-day runbook)

The hardware half of a care build, generalized from a real one. Adjust the
cart to your household; keep the install order and the pairing discipline.

## Cart → role map (reference build)

| Item | Qty | Role in HA |
|---|---|---|
| Small HAOS box (e.g. RK3528-class) | 1 | Home Assistant host — put on **ethernet**, not wifi |
| Zigbee 3.0 USB coordinator (e.g. SONOFF MG24 dongle) | 1 | Zigbee coordinator → ZHA add-on (NOT the vendor cloud app) |
| Wireless Zigbee button pushes (e.g. SNZB-01P) | 2 | #1 = HEALTH PANIC (by the usual chair) · #2 = HOME/AWAY (front door) |
| Door/window sensors (e.g. SNZB-04) | 3–4 | front/back doors + a window watch |
| mmWave presence sensors (e.g. SNZB-06) | 1–2 | usual room (chair presence) + main path night lighting |
| Temp/humidity sensors (e.g. SNZB-02) | 1–2 | usual room + main room — freeze alert (pipes / heat failure) |
| Indoor Zigbee siren | 1 | panic / armed annunciator |
| Local PoE camera (ONVIF/RTSP, **no cloud AI**) | 0–1 | exterior door watch, optional snapshot in `door_watch.yaml` |
| Wall tablet | 1 | dashboard by the door: Home/Away status, one-tap help |

Deliberate non-integrations you should expect to have: 433-MHz RF doorbells
(not Zigbee — HA can't see them), battery under-cabinet lights (no
telemetry). Accept them as dumb devices; don't promise automations they
can't do.

## Honest gaps to close before trusting the net

1. **Smoke/CO is NOT in this package.** Pair real Zigbee smoke/CO detectors
   (one near the usual chair) and enable the commented placeholder in
   `env_alerts.yaml`. A grocery-store battery alarm is not an automation.
2. **Egress/emergency planning is physical.** The panic button brings the
   siren and the family's phones; the fire route stays the resident's own
   rehearsed plan.
3. **Decide the egress of alerts at boot:** mobile-app push, Telegram, or
   both (see `.env.example`). Everything else stays inside the house.

## Install order

1. Install HAOS on the box (ethernet), onboard, create admin, set locale/TZ.
2. Plug the coordinator → *Settings → Devices & Services → ZHA* → pick its
   tty. **Pair the siren first** — it's a mains-powered router node and it
   stabilizes the mesh for everything after it.
3. Pair in order: panic button (`button_panic_usual_chair`), home/away button
   (`button_homeaway_door`), door/window sensors, mmWave presence,
   temp/humidity.
4. Create the two helpers the automations expect:
   `input_boolean.home_away`, `input_boolean.battery_low_sent`
   (*Helpers → Input boolean*).
5. Set up notify services (Companion app and/or Telegram bot).
6. Paste the files in `automations/` (pure YAML) into HA config, replacing
   every `CHANGE THESE` placeholder, then **Check configuration**.
7. Optional: the wall tablet (Fully Kiosk or the HA Companion app), mounted
   by the door.
8. **Resident walk-through:** press the panic button, hear the siren, watch
   the phones ring. Repeat until it's muscle memory for everyone. This step
   is the product.

## Pairing notes (classic traps)

- Most Zigbee button pushes factory-pair by holding the button ~5 s until the
  LED blinks, then ZHA "Add device".
- Door sensors: pull the battery tab or long-press; verify the sensor reports
  the "door" device class, not "generic".
- mmWave presence: give it 30–60 s to settle; factory sensitivity is twitchy
  in rooms with fans or HVAC turbulence — trim after day one.
- Every battery sensor gets a battery-low entity — keep `env_alerts.yaml`'s
  battery block pointed at them.
- Dual-protocol coordinators (Zigbee + Thread/Matter): leave the Thread
  border router OFF until Matter is actually needed; keep RF noise low.
- If wifi is crowded, move ZHA to Zigbee channel 20 or 25 after first
  pairings (mesh rejoin needed).

## Consent walk-through (do this out loud, with the resident)

Before the first live day, sit with the person the system is for and cover:
what each sensor detects, what alerts go out and to whom, how the Home/Away
button arms and disarms, and how to turn the system down. Write the answers
on the wall tablet's first page if it helps. A care system the resident
doesn't understand is a surveillance system, whatever it was intended to be.
