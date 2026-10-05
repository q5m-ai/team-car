# Proposed architecture

Status: design hypotheses, not verified integration capabilities.

```text
Validated Zwift source / recorded replay
                 ↓
        local read-only bridge
                 ↓
   normalized timestamped race state
                 ↓
   event detection + tactical policy ← rider / roster / race plan
                 ↓
   prioritized, expiring radio intents
                 ↓
     realtime voice session ↔ rider
                 ↓
       dots UI + captions + controls
```

## Data access is the first gate

Investigate available Zwift integrations and, where permitted, existing companion/telemetry approaches. Do not assume Sauce exposes a reusable API or that a public Zwift API provides live race data. Record concrete access methods, licensing/terms, supported operating systems, latency, and field availability before selecting an adapter.

Build a capability matrix for: self power/cadence/heart rate, rider IDs, event membership, course position/distance, nearby riders versus full field, gaps, group membership, route landmarks, finish distance, power-up inventory, and power-up use. Unavailable fields stay unavailable. Nearby-only visibility cannot establish full-race standings.

Screen/OCR access is not a default fallback: it would need separate reliability, privacy, and terms evaluation. Keep synthetic replay useful even if live access is blocked.

## Suggested implementation direction

- TypeScript throughout the first vertical slice, subject to bridge feasibility.
- Browser UI with a realtime voice provider adapter; evaluate OpenAI Realtime for the first implementation rather than hard-code transport assumptions.
- Server issues short-lived client session credentials; long-lived provider keys never enter the browser or repository.
- Local bridge handles machine-specific telemetry access and sends normalized, authenticated, read-only updates. Bind locally by default; restrict origins and avoid exposing a LAN control endpoint.
- Start with one process/session and in-memory state. No Redis or cloud database requirement for MVP.
- Provider-neutral replay fixtures and tactical policy tests independent of audio/model calls.

## State and provenance

Every observation needs source, observation timestamp, receive timestamp, and quality. Snapshots also declare scope (self/nearby/full field), available capabilities, race/session identity, and freshness. Normalize identifiers and units; reject duplicate/out-of-order updates and reset state between races.

Separate layers:

1. **Observed:** watts, positions, reported gaps, confirmed inventory.
2. **Derived:** estimated groups, gap trends, candidate selections or attacks.
3. **Advised:** a recommendation relative to plan, capability, confidence, and current window.

Derived events carry evidence, confidence, creation time, expiry, and a stable deduplication key. Race plans include route landmarks, intended actions, effort constraints, and fallback branches. Version rider edits so an obsolete plan does not drive new calls.

## Division of responsibility

Deterministic code owns freshness, unit conversion, gap trends, event detection, cooldowns, prioritization, and whether a tactical call is still valid. Tune event thresholds with labeled fixtures; a transient power spike is not automatically an attack.

The model explains valid structured facts and advice, answers questions, and handles conversational nuance. It must not invent missing positions, power-ups, teammate intent, or physical capacity. Structured tool results should report unknown/stale fields explicitly. Rider names and external metadata are untrusted data, not instructions.

Before playback, revalidate the call's race identity, plan version, evidence freshness, and expiry. Discard advice that has become irrelevant. Keep proactive calls concise and interruptible.

## Voice and UI

Investigate WebRTC browser audio, supported session authorization, server-side orchestration, and barge-in with the selected provider. Measure real latency rather than promise instantaneous response. The UI shows listening/speaking/muted/disconnected states, captions, telemetry age, and key facts without requiring interaction during effort.

Audio failure must not stop state ingestion. Telemetry failure must suppress tactical advice while retaining basic conversation and a clear degradation notice.

## Privacy and security

Default to no retained raw audio. Store race recordings only with explicit opt-in, clear retention, deletion, and redaction controls. Keep credentials out of fixtures, logs, Git, and client bundles. Redact health and identity data from diagnostic exports. Future shared-team sessions require consent and access control, not just a join URL.

## Open decisions

- Which permitted source exposes enough timely race state?
- Does useful coverage require a local desktop app rather than a browser plus bridge?
- Which route/landmark source is maintainable and licensed for use?
- Can current power-up inventory actually be observed?
- What latency and cost are acceptable for a full race?
- How should rider limits and plan deviations translate into conservative tactical policy?
