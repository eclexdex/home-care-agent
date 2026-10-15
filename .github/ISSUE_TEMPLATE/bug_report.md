---
name: Bug report
about: Something in the care net doesn't do what the model says it should
title: "[bug] short summary here"
labels: bug
---

<!--
Before you post: this is a public repo. The covenant applies to issue
threads too. Redact everything below — see the reminder at the bottom.
-->

## What broke

What happened, when, and what you actually saw (or didn't see).

## Which piece is involved

- [ ] `automations/health_panic.yaml`
- [ ] `automations/home_away.yaml`
- [ ] `automations/env_alerts.yaml`
- [ ] `automations/night_path.yaml`
- [ ] `automations/door_watch.yaml`
- [ ] Agent: daily digest
- [ ] Agent: urgent relay / channel
- [ ] Agent: HA connection (token, API)
- [ ] Docs / install runbook
- [ ] Not sure (say what you saw and we'll help place it)

## Home Assistant version

(e.g. 2025.10.x — find it under Settings → About)

## Steps to reproduce

1. ...
2. ...
3. ...

## What you expected

The behavior as written in `docs/CARE-NET-MODEL.md`. If the docs and the
YAML disagree, say so — that's a bug in both, and we want to hear about it.

## Redaction reminder

Please remove real names, addresses, chat ids, tokens, and personal file
paths before posting. Paste YAML with placeholders (CARE_RECIPIENT,
<chat-id>) instead of your household's real values. If you already posted
something sensitive, delete the comment and mention it here so a maintainer
can scrub the reference.
