# cricket-scoring (hop 1)

Scoring engine. **1 hop** from `cricket-protocol`. Publishes `ScoreSnapshot` for **hop 2**.

This service is the only interpreter of:

- whether extras make a delivery illegal
- whether `umpire_confirmed` means the wicket counts
- the public `last_event.display` labels broadcast animates

If protocol changes field *meaning* without changing types, this repo's tests can stay green while the scorecard is wrong.

## Contract with hop 2

`GET /matches/{id}/score` returns `ScoreSnapshot` only. Do not add `raw_ball`, `extras`, or `umpire_confirmed` to that payload. Broadcast will start depending on them.

## Trap branch

`trap/leak-raw-ball` — attaches the original `BallEvent` as `raw_ball` "for support tooling". Scoring tests are updated and pass. Broadcast can then couple to protocol (2 hops) without this repo noticing.

## Develop

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -e ../cricket-protocol -e ".[dev]"
pytest
uvicorn scoring.app:app --port 8000
```

testing