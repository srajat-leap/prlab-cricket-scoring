# cricket-scoring (hop 1)

Scoring engine. **1 hop** from `cricket-protocol`. Publishes `ScoreSnapshot` for **hop 2**.

This service is the only interpreter of:

- whether extras make a delivery illegal
- whether `umpire_confirmed` means the wicket counts
- the public `last_event.display` labels broadcast animates

If protocol changes field *meaning* without changing types, this repo's tests can stay green while the scorecard is wrong.

## Contract with hop 2

`GET /matches/{id}/score` returns `ScoreSnapshot` only. Do not add `raw_ball`, `extras`, `umpire_confirmed`, or a nested `match` pack that re-exports them. Broadcast will start walking the chain.

## Trap branches

`trap/leak-raw-ball` — attaches the original `BallEvent` as `raw_ball` "for support tooling". Scoring tests are updated and pass. Broadcast can then couple to protocol (2 hops) without this repo noticing.

`trap/publish-match-pack` — publishes `match.innings.latest_over.latest_delivery` so the graphics truck can walk to `wicket.umpire_confirmed` instead of trusting `last_event`. Overlay helper and tests stay green. Law of Demeter is gone; hop-2 can read hop-0 through four strangers.

## Develop

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -e ../cricket-protocol -e ".[dev]"
pytest
uvicorn scoring.app:app --port 8000
```
