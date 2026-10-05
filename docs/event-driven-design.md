# Event-driven race agent: Sauce × dots × MCP

## Direction

The rider's inspiration is live coding with dots while riding Zwift: preserve that fast, capable conversational intelligence, but give it persistent race awareness. Combine Sauce's local race-observation pattern with paseo-dots' scoped conversation/event bridge pattern. Use MCP for agent-facing race context and event notifications. A Paseo plugin is a candidate host adapter, not a requirement for the race engine.

This document records the requested technical direction and a proposed implementation; no live integration or host notification support has been verified.

## Source inspection

### Sauce for Zwift

Inspected upstream commit [`e35fb2a`](https://github.com/SauceLLC/sauce4zwift/tree/e35fb2a05ee610515fb1d06d39fd866996209dab):

- [README](https://github.com/SauceLLC/sauce4zwift/blob/e35fb2a05ee610515fb1d06d39fd866996209dab/README.md) describes a second-account game monitor, a local web server, REST/WebSocket APIs, and mods.
- [webserver.mjs](https://github.com/SauceLLC/sauce4zwift/blob/e35fb2a05ee610515fb1d06d39fd866996209dab/src/webserver.mjs) implements `/api/ws/events` with subscribe/unsubscribe and REST athlete, nearby, and group snapshots.
- [stats.mjs](https://github.com/SauceLLC/sauce4zwift/blob/e35fb2a05ee610515fb1d06d39fd866996209dab/src/stats.mjs) emits `athlete/self`, `nearby`, `groups`, and versioned query-reduced events, among others.
- [zwift.mjs](https://github.com/SauceLLC/sauce4zwift/blob/e35fb2a05ee610515fb1d06d39fd866996209dab/src/zwift.mjs) decodes `activePowerUp`; that is not proof that held, unused inventory is available.
- Source carries GPLv3. Prefer a separate API consumer initially; copying or deriving source requires a licensing decision. API availability does not settle all applicable service terms.

**First integration candidate:** an independently implemented read-only adapter to the rider's existing local Sauce instance, not a new implementation of the Zwift game protocol. Validate installed version, setup, visibility, terms, and payloads before promising features. Restrict the adapter to required observations; do not forward Sauce's arbitrary RPC or game-control surfaces to an agent.

### paseo-dots

Inspected local checkout commit [`9a87ddb`](https://github.com/q5m-ai/paseo-dots/tree/9a87ddbe85e1e3895475d34cacafa3e2067d9e6d):

- [README](https://github.com/q5m-ai/paseo-dots/blob/9a87ddbe85e1e3895475d34cacafa3e2067d9e6d/README.md) describes borrowed authenticated Paseo connections, exact scoped targets, a private local socket, bounded watches, and one-use action grants.
- [EventInbox](https://github.com/q5m-ai/paseo-dots/blob/9a87ddbe85e1e3895475d34cacafa3e2067d9e6d/server/inbox.ts) provides deduplication, cursors, bounded history, gap reporting, and immediate wake on events.
- [LATENCY.md](https://github.com/q5m-ai/paseo-dots/blob/9a87ddbe85e1e3895475d34cacafa3e2067d9e6d/LATENCY.md) reports approximately 4.7–6.0 seconds for measured local command-to-answer probes, but explicitly says near-realtime end-to-end voice delivery remains unmet. Its parent/voice relay is a separate boundary; these figures are not voice latency guarantees.

Reuse the **pattern**, not the assumption that the existing plugin is a race-radio runtime. A plugin-owned timeline row or completed coding-agent turn is not automatically an audible voice event. Preserve consent, scope, expiry, and uncertain-send handling; do not repurpose existing grants or silently extend an approved watch.

## Proposed topology

```text
Zwift → existing Sauce monitor
                   │ local read-only REST + WebSocket
                   ▼
          Team Car race engine
          ├─ freshest normalized state
          ├─ teammate / route / plan context
          ├─ derived events + bounded event log
          └─ priority / expiry / cooldown policy
                   │
                   ▼
          Team Car MCP server
          ├─ snapshot / event / plan tools
          ├─ subscribable race resources
          └─ resource-update notifications
                   │
                   ▼
          session-owning host adapter
          ├─ consumes notifications immediately
          ├─ fetches compact changes since cursor
          ├─ updates persistent voice context
          ├─ requests or suppresses proactive responses
          └─ arbitrates interruption and playback
                   │
                   ▼
          persistent realtime voice session ↔ rider
                   │
                   └─ optional bounded tactical analyst
                      for slower, deeper reasoning
```

Keep raw telemetry in the engine, not in the model conversation. Feed compact semantic changes and let the model query details. The same engine supports synthetic replay, an MCP consumer, and an optional Paseo plugin without depending on a specific host's UI.

## What “MCP events” means here

Use standard MCP resources and subscription/update notifications where the chosen protocol version and client support them. Proposed resources include `teamcar://race/current`, `teamcar://race/events`, and `teamcar://race/plan`. A resource-update notification signals changed content; it is not an arbitrary race-event body and does not inherently start an agent turn or speak.

Proposed read tools: `get_race_snapshot`, `get_race_events(afterCursor)`, `get_race_plan`, `get_teammate_status`, and `get_upcoming_landmarks`. Event fetches return cursor, oldest retained cursor, race/engine epoch, gap status, evidence, freshness, and expiry. If history is lost, fetch a snapshot and establish a new baseline rather than narrating an incomplete sequence.

The host must prove that it can receive notifications while the rider is silent and directly initiate voice responses. Tool discovery alone is insufficient. Do not use `notifications/tools/list_changed` as a race-event bus, or assume MCP sampling is supported or an unsolicited voice-output channel.

If the selected voice host cannot subscribe, implement an explicit adapter to the engine's event stream or a bounded cursor-based long poll that wakes immediately on updates. That is a declared transport alternative, not a claim that unsupported MCP push works. Verify current MCP and host APIs before implementation.

## Fast and smart: two lanes

### Hot lane — race radio

One warm realtime voice session owns conversation and immediate calls. It has the plan and a small current race picture already in context. Local event detection and scheduling remove needless model/tool round trips. A significant change supplies an evidence-backed event; the voice model can explain and recommend without launching a new general-purpose worker.

Straightforward, time-critical reminders can use a validated prewritten call when appropriate. Tactical ambiguity still deserves reasoning; do not turn the product into a collection of canned threshold alerts.

### Deliberation lane — optional tactical analyst

A persistent or bounded capable agent reasons about complex selections, competing team objectives, and plan revisions. It receives a versioned snapshot and returns structured advice tied to that snapshot and an expiry. It does not block immediate radio and does not own playback. Advice arriving after the situation changes is discarded or re-evaluated, never read out blindly.

A Paseo-hosted agent is a candidate for this lane and for pre-race planning/debrief. Whether one realtime model is sufficient should be measured before introducing a second-model dependency.

## Turn and event arbitration

- Explicit race-session start/stop; a standing authorization defines what proactive race calls are allowed during that session.
- Deduplicate by event ID; coalesce related updates and cap the queue. Latest snapshots supersede older snapshots, but decisive events retain bounded history.
- Do not send a coding-agent prompt for every telemetry frame.
- Rider speech interrupts audio. Nonurgent events wait; urgent events are rechecked before an appropriate short call.
- One owner schedules responses. No overlapping voice responses from independent workers.
- Carry race identity, engine epoch, state cursor, plan version, confidence, and expiry through detection, reasoning, and playback.
- On reconnect, resubscribe and rebaseline. Suppress obsolete notifications and stale tactical output.
- Keep source/API errors separate from race facts. Unknown teammate position is not proof they were dropped.

## Performance experiment

Instrument source observation/receipt, event detection, notification delivery, model request, first text/audio, actual playout, and barge-in stop. Use synchronized or local monotonic timing as appropriate; do not subtract unrelated machine clocks.

Initial **hypothesis**, not a promise: aim for under two seconds at p95 from receipt of a decisive event to first audible hot-lane call on the pilot setup. Also measure source age so a fast delivery of old telemetry cannot pass. Establish the supported audio/host setup and final acceptance target from evidence. Track model/tool costs and stale-call rate alongside latency.

## First vertical slice

1. Synthetic Sauce-shaped observations → normalized snapshot + one derived group-gap event.
2. MCP snapshot/event tools and resource subscription notifications, exercised by a controlled host client.
3. Persistent voice session that speaks while the rider is silent, answers “what changed?”, and supports immediate barge-in.
4. Replay a reversal before playback and prove the expired recommendation is suppressed.
5. Attach a read-only live Sauce adapter only after the synthetic path is correct.

This tests the real differentiator—event-to-voice intelligence—before investing in a complete overlay, desktop shell, or full tactical system.
