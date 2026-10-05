# Team Car

**Your AI directeur sportif for Zwift.**

A ChatGPT-style, voice-first realtime race companion: the awareness of Sauce for Zwift, delivered like a professional Grand Tour team car. Keep your eyes on the race; Team Car watches the situation, remembers the plan, and tells you what matters.

> “Two teammates made the front selection. You're six seconds behind with three riders. Close before the climb, then sit in. Save the aero power-up for the finish—if you still have it.”

This is a product/design seed, not a working Zwift integration. The quoted call is an illustrative scenario, not a claim about currently available telemetry.

## The experience

- **Race radio:** natural, interruptible two-way audio with a minimal animated dots/orb interface, captions, mute, and push-to-talk fallback.
- **Race awareness:** live position, groups, gaps, selections, attacks, and decisive moments—with source freshness and uncertainty made explicit.
- **The race plan:** pre-race briefing, route landmarks, target efforts, contingencies, and timely reminders when reality diverges from the plan.
- **Tactical advice:** explain what changed, recommend one useful action, and avoid narrating every watt.
- **Teammates:** recognize the roster, understand roles and goals, track observable race positions, and remember who should be protected or followed.
- **Power-ups:** advise about known inventory and relevant opportunities; never pretend to know an unobserved power-up or guarantee a random award.
- **Debrief:** review decisive events, plan deviations, and outcomes after the finish.

## Product principles

1. **Sound like a team car, not a sports commentator.** Calm, concise, actionable. Urgency is earned.
2. **Facts before inference.** Distinguish observed events, inferred tactics, and unknowns.
3. **Silence is a feature.** Prioritize decisive calls, suppress duplicates, and respect rider workload.
4. **The rider stays in control.** Advice only; no automated game control. Immediate mute and voice interruption.
5. **No invented race state.** Stale or missing data means degraded advice, not confident guesses.
6. **Personalize deliberately.** Fitness, race plan, teammate roles, and preferences are explicit inputs, not assumptions.

## Initial scope

Single-rider, desktop/browser voice companion with a local telemetry bridge. Begin with deterministic recorded/synthetic race replay; validate actual Zwift data access before promising live capabilities. Shared team radio and multi-rider coordination come later.

See [product brief](docs/product.md), [architecture](docs/architecture.md), and [MVP plan](docs/mvp.md).

## Status

Planning/bootstrap. No app, telemetry provider, audio session, or deployment exists yet. Repository creation does not authorize game-data scraping, credential provisioning, or hosting.
