## What changed

One or two sentences: what, and why it makes the care net safer or more
honest. Link an issue if there is one.

## What you tested

What flow you ran, on what hardware, and what you observed. If the change
touches the panic path, say explicitly what you tested and how.

## Checklist

- [ ] Works as tested — I ran the changed flow end-to-end (describe above;
      untested is fine to say out loud, it just means the PR stays a draft)
- [ ] Placeholders kept — no real names, towns, phone numbers, chat ids,
      token values, or personal file paths anywhere in the diff
- [ ] Privacy Impact Note filled in — required for anything touching
      sensing or new observation (see CONTRIBUTING.md)
- [ ] Covenant intact — care not surveillance, local-first, no health
      inference, alerts only over family-controlled channels

<!--
A reviewer will re-run the T-test on your own note. Answering it honestly
up front is the fastest path to merge — an honest "this needs a rethink"
is a welcome PR, not a rejected one.
-->
