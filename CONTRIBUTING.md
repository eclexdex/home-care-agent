# Contributing

Start with the covenant in the README. Every rule below exists to keep this
repo's one promise true: care, not surveillance.

## Adding a new automation

1. **Copy the closest existing file in `automations/` and keep its header
   discipline.** Every automation opens with a `CHANGE THESE` block listing
   exactly what an installer must replace — entity ids, thresholds, the
   family channel, placeholders like CARE_TEAM_NOTIFY and <chat-id>. New
   automations must lead with the same block and list *every* value a
   household has to touch. A file somebody can't fill in without reading the
   source is not paste-ready.
2. **Plain YAML on purpose.** These files are meant to be readable by a
   careful human at 11 p.m. on install day. Keep the comments, keep
   everything visible, don't fold config into anchors or includes. **No YAML
   linters are enforced here** — no reformat-only PRs, no style CI. If it's
   clearer, it's correct.
3. **State the transition.** Each automation goes into `docs/CARE-NET-MODEL.md`
   as event → guard → response → meaning. If the meaning is hard to write,
   that's information.

## The Privacy Impact Note

**Every PR that touches sensing** — a new sensor, a new reading of an
existing sensor, a new rule that turns sensor events into anything (a
number, a trend, a score, a longer history, a new place anything is stored
or sent) — **must include a Privacy Impact Note** in the PR description:

- What new signal is read, and how finely / how long.
- What new output exists, who sees it, and where it goes.
- The T-test, answered honestly (below).

The template in `.github/ISSUE_TEMPLATE/feature_request.md` has the fields.
A sensing PR without the note stays a draft until the note exists.

## The T-test

> **"Does this new sensor or rule reveal something the person did not
> choose to share?"**

If the answer is yes, **it does not ship as-is.** There are three honest
ways forward, in order of preference:

1. **Local-only** — the observation informs a response inside the house and
   is never recorded, aggregated, or exported.
2. **Threshold events with consent** — replace inference with the fewest
   possible configured events, disclosed to the care recipient in the
   one-pager's language, agreed before install.
3. **Don't build it** — most "well, we could also detect…" ideas die here,
   and that's the feature.

Watch for the sneaky version: new observation dressed as *analysis* of old
observation. Presence data turned into a sleep score is a new observation
of a person, not a new reading of a sensor. Silence is not data — the rule
in `docs/CARE-NET-MODEL.md` applies to your clever idea too.

## Proposing BOM and anti-BOM changes

The reference cart lives in `docs/how-to-install.md`.

- **BOM changes** (adding hardware to the cart) must earn their place: say
  which failure mode in the model the part closes, and label any cost as an
  approximate range needing verification.
- **Anti-BOM changes** (making the care net do its job with fewer parts — a
  cheaper sensor class, folding two roles into one device, dropping a part
  the pack no longer needs) are welcome and taken seriously, because a net
  more families can afford is a safer net. They need the same proof as any
  change: show that every transition in the model still fires, and say what
  you tested and what got weaker. Fewer parts with a weaker panic path is a
  non-starter; the panic flow is the floor.

## Test-before-trust

**An automation that has not been tested with the resident is a draft, not
a contribution.** The panic flow, tested *with the care recipient present*
— they press the button, the siren sounds, the phones buzz, everybody
agrees it worked — is the actual product. Everything else in this repo
supports that one moment. Say in your PR what you tested, on what hardware,
with whom present. Untested contributions are fine to open; they stay
drafts until someone with a house runs them.

## General ground rules

- Placeholders stay placeholders: no real names, towns, phone numbers, chat
  ids, token values, or personal paths in code, docs, examples, or commit
  messages.
- No vendor evangelism: device classes and protocols (Zigbee 3.0 button,
  mmWave presence) are fine; a PR that quietly makes one SKU mandatory is
  not.
- Push back kindly, take correction well, and remember the person at the
  center of all this is a person, not a dashboard.
