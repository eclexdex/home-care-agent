# Wiring the Home Care Agent

The agent is **optional**: the five automations work without it. The agent
adds a family correspondent — urgent relays and a daily digest — on top of
whatever Home Assistant already knows.

It is deliberately runtime-agnostic: any local-first AI agent (Hermes,
OpenClaw-style runtimes, a small Python loop with an LLM, or even a dumb
cron script) can play the role defined in `SOUL.template.md`. What it needs:

1. Home Assistant with a long-lived access token (read-only)
2. One messaging channel (a family Telegram group, an email, a Matrix room —
   anything you can post to programmatically)
3. A place to run (the HA host itself, a Raspberry Pi, any always-on box)

## 1. Create a read-only-ish HA token

Home Assistant's long-lived tokens inherit the creating account's
permissions, so scope matters:

1. Create a dedicated HA user (e.g. `care-agent`) under
   *Settings → People & Accounts* **as a non-admin** — ideally a user whose
   permissions do not include device control, or use HA's per-user entity
   restrictions under *Security*.
2. With that user logged in: *profile → Security → Long-lived access token →
   Create and copy* (shown once).
3. Store it in a file **outside any repo**, e.g. `~/.config/care-agent/ha.env`,
   `chmod 600`. The file holds two entries: `HASS_URL` (the LAN URL of your
   HA instance) and `HASS_TOKEN` (the token you just copied) — write them in
   the `KEY = VALUE` dotenv style, and `export $(grep -v '#' ha.env)` or load
   them however your runtime prefers.

4. Never paste the token into chat, an issue tracker, or a screenshot.

## 2. Talk to HA (REST)

Health check:

```bash
curl -s -H "Authorization: Bearer ${HASS_TOKEN}" \
  "${HASS_URL}/api/" | head -c 200
```

Read a sensor:

```bash
curl -s -H "Authorization: Bearer ${HASS_TOKEN}" \
  "${HASS_URL}/api/states/sensor.living_room_temp" \
  | python3 -c 'import json,sys; print(json.load(sys.stdin)["state"])'
```

List all states worth digesting (filter to the care entities you configured):

```bash
curl -s -H "Authorization: Bearer ${HASS_TOKEN}" "${HASS_URL}/api/states" \
  | python3 -c '
import json, sys
for s in json.load(sys.stdin):
    if any(k in s["entity_id"] for k in
           ("panic","home_away","temp","battery","door","presence")):
        print(s["entity_id"], "=", s["state"])
'
```

The agent **never calls `/api/services/...`** — service calls are device
actions, and acting is outside the SOUL. (The automations inside HA do the
acting; see `automations/`.)

### Watching events instead of polling

For real-time relays, prefer HA's WebSocket API or, simplest of all, let HA
push to you: add a final step in each care automation calling
`notify.rest` (a tiny local webhook endpoint the agent exposes). The agent
then only handles what the family's automations explicitly emit — no extra
observation surface.

WebSocket gotchas (the usual traps):
- every frame has a `success` flag — check it; failures arrive as
  `{"type":"result","success":false,...}`, not HTTP errors;
- command names are `get_states` / `get_services` / `subscribe_events`;
- large frames can arrive fragmented — read until the response with the
  matching `id`.

## 3. Minimal relay example (Python, stdlib only)

```python
#!/usr/bin/env python3
"""Daily digest relay — the whole 'agent' if you want zero dependencies.
Cron it: 0 7 * * *  care_digest.py"""
import json, os, urllib.request

HASS_URL  = os.environ["HASS_URL"]
HASS_TOKEN = os.environ["HASS_TOKEN"]
TELEGRAM_BOT_TOKEN = os.environ["TELEGRAM_BOT_TOKEN"]
TELEGRAM_CHAT_ID   = os.environ["CARE_TEAM_TELEGRAM_CHAT_ID"]

def ha_states():
    req = urllib.request.Request(f"{HASS_URL}/api/states",
                                 headers={"Authorization": f"Bearer {HASS_TOKEN}"})
    with urllib.request.urlopen(req, timeout=10) as r:
        return json.load(r)

def build_digest(states):
    care = [s for s in states if any(k in s["entity_id"] for k in
            ("panic", "home_away", "temp", "battery", "door", "presence"))]
    lines = ["🏠 Care digest"]
    lines += [f"· {s['entity_id'].split('.')[0]}:{s['entity_id'].split('.',1)[1]}"
              f" = {s['state']}" for s in care]
    low_batt = [s for s in states if "battery" in s["entity_id"]
                and s["state"] not in ("unavailable", "unknown")
                and float(s["state"]) < 20]
    if low_batt:
        lines.append("🔋 low batteries: " + ", ".join(s["entity_id"] for s in low_batt))
    unavailable = [s["entity_id"] for s in care if s["state"] == "unavailable"]
    if unavailable:
        lines.append("⚠️ offline sensors: " + ", ".join(unavailable))
    return "\n".join(lines)

def main():
    text = build_digest(ha_states())
    req = urllib.request.Request(
        f"https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage",
        data=json.dumps({"chat_id": TELEGRAM_CHAT_ID, "text": text}).encode(),
        headers={"Content-Type": "application/json"})
    urllib.request.urlopen(req, timeout=10)

if __name__ == "__main__":
    main()
```

> Note: the one outbound call above (Telegram) is the *family-controlled
> channel* the covenant permits as the alert path. Swap it for any channel
> your family owns. If you add an LLM to write the digest in prose, run it
> locally and keep the SOUL's hard rules: redact, never post data onward.

## 4. Daily-digest pattern (with a real agent)

Feed the agent, once a day:

1. the filtered sensor snapshot (above),
2. the HA `logbook`-derived list of automation triggers for the last 24 h
   (`/api/logbook/<timestamp>?entity=...`),
3. the SOUL's voice rules,

and ask for exactly one output: the digest message. One input surface, one
output surface — that containment is what makes an LLM safe to put near a
care arrangement.

## 5. Runbook

- **Token rotated/expired:** dashboard works, API 401s → mint a new token,
  update the credential file, restart the agent. Don't debug the network.
- **Agent unreachable / HA down:** the *automations still work* — they run
  inside HA. The agent is the correspondent, not the safety net.
- **New sensor added:** add it to the digest filter. If it observes the
  resident beyond the agreed affordances, that's a family covenant decision
  first, a config change second.
