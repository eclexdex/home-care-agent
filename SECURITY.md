# Security

One page. The honest version of what this care net protects, what it can
lose, and what to do about it.

## Posture: local-first

The safety logic — panic, arming, env alerts, night path, door watch — runs
entirely on the local Home Assistant box. **Cut the internet and the safety
net keeps working:** the siren sounds, the lights come on, the house still
reacts. What degrades is remote delivery (relaying alerts to phones over
external channels). That failure mode is known and disclosed, not hidden.
The optional agent is read-only and adds no control path at all.

## Token scoping

The agent runs on a long-lived access token minted by a **dedicated,
non-admin, scoped HA user**, stored outside the repo with mode 600, never in
chat, commits, or logs.

**What a leaked read token CAN do:** read care entity states (on/off, open/
closed, temperature, battery levels) and the logbook of state changes.

**What it CANNOT do:** control devices, call services, edit or disable
automations, change notifications, or touch the arming state — because the
account it belongs to is non-admin and scoped. A leaked token makes the
care net *readable*, never *weaponizable*.

## What a leaked token still leaks — and mitigations

Read honestly: care-entity states and logbook history are **presence-ish
data**. Somewhere in that stream a patient reader could guess a routine.
That's why the token is scoped and the agent never writes anything back.

Mitigations that matter:

- **Rotation is a switch, not a project:** deactivate the dedicated user in
  HA and the token is dead immediately. Re-mint under a fresh user.
- **The digest channel is separate:** knowing the token does not put a
  message on the family channel, and knowing the channel does not read the
  house. Two secrets, one breach at a time.
- The agent itself keeps no ledger: it summarizes and forgets, so a leaked
  agent context is not an archive.

## Physical and radio threats

Zigbee devices cannot join the network on their own. Joining requires
**opening the pairing window on the local coordinator**, which requires
physical or local-console access to the box. An unpaired device sitting
outside the house cannot join the care net. Practical discipline: pair
devices at install time, don't leave the pairing window open, and unpair
anything you can't account for. The Zigbee network key lives on your local
coordinator, not on a vendor's server.

## Disclosure

Report suspected vulnerabilities to **SECURITY_CONTACT@example.invalid**.
That address is a placeholder — **every fork must replace it with a real,
monitored contact before publishing the fork**, or the disclosure path is
broken on day one. If your fork handles sensitive reports, add an encryption
key to this file. We'd rather hear quietly than learn publicly.

## Out of scope

- **Anyone already inside your LAN with admin access.** At that point the
  fight isn't digital; lock your boxes and your accounts, and treat this
  repo's local-first design as damage limitation.
- Physical tampering with devices inside the home by someone with room
  access (unplugging is, by the covenant, always allowed).
- Firmware exploits in third-party hardware — report them to the hardware
  vendor; we'll patch configs and warn the community.
- Social engineering of the care team ("it's your bank, read me the
  code") — that's a human problem with a passphrase answer, not a software
  patch.

If something here reads as wrong for your household, that's a feature of
open source: change it, and tell us why.
