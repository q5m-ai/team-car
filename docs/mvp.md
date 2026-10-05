# MVP delivery plan

## 1. Telemetry feasibility — go/no-go

Document a permitted live-data path, target OS, setup requirements, field/capability matrix, and measured freshness. Prove self telemetry plus enough surrounding race context to support useful calls. If full-field or power-up data is unavailable, narrow claims rather than fabricate substitutes.

**Exit:** reproducible, read-only probe and sanitized sample; explicit decision about supported MVP features. If access is blocked, continue only as a replay prototype, not a live product.

## 2. Replay-first race intelligence

Define normalized observations, rider roster, race plan, derived events, and expiring radio intents. Build a deterministic replay harness with labeled scenarios:

- Decisive front selection and a teammate making/missing it.
- Growing gap versus noisy/transient changes.
- Upcoming planned sector and deviation from intended effort.
- Known power-up opportunity versus unavailable inventory.
- Partial field visibility, stale data, out-of-order updates, disconnect/reconnect.
- Changed race plan, race restart, duplicate events, expired recommendations.

**Exit:** repeatable tests demonstrate correct event evidence, suppression, priority, and degraded behavior without a model.

## 3. Voice vertical slice

Add animated dots, captions, microphone controls, push-to-talk, mute, provider session setup, and race-state query tools. Feed synthetic/replay state through the same contract as a future live bridge.

**Exit:** rider can ask about the race and receive grounded answers; one timely proactive call works; interruption, expiry, and unavailable-data responses are tested. Measure latency and session cost; agree acceptance targets before live racing.

## 4. Live single-rider pilot

Connect the validated bridge. Add manual plan/roster setup, pre-race briefing, bounded tactical rules, freshness display, and opt-in diagnostic recording. Test on a non-critical ride before a race.

**Exit:** complete a pilot with evidence for call usefulness, false alerts, missed events, latency, recovery, and cost. No deployment or external exposure is implied by this milestone.

## 5. Debrief and refinement

Provide event timeline, recommendation history, and rider corrections. Improve thresholds and interaction from replayable evidence. Add automated regression scenarios for every accepted failure correction.

## Later—not MVP

- Shared multi-rider team radio and coordinated roles.
- Automated roster/calendar/training-service integrations.
- Persisted personalization across events and seasons.
- Rich tactical simulation and opponent history.
- Mobile-first or packaged desktop distribution.
- Public hosting, billing, and multi-tenant operations.

## Definition of done

No unsupported claims about telemetry. No secrets in client or logs. No advice from expired evidence. Deterministic tactical-policy tests plus voice interaction checks. Document supported setup, known blind spots, operating cost, and immediate stop/mute controls.
