# Home Assistant — REST/WebSocket cheat sheet (care agent)

For the care agent only. The agent is read-only: it never calls the services
API. Acting belongs to HA automations.

## Long-lived token

- Creation path: profile → Security → Long-lived access token.
- Mint it from a dedicated NON-admin scoped user (not the admin account) whose
  scope covers reading states, logbook, and history only.
- The token is shown once, at creation. Lost = re-mint. There is no way to
  recover the displayed value.
- Store it outside the repo, chmod 600. Never in chat, version control, or logs.

## Core REST endpoints

All requests carry the header `Authorization: Bearer $HASS_TOKEN`.

| Endpoint | Purpose |
|---|---|
| `GET /api/` | Health check. `200` + `{"message": "API running."}` = alive. |
| `GET /api/states` | Every entity state. Filter to the care set before use. |
| `GET /api/states/<entity_id>` | One entity state. |
| `GET /api/logbook/<timestamp>` | Events after a timestamp; supports an entity filter. |
| `GET /api/history/period/<start_time>` | History over a period; filter by entity. |

Example curl lines (using `$HASS_URL` / `$HASS_TOKEN` from the environment):

```
# health check
curl -s -H "Authorization: Bearer $HASS_TOKEN" "$HASS_URL/api/"

# all states
curl -s -H "Authorization: Bearer $HASS_TOKEN" "$HASS_URL/api/states"

# one entity
curl -s -H "Authorization: Bearer $HASS_TOKEN" "$HASS_URL/api/states/sensor.living_room_temperature"

# logbook after a timestamp, for one entity (ISO 8601 local time)
curl -s -H "Authorization: Bearer $HASS_TOKEN" "$HASS_URL/api/logbook/$SINCE_TS?entity=sensor.living_room_temperature"

# history for one entity over a period
curl -s -H "Authorization: Bearer $HASS_TOKEN" "$HASS_URL/api/history/period/$START?filter_entity_id=sensor.living_room_temperature&end_time=$END"
```

## WebSocket API

- Endpoint: `ws://<host>:8123/api/websocket` (or `wss://` behind TLS).
- First frame (auth): `{"type":"auth","api_key":"$HASS_TOKEN"}`.
  - Success: `{"type":"auth_ok"}`.
  - Failure: `{"type":"auth_invalid"}` — treat like a 401: the token is wrong
    or revoked. Stop and re-mint; do not debug the network.
- After `auth_ok`, send command frames, each with its own `id`:
  - `{"id":1,"type":"get_states"}` — current states.
  - `{"id":2,"type":"subscribe_events","event_type":"state_changed"}` — push
    state changes instead of polling.
  - `{"id":3,"type":"get_services"}` — the service catalog (read-only
    reference; the agent never calls services).
- Every response frame carries a `success` flag. Errors arrive as
  `{"type":"result","success":false,"message":"..."}` — not as HTTP errors.
  Check `success` on every id-matched response.
- Command names are `get_states` / `get_services` / `subscribe_events` — not
  REST-style verbs.
- The service payload key is `service_data`, not `data` (relevant only if you
  read a service definition; the agent never sends one).
- Large frames arrive fragmented — keep reading until the response whose `id`
  matches the command you sent, then stop.

## Rate / practical limits

- The history API can be heavy. Always filter by entity id and by period. Never
  pull unfiltered history.
- Avoid tight polling loops. Prefer event subscription over WebSocket
  (`subscribe_events`), or a 1–5 minute poll cadence at most.
- Logbook and history take timestamps. Keep the window small.

## Common pitfalls

- **Numeric vs string.** States arrive as strings. Cast defensively; a failed
  cast is "unknown," not an error.
- **`unavailable` vs `unknown`.** Different meanings, both legitimate, neither
  an error. `unavailable` = off the network; `unknown` = no reading. Neither
  is a freeze or heat event.
- **Timezone.** HA returns HA-local timestamps with an offset embedded. Do not
  assume UTC. Convert only when composing a message.
- **Automations pause across restarts.** After an HA update, check each
  automation's `state`. An automation that was on can come back paused or
  `off` after an HAOS update. Verify before assuming the safety net is armed.
- **State restoration gotchas.** Right after a restart, some entities report
  `unavailable` until the sensor re-syncs. Do not read the first post-restart
  state as truth.
- **401 means token.** A `401` with a working dashboard is an expired or
  revoked token. Re-mint. Do not debug the network.
