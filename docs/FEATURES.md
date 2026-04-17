# Features

Detailed specifications for every major feature. This document is the source of truth for "what does this feature actually do?"

---

## 1. The run mechanic

### Regular run
- Server taps their button once
- A run is logged for that server, that shift
- Base points awarded (default 1, admin-configurable)
- Button briefly shows tap feedback
- Last-run area updates to feature that server

### Double (Full Hands!)
- Server taps their button 2 times within the Full Hands time window (default 2000ms, admin-configurable)
- System recognizes it as a Full Hands sequence
- Both runs count (so: 2x base points)
- Plus Full Hands bonus (default +3 points, admin-configurable)
- Full-screen celebration takes over the tablet for ~1.5-2 seconds
- Celebration shows server's avatar/name large, "FULL HANDS!" headline, LLM-generated personalized line
- Haptic double-pulse on the tablet
- Optional audio (admin toggleable; default off because restaurants are loud)
- After celebration, last-run area features that server with updated stats

### Triple (Three-Peat)
- Server taps 3 times within the Full Hands window
- All three runs count (3x base points)
- Plus Full Hands bonus AND Triple bonus (default +10 points, admin-configurable)
- Larger full-screen celebration — reserved visual treatment unique to Triples
- More intense haptics
- LLM line acknowledges rarity and specificity ("Marcus threaded a triple — three tickets in one trip")
- Last-run area updates after celebration

### Quadruple (Four-Loaded) — mythic
- 4 taps within the window (extraordinarily rare, almost impossible to do legitimately without very light tickets)
- All 4 runs count
- Maximum bonus points
- Unique legendary-tier celebration
- LLM generates a standout line
- Admin integrity system watches this closely (4-taps-in-2-seconds is a common cheat pattern)

### Special run
- Long-press on server's button (1 second to activate)
- During the press: subtle ring fills around button edge, chip appears briefly showing the configured special name
- If lifted before 1 second completes: registers as a regular tap
- If held past threshold: registers as a special run
- Special points awarded (default 25, admin-configurable, set by the restaurant)
- Shows in celebration with the configured special name ("PIZOOKIE!" or equivalent)
- Stacks with doubles/triples — you can have a Triple where one or more tickets are specials, full points stack

### Stacking rules (full stack)
Any combination of regular + special taps within the Full Hands window counts as a single sequence with all bonuses stacked:

- **Example 1:** Tap + tap (regular + regular) = Full Hands = 2 base + Full Hands bonus
- **Example 2:** Tap + long-press (regular + special) = Full Hands = 1 base + 1 special + Full Hands bonus
- **Example 3:** Tap + tap + long-press = Triple = 2 base + 1 special + Full Hands bonus + Triple bonus
- **Example 4:** Long-press + long-press + tap = Triple = 2 special + 1 base + Full Hands bonus + Triple bonus

The math is just summed event types in the window. Clean and intuitive.

### Trust but verify
- "Trust the tap" philosophy: system does not ask for confirmation on sequences. If you tapped 3 times in 2 seconds, you earned the Triple.
- Background integrity service flags suspicious patterns for admin review
- Admin can invalidate individual runs after the fact
- See [Integrity](#11-integrity--anti-cheat) below

---

## 2. Tier and level system

### Default tier structure

Seven tiers representing a restaurant career arc:

| Tier | Levels | Representative color |
|------|--------|---------------------|
| Rookie | 1–5 | Muted green |
| Runner | 6–15 | Copper |
| Regular | 16–30 | Teal |
| Closer | 31–50 | Deep purple |
| Veteran | 51–75 | Gold |
| Legend | 76–100 | Platinum |
| Hall of Fame | 101+ | Animated iridescent / holographic |

Admin can rename tiers, adjust level thresholds, and modify colors. Defaults work out of the box.

### XP curve

Default curve tuned so that:
- Rookies feel weekly progress (multiple levels per shift when new)
- Regulars feel monthly progress (meaningful milestone every 2-4 weeks)
- Veterans feel quarterly progress (levels take months but are worth it)
- Hall of Fame is uncapped — the curve continues, never a ceiling, but increments become statistically rare

Implementation: parameterized curve (base + incremental step + tier-specific multiplier), stored in `RestaurantConfig`, fully modifiable.

### Visual tier representation

- **On server's profile** — large tier badge prominent; level number paired with tier name ("Level 47 Closer")
- **On avatar bubble in last-run area** — small colored bubble at bottom-right of avatar, color = tier color (Destiny/Overwatch style)
- **On leaderboard** — tier badge beside name
- **In celebrations** — tier info woven into LLM-generated lines where relevant
- **Hall of Fame treatment** — subtle animation on the bubble (shimmer, gradient shift), differentiated from all other tiers

### Tier-aware LLM tone
The on-device LLM is passed the server's tier and level with every prompt. Prompt engineering guides tone:
- Rookies: encouraging, patient, welcoming
- Runners/Regulars: confident, familiar
- Closers: respectful, skilled-peer tone
- Veterans: acknowledgment of experience, specific praise
- Legends/Hall of Fame: deference, reverence (without sycophancy), acknowledgment of stature

---

## 3. Achievement framework

Seven categories, all configurable:

### A. Milestone achievements (career markers, one-time)
- First Run, First Shift
- Century (100 lifetime), 500 Club, Quadruple Digits (1K), 5K Lifer, 10K Machine, 25K Legend, 50K Hall of Famer
- Tier promotions (one per tier reached)

### B. Shift achievements (repeatable daily)
- First Run Today
- Double Digits (10 in a shift)
- Score (20)
- Thirty Burger (30)
- Shift MVP, Runner-Up, Bronze
- Workhorse (highest share of team total)

### C. Rhythm achievements (skill patterns)
- Full Hands! (double)
- Three-Peat (triple)
- Four-Loaded (quad — mythic)
- Hot Streak (5 in a row without miss)
- On Fire (10 in a row)
- Unstoppable (15 in a row)
- Metronome (steady pace for 30 minutes — anti-cheat-friendly)
- Peak Performance (strong run count during lunch or dinner peak hour)
- Closer (strong run count in final hour)
- Opener (strong run count in first hour)

### D. Special run achievements
- First Special
- {Configured name} Pro (25 lifetime specials)
- {Configured name} Champion (100)
- Special Ops (500)
- Stacked (complete a Triple containing a special)
- All Specials Day (5+ specials in one shift)

### E. Team & social achievements
- Team Goal (team met shift target)
- Comeback (team hit goal after trailing at transition)
- Perfect Team (every roster member hit personal minimum)
- Relay (clean lunch-dinner transition)
- Helping Hand (admin-awarded for witnessed teamwork)

### F. Longevity & seniority (tier-gated)
- One Year In (365 days since first shift, tier 3+)
- Five Year Lifer (tier 4+)
- Decade Deep (tier 5+)
- Patient Zero (present at restaurant's FullHands launch)
- Night Owl (tier 3+ only — rookies shouldn't be closing late)
- Double-Shift Warrior (completed both lunch and dinner in one day)
- Hundred Shifts (100 completed tracked shifts)
- Thousand Shifts (tier 5+)

### G. Admin-custom achievements
Admin can create their own:
- Name, description, criteria (one-time/repeatable, trigger conditions)
- Point value, optional icon
- Automatically retroactively awarded to any server already meeting criteria

### Rarity and celebration intensity
- Common (silver/gray) — small visual feedback
- Rare (blue/bronze) — moderate celebration
- Epic (gold) — full-screen celebration
- Legendary (animated, unique) — stop-the-line celebration with LLM line acknowledging magnitude

### Retroactivity and permanence
- Changing achievement criteria retroactively awards servers who met the new criteria
- Deleting custom achievements does NOT strip servers who earned them — earned rank is permanent
- Admin can revoke achievements that came from invalidated runs, with audit trail

---

## 4. Shift management

### Shift types
- Lunch (default)
- Dinner (default)
- Brunch (optional, for weekend/special-schedule support)
- Late (optional, for late-night shifts)
- Custom (admin can define)

### Shift lifecycle
Every shift is a state machine with clear states: Scheduled → Active → Transitioning → Closed. See ARCHITECTURE.md for details.

### Shift planning
Admin plans the day in advance:
- Selects roster for lunch (list of servers)
- Selects roster for dinner (list of servers)
- Sets transition window start/end (inherits from restaurant defaults unless overridden)
- Optional: sets team mode and team assignments
- Optional: sets team goal

### Transition logic (the hard part the old app struggled with)

During the transition window (default 3:30–5:00 PM):
- Both lunch and dinner shifts can be simultaneously active in the data model
- Each run is attributed to the correct shift based on the tapping server's shift membership:
  - Lunch-only servers' taps → lunch shift
  - Dinner-only servers' taps → dinner shift (even during transition, before dinner "officially" starts)
  - Both-shift servers' taps → lunch shift until transition ends, then dinner shift starts fresh (their count resets to 0 for dinner)

At transition end:
1. Lunch shift transitions to Closed state
2. Lunch shift is persisted to history
3. Lunch-only servers become inactive (can't tap)
4. Both-shift servers' dinner counts start at 0 (fresh)
5. Dinner-only servers' transition-window runs are already attributed to dinner, counts remain
6. Dinner shift enters Active state

All state transitions are event-driven (time events, admin events), not mutation-in-ticker.

### Edge cases to handle (all included in v1)

- **Servers who come in unexpectedly** — admin can add to roster mid-shift; their runs start from when they're added
- **Servers who leave early / call out** — admin can mark them as departed; their runs stay counted, they can't tap anymore
- **Tail-end-of-lunch-into-dinner workers** — treated as dinner-only if admin marks them as such, with their transition-window taps attributed correctly
- **Non-standard schedules (brunch, single-shift days)** — transition window is optional; if no transition is set, shift simply runs to its close time
- **Single-shift days** (e.g., Sunday dinner only) — no transition window, just one shift
- **Admin sets wrong date plan** — mid-shift correction flow: admin can re-plan without losing data, runs stay attributed to the active shift

### Manual admin overrides
Admin can, at any time:
- Manually start or end a shift
- Manually advance or reverse the shift state
- Manually move the transition window
- Manually add or remove servers from the active roster
- Manually invalidate runs (with required reason)
- Manually award achievements (Helping Hand and other admin-awarded ones)
- Override MVP / Runner-Up / Bronze assignments if ties are ambiguous

All overrides are audit-logged.

---

## 5. Team mode

### When enabled
- Admin toggle per-shift; off by default
- Admin defines 2-4 teams with names and colors
- Admin assigns each server to a team

### Visual representation
- **Colored ring around each server's button** when team mode is active (primary indicator)
- **Small team abbreviation chip** in button corner (accessibility backup for color-blind users)
- **Top-of-screen team standings bar** — horizontal, divided into team-colored segments proportional to scores, updates live on every tap
- **Tap the standings bar** to expand team details: members, individual contributions, recent runs, team stats

### Scoring
- Raw totals only in v1 (sum of team members' runs)
- Per-capita scoring is a v2 feature flag if the raw totals don't feel fair with uneven teams

### Team-aware LLM
When team mode is active, the LLM knows:
- Which team the celebrated server is on
- Current team standings
- How the current run affected the standings
- Whether their team is leading, trailing, or close

LLM lines naturally acknowledge team dynamics without being instructed per-message.

### Team-aware celebrations
- Brief team-colored ripple across teammates' buttons when one member hits a milestone
- End-of-shift team victory celebration with winning team's colors filling the screen

### Team achievements
- Team Victory (repeatable)
- Undefeated (won every shift in a week — repeatable weekly)
- Team Captain (highest contributor on winning team — repeatable)
- Clutch (final run pushed team into the lead — rare)
- Dynasty (10 shift wins with same team composition — tier-gated)

### Edge cases
- **Uneven teams** — admin chooses raw totals (v1) or per-capita (v2)
- **Mid-shift team swap** — runs stay attributed to the team they were earned under
- **Solo team (one team has no one)** — gracefully revert to individual mode for that shift

---

## 6. The tablet UI

### Landscape orientation, admin-locked to landscape

### Top region: context bar
- **Team mode on:** team standings bar with colored segments
- **Team mode off:** shift context (shift type, roster size) with team goal progress bar if goal is set
- Tappable to expand details

### Middle region: server buttons
- Grid of server buttons, sized to maximize tap target
- Each button shows:
  - Server name (large, high-contrast)
  - Shift run count (the biggest number)
  - Shift special run count (smaller, labeled with special name, e.g., "⭐ 3 Pizookies")
  - Current level and tier indicator (small)
- **Button background color changes based on shift standings** (relative performance bands):
  - **On Fire** — top performer, warm saturated color
  - **Strong** — above median, solid mid-tone
  - **Steady** — around median, neutral
  - **Picking Up** — below median, cool (never negative/shaming)
- **Team-colored ring around button** when team mode is active
- **No avatars on buttons** — avatars appear in the last-run area and on profiles; buttons stay clean and legible

### Bottom region: last-run area
- Always visible, persists between runs until the next run replaces it
- Features the most recent runner:
  - Their avatar (large)
  - Their personalized banner as background
  - Their name
  - Shift stats: runs | specials | XP earned
  - All-time stats: total runs | total specials | total XP
  - Tier-colored bubble on avatar (bottom-right) with level number
- Modern game-UI aesthetic — rich but legible
- Idle state (before first run of shift): clean "Ready to run" with shift context, minimal visual weight

### Celebration overlays
- Full-screen takeover for Full Hands, Triples, Quads, and epic/legendary achievements
- 1.5-2 seconds duration
- LLM-generated personalized line
- After celebration, last-run area features the celebrated server

---

## 7. Server profiles

Accessible by anyone tapping a server's name or avatar.

### Rookie profiles
- Sparse — intentionally so
- Basic stats, tier badge, current level
- Achievement wall shows any earned (often empty early on)

### Veteran profiles (museum-like)
- Rich display of career stats
- Achievement wall (all earned, organized by category and rarity)
- Career highlights:
  - First shift date / years of service
  - All-time bests (shift record, streak record)
  - MVP history with counts
  - Tier progression history with dates
- Personal banner customization
- Avatar history
- Recent activity (last N shifts)

### What's visible to everyone
- All public stats (server's runs, achievements, tier/level)
- Public banner and avatar
- No private data (no personal notes, admin comments)

### What's admin-only
- Integrity score history
- Invalidated run counts
- Admin notes
- Detailed run patterns / heat maps

---

## 8. LLM integration

### On-device only for v1
- Model: Gemma 2B/3N or Phi-3 Mini or similar small-footprint LLM
- Inference: MediaPipe LLM Inference (preferred) or llama.cpp via FFI
- Target tablet: 6-8 GB RAM, no GPU
- Inference target: under 2 seconds per generation

### Use cases
1. **Per-run encouragement** — brief, contextual line after a notable run
2. **Full Hands / Triple / Quad celebration lines** — longer, personalized, tier-aware
3. **Achievement unlock lines** — acknowledges the specific achievement with personal flair
4. **End-of-shift recap per server** — 2-3 sentence narrative summary
5. **Shift-wide recap** — team summary, standout moments
6. **Season/period reflections** — longer narratives, refreshed periodically

### Prompt engineering principles
- Always include: server's name, tier, level, shift context, what just happened
- Restaurant "voice" is admin-configurable (default neutral-warm; can be set to "sports-bar energy," "fine-dining reserved," "casual friendly," etc.)
- Never shame, never compare negatively, never call out slow performers
- Respect tier hierarchy in tone
- Restaurant-specific references when admin provides them (menu items, slang)
- Fallback strings available when LLM fails or is too slow

### Prompt structure (example)
```
System prompt includes: restaurant voice, tier awareness, tone guidelines,
safety rails (no shaming, no comparison, no references to slow teammates)

User prompt: Generate a brief celebration line for Marcus who just hit
Full Hands. He's a Level 47 Closer with 12,847 lifetime runs. This is his
8th Full Hands this shift. Restaurant voice: "sports-bar energy".
```

### What the LLM does NOT do
- Does not generate names, achievements, or mechanics (those are code)
- Does not make authoritative claims about facts (just flavor text)
- Does not store user data or learn between sessions

---

## 9. Admin features

All behind PIN lock.

### Restaurant configuration
- Restaurant name, branding
- Special run name and point value
- All point values (base, bonuses)
- Full Hands time window
- Tier names, colors, thresholds, XP curve
- Achievement enablement
- Custom achievement creation
- LLM voice/tone setting
- Operating hours, default transition window

### Roster management
- Add/edit/remove servers
- Assign preset avatars or upload custom
- Assign preset banners or upload custom
- Manage team assignments (when team mode is on)

### Shift management
- Plan upcoming shifts (rosters, transitions)
- Override current shift state (start/end/transition)
- Set team goals
- Toggle team mode per shift

### Integrity dashboard
See [Integrity](#11-integrity--anti-cheat) below.

### History and reporting
- View past shifts with full details
- Per-server history (all shifts they were part of)
- Team performance trends
- Shift-over-shift comparisons
- Achievement awarding history

### Manual awards
- Award Helping Hand and other admin-awardable achievements
- Add custom bonus XP (with reason logged)
- Invalidate runs (with reason logged)
- Revoke achievements if needed (with reason logged)

### Backup & restore
- Full backup to USB via Android SAF
- Restore from backup file (full replace)
- Backup reminder prompts after N shifts since last backup

### Data management
- Export data (CSV, JSON)
- Archive old shifts (configurable retention)
- Reset restaurant (factory wipe, password-protected)

---

## 10. Celebrations system

### Layered celebration model

Every earning moment has a proportional celebration:

| Event | Celebration intensity |
|-------|----------------------|
| Regular run | Button tap feedback only (subtle) |
| Common achievement | Small popup, 1-2 seconds |
| Rare achievement | Moderate popup with LLM line |
| Full Hands | Full-screen takeover, LLM line, haptic double |
| Epic achievement | Full-screen, LLM line, extended haptic |
| Triple | Larger full-screen, unique visual, extended haptic, tier-aware LLM |
| Legendary achievement | Stop-the-line celebration, sustained visual, major LLM moment |
| Quad | Mythic-tier celebration reserved just for this |

### Celebration caps (anti-cheat protection)

- Celebration frequency tapers after unrealistic tap bursts
- A server tapping 50 times in 10 minutes does NOT see 50 celebrations
- Protects the *feel* of the app without accusing anyone

### Team-aware rippling
When team mode is on, teammates' buttons briefly flash their team color during a celebration.

### Audio (optional, admin-toggle)
Default off because restaurants are loud and audio may not cut through or may be disruptive. Admin can enable.

### Haptics
- Single tap: none or very subtle
- Full Hands: double pulse
- Triple: triple pulse, stronger
- Quad: distinct sustained pattern
- Achievements: pattern-keyed to rarity

---

## 11. Integrity / anti-cheat

### Signals tracked
- Tap interval distribution per server
- Burst density
- Bimodal patterns
- Time-of-day anomalies vs restaurant's typical pace
- Runs-per-minute rate vs team average
- Double/Triple rate disproportionate to team average
- End-of-shift spikes
- Same-second taps across different servers (friend-tapping pattern)
- Achievement-threshold proximity spikes

### Anomaly score
- 0-100 per server per shift
- Composite of all signals, with configurable weights
- Color-coded: green (clean), yellow (worth a glance), red (clear anomaly)
- NEVER visible to servers or on the public tablet — admin-only

### Integrity dashboard
- **Layer 1:** Daily heat map per server (time-of-day buckets × intensity)
- **Layer 2:** Tap interval histogram per server (with team average overlay)
- **Layer 3:** Anomaly score ranking (green/yellow/red)
- **Layer 4:** Searchable raw tap log (filterable, exportable)
- **Layer 5:** Run-removal tool (with required reason, audit-logged)

### Real-time flagging (subtle)
- In-shift anomaly flagging creates yellow dots on admin screen
- Nothing visible on public tablet
- Admin can investigate after shift

### Trust-but-verify philosophy
- System never publicly accuses
- Never deletes data destructively (invalidation only)
- Preserves all evidence for audit trail
- Human judgment always decides, never automated punishment

---

## 12. Historical data and trends

- All shifts archived with full detail
- Server lifetime stats computed from run records (source of truth)
- Trends visible to admin:
  - Runs-per-shift over time (per server, per team)
  - Peak hours performance
  - Special run patterns (are they happening? which servers?)
  - Achievement unlock cadence
- Servers can view their own historical performance on their profile
- History is fully backed up in USB export

---

## 13. Sound and accessibility

### Accessibility
- High-contrast typography throughout
- Minimum tap target sizes meet Material Design guidelines
- Colorblind-safe palettes (color paired with pattern/letter)
- Optional larger-text mode

### Sound
- Off by default (restaurants are loud)
- Admin-toggleable
- If enabled, celebrations have distinct audio cues matching haptic patterns
- No background music (not appropriate for restaurant floor)

### Haptics
- Always on by default
- Admin-toggleable
- Tablet-side only (the tablet's own haptic motor)

---

## 14. Default restaurant config (out of the box)

For admin setup simplicity, ship with these defaults:
- **Restaurant name:** "FullHands Restaurant" (admin changes on first launch)
- **Special run name:** "Special Run" (generic, admin renames)
- **Regular run points:** 1
- **Special run points:** 25
- **Full Hands bonus:** +3
- **Triple bonus:** +10
- **Quad bonus:** +25
- **Full Hands time window:** 2000ms
- **Long-press threshold:** 1000ms
- **Transition window:** 3:30 PM – 5:00 PM
- **Tier names/thresholds:** as specified above
- **All achievements:** enabled
- **Team mode:** off
- **Sound:** off
- **Haptics:** on
- **LLM voice:** "warm and encouraging"
