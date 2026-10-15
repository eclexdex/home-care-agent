---
name: Feature request
about: An improvement that keeps the care net honest
title: "[feature] short summary here"
labels: enhancement
---

<!--
Redact first: no real names, addresses, chat ids, tokens, or personal
paths in anything you paste here.
-->

## What this is for

The care problem you're solving, in plain words. Who it helps, and what
goes better or safer if we ship it.

## The change you'd like

What should happen — which event, which guard, which response. If you can
write it as `event → guard → response → meaning` (see
`docs/CARE-NET-MODEL.md`), even better.

## Privacy note

**Does this feature add a new kind of observation?**

A new sensor counts. So does a *new reading of an existing sensor* — more
history, finer granularity, a new derived signal, a new place anything gets
stored or sent. If you're unsure, answer yes and we'll look together.

- [ ] No new observation (explain in one line why)
- [ ] New observation (answer the T-test below)

**If yes — the T-test:** does this reveal something the care recipient did
not choose to share? If the honest answer is yes, say so in the open anyway
— we'd rather find the local-only or consent-shaped version of your idea
than quietly ship something that breaks the covenant.

## Why it fits the covenant

Local-first? No health inference? Alerts only over family-controlled
channels? A sentence or two on each is plenty.
