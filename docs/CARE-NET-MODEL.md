# The Care Net Model — event, meaning, response

Every rule in this repo is a transition: something happens (event), a guard
decides whether it matters (guard), the house responds (response), and the
response means something to a human (meaning). The meaning column is the
contract. A rule that cannot state its meaning in one honest sentence is not
a rule for this repo.

## Household arming states

The whole system lives in two states, plus one override that is reachable
from both at any time. Transitions belong to the care recipient alone —
there is no remote arming, no scheduled arming, and the agent never calls
any service, so nothing outside a physical button can change state.

```
                                   HOME
                          (default; calm; door_watch is
                           deaf by design; night_path and
                           env alerts live)
                                │        ▲
              press button ─────┘        └───── press button
              (door-side)                  (door-side)
                                ▼        │
                                   AWAY
                          (armed; door_watch LIVE;
                           everything else unchanged)

        panic button ──▶ PANIC  (reachable from HOME and AWAY alike;
                                 no guard, no arming required)

        ┌─────────────────────────────────────────────────┐
        │ PANIC: siren + lights + immediate family relay  │
        │        — then the system returns to whatever    │
        │          state it was in. Panic arms nothing.   │
        └─────────────────────────────────────────────────┘
```

State helpers the automations expect: `input_boolean.home_away` (off = HOME,
on = AWAY) and `input_boolean.battery_low_sent` (housekeeping latch).

## The five automations as transitions

### 1. `health_panic` — the repo's reason for existing

- **Event:** press of the wireless panic button (by the usual chair).
- **Guard:** none. It works in HOME, in AWAY, asleep, awake, internet up or
  down. A cry for help is never gated.
- **Response:** siren sounds, lights come on, and an immediate alert goes to
  the care team's phones over the family channel.
- **Meaning:** "I need help, now" reaches real humans in seconds. This is
  the plain, life-saving core; every other file exists to keep it reliable.

### 2. `home_away` — the resident owns the mode

- **Event:** press of the door-side button.
- **Guard:** a human pressed it. Nothing else in the system can toggle the
  state — not the agent, not a schedule, not a remote app the vendor added.
- **Response:** `input_boolean.home_away` flips between HOME and AWAY,
  gating the rest of the logic.
- **Meaning:** the care recipient controls what is armed, simply, locally,
  with a physical button. Dignity is architectural here, not aspirational.

### 3. `env_alerts` — the house watches the house

- **Event:** temperature crosses the freeze threshold (below 40°F) or the
  heat threshold (above 85°F — a heat failure in summer is its own danger);
  a watched `*_battery` entity goes `unavailable`. Watched: panic button,
  home/away button, front door, back door, presence, temp sensor.
- **Guard:** freeze/heat thresholds configured by the family. Battery:
  fires only while `input_boolean.battery_low_sent` is off, then latches it —
  once per event, never nagging. Smoke/CO is a commented placeholder pending
  hardware, honestly labeled as not yet in the package.
- **Response:** freeze and heat-failure are Urgent class — relayed to the
  family immediately. Battery-low is a housekeeping note, once, quietly.
- **Meaning:** a heat failure in winter is a genuine health risk — pipes and
  people. The battery note exists so the safety net never dies silently from
  a dead cell.

### 4. `night_path` — light, not logs

- **Event:** mmWave presence in shared rooms during the night hours. Also:
  no presence movement for more than two hours during the day.
- **Guard:** night-hours window for the lighting. The idle check-in is the
  *only* rule in the whole system that reacts to a non-event, and only at
  the single configured threshold — see "Silence is not data" below.
- **Response:** a low, warm light path on. For the idle case: a gentle
  "just checking in — call or knock" nudge to the care team. Nothing is
  logged, exported, or kept.
- **Meaning:** a 3 a.m. trip to the kitchen should not end in a fall. The
  nudge is a care affordance — an invitation for a human voice — not a
  movement log with a nicer name.

### 5. `door_watch` — deaf unless you armed it

- **Event:** a door or window contact sensor opens.
- **Guard:** `input_boolean.home_away` is ON — the resident armed AWAY. In
  HOME it is deaf by design, and no log entry, history, or counter is
  produced on the deaf side.
- **Response:** siren plus an immediate alert to the care team.
- **Meaning:** when the resident has said "I'm out," an opening door is
  news. When the resident is home, doors are nobody's business.

## The agent's cadence

The optional agent reads; it never acts (read-only token, no service
calls — see agent/README.md). Its output has exactly three rhythms:

- **Urgent** (panic, smoke, freeze, heat-failure): relayed *immediately,
  verbatim, timestamped, no editorializing*. The agent does not summarize a
  cry for help or add its opinion to it.
- **Digest**: once each morning at the configured time — system
  availability, battery status, a recap of overnight alerts, and anomalies
  worth a human phone call (the recommendation is always the phone call,
  never more surveillance).
- **Housekeeping**: once-only quiet notes (a low battery, a sensor going
  offline). If it fired, it stays said.

Anything the agent would want to say outside these three rhythms is, by
design, something it does not get to say.

## Silence is not data

State it plainly, because this is where care systems go wrong:

- **Sensor silence is never interpreted** as an absence-of-activity signal,
  a behavior change, or a health signal — with one single exception: the
  configured two-hour idle nudge. One threshold, configured in the open,
  disclosed to the care recipient, producing a phone call and no record.
- **An offline sensor is a maintenance problem, not a person problem.**
  Dead battery, dropped Zigbee join, or a moved router gets a housekeeping
  note to the family. It never becomes inference about the person inside.
- **The system may only react to events the family configured.** The YAML
  in `automations/` is the complete list of what the care net cares about.
  There is no hidden model, no aggregate, no "well, it usually doesn't do
  this."
- **An event that did not fire is not evidence of anything.** Not of sleep,
  not of health, not of mood, not of absence. Absence of a trigger is not
  data, is not stored, and may not be queried — the logbook shows what
  happened, and the agent is trusted not to audit a person's life from the
  gaps.

This section is a covenant clause, not a style preference. A pull request
that turns silence into a signal — presence "scores," activity "baselines,"
sleep "trends" — violates the model regardless of how useful it looks, and
the T-test in CONTRIBUTING.md exists to catch exactly that.
