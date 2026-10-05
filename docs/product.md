# Product brief

## North star

Make an amateur Zwift racer feel supported by a professional team car: someone understands the race, knows the rider and teammates, remembers the strategy, and speaks at the right moment.

The interface borrows the simplicity of a realtime voice conversation, not any third-party branding or assets. Race intelligence is the product; the animated dots are its low-distraction surface.

## Race lifecycle

### Before

Import or enter event details, route, roster, rider capabilities, and a race plan. Confirm contested or missing information. Brief the rider on decisive sectors, expected selections, team roles, finishing strategy, and fallback plans. Test microphone, output device, and telemetry freshness.

### During

Maintain a compact live race picture. Answer “where's our sprinter?”, “what's next?”, “can I let this go?”, and “how far to the climb?” when evidence supports an answer. Proactively call significant selections, credible attacks, dangerous gaps, plan deviations, and confirmed power-up opportunities.

Use short messages: **situation → action → reason**, often just one sentence. Example: “The front group is pulling away. Close now if you can; this is the selection we planned to follow.” Recommendations depend on actual rider capability, course position, confidence, and plan.

### After

Summarize the key moments and which recommendations were given, with evidence and timestamps. Compare intent with execution without pretending to establish causality. Let the rider correct interpretations before carrying lessons into future plans.

## Voice behavior

- Rider speech interrupts playback immediately; abandoned calls are not blindly resumed.
- Separate conversational replies from proactive radio calls.
- Priority: connectivity/safety notices, decisive tactical calls, plan reminders, optional status.
- Coalesce related changes; apply cooldowns and expire advice when its window closes.
- Offer adjustable verbosity, proactive-call level, captions, and a reliable mute.
- Do not flood a rider during a high-effort interval. Allow urgent concise exceptions.
- Say “I can't see that” rather than infer private teammate intent or missing inventory.
- No medical coaching or encouragement to ignore distress; the rider can stop at any time.

## Teammate knowledge

Explicit roster with stable rider identifiers, display names, roles, rider-entered capabilities, and race-specific responsibilities. Distinguish **agreed plan** from **observed behavior** and **inferred intent**. A teammate moving up is not proof of an attack or team instruction.

MVP observes roster members only through validated available telemetry. Private teammate messages, shared plans, or audio require separate opt-in; Team Car does not speak on behalf of other riders.

## Boundaries

Not an automated rider, guaranteed winning strategy, replacement for Zwift's UI, or an official Zwift integration. Do not assume permission to access undocumented interfaces, redistribute game data, or reuse another product's code. Investigate supported integrations and applicable terms first.

## Success measures

- Correct, actionable calls on labeled replay scenarios; track false positives and missed decisive events.
- End-to-end event-to-audio latency measured at p50/p95, with component breakdown.
- No tactical advice from expired state; explicit degraded mode under outages.
- Successful barge-in, mute, and recovery across supported audio devices.
- Rider-rated usefulness, distraction, and trust—not number of words spoken.

Quantitative targets will be set after telemetry and voice feasibility spikes, before MVP acceptance.
