# Urgent alert relays — EXAMPLE

> EXAMPLE output — all data fictional, generated to show tone and structure.
> `[care recipient]` is the placeholder for the person being cared for;
> nothing here refers to a real person or household. Urgent relays go out
> immediately, verbatim, with a timestamp — the agent does not editorialize
> or diagnose.

---

## 1. Panic button

> 🚨 PANIC — [care recipient] pressed the panic button at 14:32.
> Siren + lights on. Call now.

Why it reads that way: this is the relay, not a summary. The timestamp comes
first so the care team can order events if several alerts stack up. It states
only what the system knows — a press happened, the siren and lights fired —
and never guesses at *why* (no "they fell," no "they seem distressed"),
because the button's meaning belongs to the person who pressed it. The one
imperative — "Call now" — is the whole required human action: voice beats a
dashboard, and a false alarm resolved in ninety seconds is a price worth
paying forever.

## 2. Freeze / heat failure

> 🥶 FREEZE — indoor temperature fell to 38°F at 03:12 (hall sensor).
> Heat may have failed. Check the heat — and check on [care recipient].

Why it reads that way: the reading is the fact (below the configured 40°F
freeze threshold); "heat may have failed" is
deliberately hedged because the sensor reports temperature, not furnace
status, and the agent refuses to invent a diagnosis it can't see. Timestamp
first again, and the paired instruction names both real stakes: a heat
failure in winter is a pipes problem and a person problem in that order of
plumbing but the reverse order of urgency. At 03:12, the right human action
is a call now, not a morning digest item — which is why this class relays
immediately.

## 3. Armed-away door open

> 🚪 DOOR — front door opened at 09:47 while the system is armed Away.
> Siren active. Check on [care recipient], then check the door.

Why it reads that way: the "armed Away" state is load-bearing and appears in
the alert text because the same sensor is silent-by-design when disarmed —
the resident controls what is armed, and the alert says under which
permission it fired. It reports event, state, and siren status, and draws no
conclusion (not "they wandered off," not "possible intrusion" — a person
leaving for a walk is not an incident). The action order is intentional:
confirm the person is okay first, look at the door second.
