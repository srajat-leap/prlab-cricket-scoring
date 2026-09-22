# Traps for single-repo review

## `trap/leak-raw-ball`

**The PR:** add optional `raw_ball` on `ScoreSnapshot` so on-call can see the original delivery. Comment it as debug-only. Update engine tests.

**What a hop-1 review usually says:** additive JSON field, documented as internal, tests updated, LGTM.

**1 hop up (protocol):** no compile break. The leak re-exports hop-0 fields that scoring was supposed to hide.

**2 hops down (broadcast):** a later UI PR can animate from `raw_ball.extras.type` and `raw_ball.wicket.umpire_confirmed`, skipping scoring's `wicket_counted` rules. Architecture review of *this* PR cannot see that consumer. It only creates the opening.

**Functional truth:** ScoreSnapshot is a firewall, not an envelope for BallEvent.

## `trap/publish-match-pack`

**The PR:** add a nested `match` pack on `ScoreSnapshot` so overlay clients can walk `match.innings.latest_over.latest_delivery` instead of parsing `last_event.display`. Add `graphic_cue()` that does that walk. Update tests.

**What a hop-1 review usually says:** structured API for the truck, Law of Demeter is "just a style nit", tests added, LGTM.

**1 hop up (protocol):** the pack re-exports `extras.type` and `umpire_confirmed`. A protocol default now flows through four objects.

**2 hops down (broadcast):** a later UI PR can animate from `snapshot.match.innings.latest_over.latest_delivery.wicket.umpire_confirmed`, skipping `wicket_counted`. That walk is the Law of Demeter break.

**Functional truth:** tell `last_event` what happened. Do not ask a delivery you reached through a match, an innings, and an over.
