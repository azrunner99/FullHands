# Design Principles

Philosophical guardrails for every decision in FullHands. When in doubt, consult this document.

---

## 1. Servers are protagonists

Every feature should make servers feel like characters in an ongoing story. They are not metrics being measured. They are not resources being managed. They are the people the app exists for, and the app should treat them that way.

**In practice:**
- Profiles should feel like character sheets, not HR records
- Celebrations are about the person, not the number
- LLM-generated lines address the server by name with awareness of their story
- Long-tenured servers' profiles should visibly reflect their career

## 2. Shared visibility shapes everything

The tablet is seen by the entire team. Design for that reality.

**In practice:**
- Celebrations are team events, not private notifications
- Anything that would embarrass a server stays hidden
- Leaderboards show everyone, but neutral framing
- Integrity flags are admin-only, never public
- Team-mode dynamics assume people are watching each other

## 3. Never publicly shame

No server should ever feel called out, compared unfavorably, or singled out in front of their coworkers. The worst visual state is neutral. There is no "losing" color.

**In practice:**
- Button color bands range from "On Fire" (top) to "Picking Up" (low) — never "failing" or "bad"
- LLM prompts include explicit safety rails against negative comparisons
- Anomaly/integrity findings are admin-only
- No "you're behind" messaging to individuals
- Team mode celebrates winners but doesn't announce losers

## 4. Respect tenure

A 20-year veteran and a first-week rookie should look different at a glance. The app should carry the weight of career.

**In practice:**
- Tier progression with genuinely uncapped top tier
- Hall of Fame visual treatment distinct from everything else
- Legacy achievements gated behind tier thresholds
- LLM tone adjusts for tier (respect for veterans, encouragement for rookies)
- Veteran profiles are richer; rookie profiles are intentionally sparse
- Lifetime stats prominently displayed for those who've earned them

## 5. Admin can always override

Restaurants are messy. Plans change mid-shift. Mistakes happen. Managers need clear paths to fix bad state without losing data.

**In practice:**
- Every automated decision has a manual override path
- State machines accept admin-forced transitions
- Invalidation instead of deletion preserves history
- All overrides are audit-logged
- No dead-ends — always a way out of any state

## 6. Trust but verify

Most people don't cheat. The system should treat users as trustworthy, but it should notice when something is clearly off.

**In practice:**
- "Trust the tap" — no confirmation required on Triples
- Integrity scoring runs in background, never in servers' faces
- Flags are suggestions, not accusations
- Admin investigates, admin decides
- Preserve data for audit; never destructively delete

## 7. Legible from three feet away

This is a mounted tablet, not a phone. Design for glance-and-tap, not scroll-and-read.

**In practice:**
- Large fonts, bold weights, high contrast
- Big tap targets (bigger than Material minimums)
- Minimal nested navigation
- The main screen does 90% of the job
- Everything important is visible without interaction

## 8. Works offline, forever

No core feature requires internet. Ever.

**In practice:**
- All data stored locally in SQLite
- LLM runs on-device
- USB backup for data protection (no cloud required)
- Config doesn't require network verification
- Future cloud features are opt-in, never required

## 9. Celebrations should feel earned

Visual rewards should scale with actual achievement, not just tap frequency. A celebration that fires every tap becomes meaningless.

**In practice:**
- Celebration intensity scales with rarity
- Celebration frequency tapers with unrealistic bursts
- Legendary celebrations are actually rare
- Common moments have subtle feedback; epic moments stop the room
- Avoid reward inflation

## 10. The special is a management tool

The "special run" is not just flavor — it's a lever managers use to fix operational problems. Design features that support that purpose.

**In practice:**
- High point values for specials (admin-configurable, because specials solve different problems at different restaurants)
- Full stack with doubles/triples for maximum motivation
- Visible tracking separate from regular runs
- LLM specifically praises special-run behavior
- History/analytics surface special-run patterns for admin decision-making

## 11. Restaurants are different; defaults are sensible

FullHands works across many restaurant types. Ship strong defaults so anyone can use it immediately. Let admin customize to fit their specific needs.

**In practice:**
- Every configurable value has a default that works
- Out-of-box experience is complete, not a setup wizard slog
- Customization is available but not required
- Admin learns the system before needing to tune it

## 12. Modern game aesthetic, restaurant reality

The visual language borrows from modern video games (tier bubbles, colored badges, rarity treatments) because restaurant staff skew young enough to instantly recognize it. But the app is used in a real workplace — never toy-like, never childish.

**In practice:**
- Rich visual treatments, but grounded
- No cartoon mascots, no goofy animations
- Sophisticated color, typography, and motion
- The aesthetic says "this is a real product that respects you"

## 13. Data is sacred

A shift's data is the server's history. Protect it.

**In practice:**
- No destructive deletes
- Invalidation is reversible
- Backups are first-class
- Restore is deliberate, not accidental
- Schema versioning for forward compatibility
- Admin actions are audit-logged

## 14. Build for evolution

Today's design will need to grow. Build so growth is possible without rewrites.

**In practice:**
- LLM behind an interface (v1 on-device, v2 can add cloud)
- Tenant-aware data model (v1 single-restaurant, v2 can go multi-tenant)
- State machines, not spaghetti
- Testable pure logic
- Documented extension points

## 15. If a feature is worth doing, it's worth doing well

Better to ship fewer features polished than many features half-broken. The old app had 50+ features, many of which didn't work reliably. FullHands should ship core features rock-solid.

**In practice:**
- Cut scope, not quality
- Shift transitions MUST work — design investment proportional to difficulty
- Integrity system MUST be trustworthy — no false accusations
- Celebrations MUST delight — no half-hearted animations
- Quality over quantity, always
