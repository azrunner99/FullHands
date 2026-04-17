# Claude Code Kickoff

This document is the starter prompt to paste into Claude Code (or hand to it as a first instruction) when beginning the FullHands build.

---

## How to use this

1. Set up your dev environment (VS Code, Flutter SDK, Android Studio for Android tooling, Claude Code CLI installed)
2. Create a new empty repo for FullHands
3. Drop the contents of this `fullhands` folder (README.md, docs/) into the repo root
4. Copy the old repo's zip file into the repo root for asset extraction (don't commit the zip; it's source material only)
5. Run `claude` in the repo root
6. Paste the prompt below as your first message

---

## Starter prompt for Claude Code

```
I'm building a new Flutter app called FullHands — a shared-tablet food running
tracker and gamification system for restaurant teams. I've placed extensive
planning documents in this repo that cover the vision, architecture, features,
and design principles.

Before writing any code, I need you to:

1. Read all of these files thoroughly:
   - README.md (project overview)
   - docs/ARCHITECTURE.md (technical structure)
   - docs/FEATURES.md (detailed feature specs)
   - docs/DESIGN_PRINCIPLES.md (philosophical guardrails)
   - docs/ASSETS_TO_PORT.md (carry-over checklist)

2. Tell me back, in your own words, what you understand this app to be and
   what the most important design priorities are. I want to confirm we're
   aligned before any code gets written.

3. Propose a build order — what should be built first, second, third, etc.
   Think about dependencies (you can't build the UI before you have data
   models; you can't build celebrations before you have a run mechanic;
   shift transitions are the hardest part and probably need to be tackled
   early so they're rock-solid). Err on the side of getting core data
   flowing end-to-end before investing heavily in UI polish.

4. Flag anything in the planning docs that seems unclear, contradictory,
   underspecified, or potentially problematic. These docs were written
   conversationally and may have gaps. I'd rather you call them out than
   guess.

5. Ask me any clarifying questions before we start. Don't be shy — better
   to ask now than build the wrong thing.

Do NOT write any Flutter code, create any files, or run any setup commands
in this first response. I want to align on the plan before you start moving.

Context on me: I have worked on a predecessor app (food_runs_counter) but
I'm rebuilding from scratch here. I'm not a professional Flutter developer;
I'll be leaning on you heavily for architecture decisions and implementation.
I want clean, testable, maintainable code. The old app grew into a monolithic
mess because of rapid organic growth; let's avoid that fate by being
disciplined about structure from day one.

Context on the process: I'll be using the "GSD" (Get Shit Done) prompt
pattern alongside you to help make decisions as the build progresses.
That means you should feel free to surface trade-offs and pros/cons rather
than always picking for me — but also be willing to have a strong opinion
when you have one. Lean toward shipping working software over debating
every detail.

One hard constraint: the on-device LLM piece is novel territory. Don't
build any LLM integration in the first few phases. Get the core data model,
shift state machine, basic tap mechanic, and admin flows working first.
LLM comes in once the foundation is solid.
```

---

## Suggested first build phases

Not binding — Claude Code should propose its own order, but for reference, a reasonable sequence:

**Phase 1: Foundation**
- Flutter project setup, Android configuration
- SQLite schema with core entities (Server, Shift, Run, Achievement, etc.)
- Data access layer (repositories)
- Injectable Clock abstraction
- Admin PIN + basic settings persistence
- Unit test harness for all core logic

**Phase 2: Shift mechanics (the hard part)**
- Shift state machine (Scheduled → Active → Transitioning → Closed)
- Day planning (rosters, transition windows)
- Transition logic with all edge cases (lunch-only, dinner-only, doubles, unexpected arrivals, early departures, etc.)
- Comprehensive unit tests for the state machine
- Admin manual overrides for all state transitions

**Phase 3: Core tap mechanic**
- Run logging (single tap)
- Full Hands detection (double within window)
- Triple detection
- Quad detection
- Special run (long-press) with visual feedback
- Full stacking rules with all combinations tested
- Integrity data capture (timestamps, device state)

**Phase 4: Tablet UI skeleton**
- Landscape layout with three regions (top bar, button grid, last-run area)
- Clean server buttons (no avatars, standings-based color bands)
- Last-run area with avatar, banner, tier bubble, stats
- Navigation between screens
- Admin PIN flow
- Basic profile view

**Phase 5: Gamification core**
- XP and level calculations
- Tier system with defaults
- Achievement framework with seven categories
- Retroactive awarding
- Admin custom achievements

**Phase 6: Celebrations**
- Celebration layer with rarity-based intensity
- Full Hands / Triple / Quad visual treatments
- Achievement unlock overlays
- Haptic patterns
- Celebration throttling/caps

**Phase 7: Team mode**
- Team configuration
- Standings bar
- Team-colored ring on buttons
- Team achievements
- Team-aware celebrations

**Phase 8: Admin deep features**
- Restaurant configuration UI
- Roster management
- Shift planning interface
- History and reporting
- Integrity dashboard with heat maps and anomaly scoring
- Run invalidation tooling

**Phase 9: LLM integration**
- Provider interface and on-device implementation
- Prompt engineering for each use case (encouragement, celebration, recap)
- Fallback strings when LLM unavailable or too slow
- Tier-aware tone handling
- Restaurant voice configuration

**Phase 10: Polish & backup/restore**
- USB backup with Storage Access Framework
- Full restore flow with schema validation
- Asset porting (avatars, banners, fonts from old repo)
- New tier badge artwork
- Default banner selections and custom splash screen
- Final accessibility pass
- Performance tuning

**Phase 11: Testing on real hardware**
- Deploy to physical Android tablet
- UX validation at three-feet viewing distance
- Latency tuning
- Real-shift simulation testing

---

## Ongoing practices

Encourage Claude Code to:

- Write tests *before or alongside* implementation, especially for anything touching shift state or integrity
- Keep files small (< 500 lines)
- Surface architectural decisions for discussion before committing to them
- Periodically summarize what's built, what's next, and what's blocked
- Never use `shared_preferences` for anything beyond trivial flags
- Propose refactors when files start growing beyond healthy size
- Document deviations from the planning docs and why

---

## If you need to restart

If a session gets derailed or if you want Claude Code to re-orient later, point it back to these docs with:

```
Please re-read README.md, docs/ARCHITECTURE.md, docs/FEATURES.md,
docs/DESIGN_PRINCIPLES.md, and docs/ASSETS_TO_PORT.md. Then tell me
your current understanding of where we are in the build, what's next,
and any concerns or questions.
```

Keep these docs updated as decisions evolve during the build. They're living documents.
