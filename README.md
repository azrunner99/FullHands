# FullHands

**A shared-tablet food running tracker and gamification system for restaurant teams.**

FullHands is a tablet app mounted next to the expo window — the place where kitchens hand off completed food to the servers and runners who deliver it to tables. Every run counts, every server is a character in the story, and the whole team sees it unfold in real time.

---

## What FullHands is

At its simplest, FullHands counts how many plates servers run from the kitchen to their tables during a shift. But the goal isn't just tracking — it's turning one of the most thankless, overlooked parts of restaurant work into something visible, celebrated, and competitive in the way it deserves to be.

The phrase "full hands in, full hands out" is drilled into every server during training. It means: never walk empty-handed. Always be moving food, dishes, drinks — you're part of the team, not just yourself. The app is named after that ethic because it's the heartbeat of what it measures.

## Who it's for

**Servers and food runners** are the protagonists. They tap their button when they run food. They see their stats grow, their tier climb, their achievements unlock. The app should make them feel like the heroes of the floor — not monitored, not micromanaged, celebrated.

**Managers and admins** use FullHands to run their teams: set rosters, configure shifts, define team goals, track trends, investigate integrity concerns, and recognize standout performers. Admin features are protected behind a PIN.

## How it's used

A single Android tablet is mounted or placed **next to the expo screen** — the kitchen-facing display where food gets "sold off" before being carried to tables. Every time a server grabs a ticket of food, they tap their button on the tablet. That's it. No phones, no logins, no passwords for servers — just walk up, tap, go.

Because it's a shared-tablet experience visible to the whole team, the app leans into the social dynamics of a restaurant floor. Celebrations are public. Leaderboards are live. When one server hits a milestone, everyone sees it.

## Core design philosophy

- **Servers are protagonists, not metrics.** Every feature should make servers feel like characters in an ongoing story.
- **Shared visibility shapes everything.** The tablet is seen by the whole team — celebrations are team events, never private notifications.
- **Never publicly shame.** No one should ever feel called out in front of their coworkers. Neutral states, never negative.
- **Respect tenure.** A 20-year lifer's profile should visibly represent those decades. New hires shouldn't look the same as veterans.
- **Admin can always override.** Restaurants are messy. Managers need to fix bad state without losing data.
- **Trust but verify.** Most people don't cheat. The system shouldn't treat them like they might, but it should catch the subtle cases when they arise.
- **Legible from three feet away.** This is not a phone app. Everything is big, high-contrast, glance-and-tap.
- **Works offline. Forever.** No internet required for any core feature.

## Current state

This is a ground-up rebuild. A previous Flutter app (food_runs_counter, BJ's-specific) established the core concept and many of the mechanics, but accumulated architectural debt that made it hard to evolve. FullHands takes the lessons learned and builds clean.

**Key differences from the predecessor:**
- Multi-restaurant from day one (admin configures brand, special run name, achievement thresholds, etc.)
- On-device LLM for generated encouragement and shift recaps (no static message list)
- Proper state machine for shift transitions (the old app's single biggest bug source)
- Tier-based progression that actually respects long tenure
- Team mode with colored team standings
- Modern game-UI aesthetic for server identity and celebrations
- USB backup/restore for data protection
- Integrity dashboard with anomaly detection for admin review

## Documentation

- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — Technical structure, data model, tech stack decisions
- [`docs/FEATURES.md`](docs/FEATURES.md) — Detailed feature specifications
- [`docs/DESIGN_PRINCIPLES.md`](docs/DESIGN_PRINCIPLES.md) — Philosophical guardrails for all design decisions
- [`docs/ASSETS_TO_PORT.md`](docs/ASSETS_TO_PORT.md) — Carry-over checklist from the old repo
- [`docs/CLAUDE_CODE_KICKOFF.md`](docs/CLAUDE_CODE_KICKOFF.md) — Starter prompt for Claude Code

---

**Built by someone who worked restaurant floors and wanted something better.**
