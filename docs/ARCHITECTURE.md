# Architecture

This document describes how FullHands is structured technically. It is intentionally opinionated about decisions already made, and intentionally open about decisions left to the build process.

---

## Platform & stack

- **Framework:** Flutter (Dart), Android-first, iOS path open for future
- **Target device:** Android tablet (10–13 inch), landscape primary orientation
- **Minimum Android version:** Android 10 (API 29) — reasonable modern baseline for tablets
- **State management:** Riverpod or Bloc (decided during build — Provider from the old app is usable but newer options are cleaner for the state machine complexity we need)
- **Local storage:** SQLite via Drift or sqflite (NOT shared_preferences — we need real queryability for history, integrity analysis, and trends)
- **LLM inference:** MediaPipe LLM Inference (primary) or llama.cpp via FFI (backup option). Decision during build based on model support and performance.

## Key architectural principles

### 1. Clean separation of concerns

The old app collapsed UI, state, persistence, and business logic into large monolithic files (`home_screen.dart` at 105KB, `app_state.dart` at 50KB). This is the single biggest thing to avoid.

Structure the code as:

```
lib/
  data/           # Database, repositories, persistence
  domain/         # Business logic, models, state machines
  llm/            # LLM provider interface and implementations
  ui/             # Screens, widgets, theming
    screens/
    widgets/
    theme/
  services/       # Cross-cutting services (time, haptics, backup)
  config/         # Restaurant config, feature flags
  integrity/      # Anti-cheat analysis
```

No single file should exceed ~500 lines. If it's growing beyond that, it's doing too many things.

### 2. LLM behind an interface

All LLM calls go through an abstract `LLMProvider` interface with specific methods for each use case:

```dart
abstract class LLMProvider {
  Future<String> generateEncouragement(EncouragementContext ctx);
  Future<String> generateShiftRecap(ShiftRecapContext ctx);
  Future<String> generateAchievementLine(AchievementContext ctx);
  // etc.
}
```

The v1 implementation is `OnDeviceLLMProvider` backed by MediaPipe. When cloud features come later, adding `CloudLLMProvider` is a drop-in change, not a rewrite.

Each method takes a structured context object (not raw strings) so prompt engineering is centralized and testable.

### 3. Shift state as a proper state machine

This was the old app's Achilles' heel. Shift transitions lived inside a 30-second ticker that mixed time-based triggers, data mutations, and UI state sync. It broke often.

The new design treats shifts as first-class state machines:

```
Scheduled → Active → Transitioning → Closed
```

- **Scheduled** — shift exists in the plan but hasn't started
- **Active** — shift is running, runs are being logged
- **Transitioning** — during the configurable transition window, both lunch and dinner can be simultaneously active, with runs attributed to servers' actual shift membership
- **Closed** — shift is done, data is persisted to history, no more runs accepted

A `ShiftManager` service owns the state. The clock fires events ("transition window began," "transition window ended," "dinner shift should activate"), and the manager responds to events by changing state. Time-based triggers are decoupled from data mutations.

### 4. Testability first

Every piece of core logic — shift state transitions, achievement unlocking, tier calculations, integrity scoring — must be testable without a UI or real time.

- Use an injectable `Clock` abstraction (`DateTime.now()` never called directly in business logic)
- Keep business logic in pure Dart classes, not in widgets
- Riverpod providers or Bloc events are the UI-to-logic bridge
- Integration tests run against a real SQLite database but with synthetic time

### 5. Multi-restaurant configuration

The app supports a single active restaurant configuration per install, but the data model is tenant-aware from day one.

A `RestaurantConfig` object holds:

- Restaurant name (branded, shown on splash and admin)
- Special run name (e.g., "Pizookie")
- Special run point value (default 25, admin-configurable)
- Regular run point value (default 1)
- Full Hands bonus points (default +3)
- Triple bonus points (default +10)
- Full Hands time window (default 2000ms, admin-configurable)
- Tier names and thresholds (7 tiers, admin can rename)
- XP curve parameters (defaults provided)
- Achievement enablement and custom achievements
- Team mode configurations
- Shift schedule defaults (operating hours, transition window)
- Integrity thresholds and weights

Config is persisted locally and backed up with the main data export.

---

## Data model

### Core entities

**Server**
- id, name, team_color (nullable, populated when team mode is active)
- avatar_path, banner_path, preset_avatar_id (if from gallery)
- created_at, is_active

**ServerProfile** (1:1 with Server, career stats)
- lifetime_runs, lifetime_specials, lifetime_xp
- best_shift_runs, best_shift_date
- mvp_count, runner_up_count, bronze_count
- current_streak_best, all_time_streak_best
- tier_promotions_earned (list of tier achievements)
- shifts_completed, doubles_count, triples_count
- first_shift_at
- avatar_history

**Shift**
- id, shift_type (lunch/dinner/brunch/late/custom), state (scheduled/active/transitioning/closed)
- planned_start, planned_end, actual_start, actual_end
- roster (list of server_ids)
- team_mode_enabled, team_config (if enabled)
- team_goal (optional, with goal_type)
- transition_window_start, transition_window_end
- created_at, closed_at

**Run** (a single tap, the atomic event)
- id, shift_id, server_id, timestamp
- run_type (regular/special)
- sequence_group_id (nullable — if part of a Double/Triple, groups taps together)
- sequence_position (1, 2, or 3 within the group)
- points_awarded, bonus_points_awarded
- is_invalidated (for admin cheat-removal), invalidation_reason
- device_state_snapshot (for forensics)

**Achievement** (definition)
- id, name, description, category
- rarity (common/rare/epic/legendary)
- is_repeatable, is_admin_custom
- criteria_config (JSON — flexible, supports different trigger types)
- point_value
- icon_config
- gate_tier (nullable — some achievements are tier-gated)

**AchievementUnlock** (server earning an achievement)
- id, server_id, achievement_id, shift_id
- unlocked_at, points_awarded
- invalidated (parallel to Run invalidation)

**DayPlan**
- date, lunch_roster, dinner_roster
- lunch_shift_id, dinner_shift_id (null until created)
- transition_window_minutes
- notes (admin-only)

### Soft-delete and invalidation

No destructive deletions for runs or achievements. When admin removes a fraudulent run, it gets marked invalidated with a reason and timestamp. Stats recalculate to exclude it, but the record stays for audit.

---

## Admin authentication

- Admin PIN stored as a hash (argon2id or similar; never plaintext)
- PIN gates all admin screens, manual overrides, restaurant config
- Servers have unrestricted access to the public-facing screens (tap buttons, view their own profile, history, leaderboard)
- PIN can be reset only by wiping the app or via backup restore

## Backup & restore

- Admin can trigger a full backup to a USB drive via Android's Storage Access Framework
- Backup is a versioned JSON (or SQLite) dump including:
  - All servers, profiles, shifts, runs, achievements, day plans, restaurant config
  - Schema version number for forward compatibility
  - Backup metadata (timestamp, restaurant name, record counts)
- Restore validates schema version and record integrity before overwriting
- Partial restore is NOT supported (too error-prone); it's full replace or nothing
- Admin gets a gentle reminder to back up after N shifts since last backup (configurable, default 7)

## Integrity / anti-cheat architecture

A separate `IntegrityService` analyzes tap patterns in the background:

- Per-server, per-shift anomaly score (0-100) with configurable signal weights
- Signals: tap interval distribution, burst density, bimodal patterns, time-of-day anomalies, runs-per-minute rate vs team average, disproportionate double/triple rate, end-of-shift padding
- Surfaces findings only in admin-only dashboard; never visible to servers
- Provides tools for admin to investigate, invalidate runs, and document their reasoning

Real-time: subtle in-shift flagging for admin later review (yellow dot on admin screen); nothing visible on the public tablet.

---

## Performance targets

- Tap-to-feedback latency: under 100ms (critical; anything slower feels broken)
- Celebration animation: 1.5-2 seconds total, never blocks next tap
- LLM generation for encouragements: under 2 seconds on target tablet hardware (budget constraint for model selection)
- App cold start: under 3 seconds to interactive state
- No frame drops during celebrations even on modest tablets

---

## What's intentionally NOT in v1

These are real, valuable features, but they're out of scope for the first build to keep focus:

- Cloud sync / multi-device
- Push notifications
- Companion phone app for servers
- Manager web dashboard
- POS integration (ticket-fire data)
- Cross-restaurant analytics
- Subscription/billing
- Cloud LLM fallback
- Custom achievement icon uploads (text-only for v1 if needed)
- Per-capita team scoring (raw totals only for v1)
- Power-ups
- Seasonal challenges
- Mentorship tracking

The architecture should leave doors open for these, but not build them now.
