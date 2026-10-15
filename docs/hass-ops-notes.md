# Home Assistant API ops notes (generalized lessons)

Operating rules learned the hard way, generalized. No site addresses,
credential paths, or deployment detail here — those belong in your own
credential store.

## Standing rules

- The HA long-lived token **never enters chat** and never enters a repo.
  It lives only in a local credential file (chmod 600).
- Reads (sensor/device states, automation and scene lists) are safe by
  default. **Writes** — create/modify/delete automations, activate scenes,
  control devices — require the human operator's approval; on install day
  the operator being present in the house is the approval.
- Paste-verified changes only: after pasting a YAML automation, run
  *Check configuration* before enabling it, then test the panic flow
  end-to-end with the resident.

## Reachability ladder

1. **REST API** — `/api/states`, `/api/services/...` with
   `Authorization: Bearer <token>`. First choice for everything an agent
   does.
2. **WebSocket API** — for event subscriptions and bulk reads when polling
   REST is too chatty.
3. If the dashboard loads but the API returns 401, the token is
   wrong/expired/revoked — mint a new one. Do not debug the network.

## WebSocket API pitfalls

- Every response carries a `success` flag — check it. Errors come back as
  `{"type":"result","success":false,...}` inside a *successful* connection,
  not as HTTP errors.
- Message names: `get_states`, `get_services`, `call_service`,
  `subscribe_events`.
- The service payload key is `service_data` (not `data`) in WS frames.
- Large frames arrive fragmented — keep reading until the response whose
  `id` matches your request.

## Long-lived token reality

- Tokens inherit the **creating account's** permission level; a token minted
  by an admin is an admin key. Create a scoped, non-admin user for agent
  access.
- Tokens are shown once at creation. Lost = re-mint; there is no reveal.
- A care agent only ever needs read endpoints (`/api/states`,
  `/api/logbook`). If your agent design needs service calls, re-examine the
  design — this project's answer is "let HA automations act, let the agent
  notify".

## Care-posture reminder

Write actions exist to keep the safety net working. Any new sensor, camera
view, or automation that could observe the resident beyond the agreed safety
affordances is a family covenant decision — not a routine ops change, and
never an agent's call.
