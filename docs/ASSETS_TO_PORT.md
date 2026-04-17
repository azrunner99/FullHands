# Assets to Port from Old Repo

The previous app (`foodrunner97501-main`) has real artwork worth carrying over. This document lists what to bring, what to skip, and where to put it in the new project.

---

## To port as-is

### Avatar gallery (31 images)
**Source:** `foodrunner97501-main/assets/avatars/image001.png` through `image031.png`
**Destination:** `assets/avatars/` in new project
**Reason:** Generic enough to work across restaurant types; solid starting gallery for servers to choose from.

Keep the numeric naming convention for simplicity, or rename to descriptive names if a review of the images suggests clearer labels.

### Fonts
**Source:** Old project's `assets/fonts/` directory
- `LuckiestGuy-Regular.ttf` — display font for big numbers and celebration headlines
- `Montserrat-Bold.ttf` — body font for readable stats

**Destination:** `assets/fonts/` in new project

These are already configured in the old `pubspec.yaml` and work well for the tablet UI.

---

## To curate (keep the best 15-20)

### Banner gallery
**Source:** `foodrunner97501-main/assets/banners/` (100+ images)
**Destination:** `assets/banners/` in new project (keep ~15-20 most generic/broadly appealing)

**Selection criteria:**
- Must not contain restaurant-specific branding (BJ's logos, menu items)
- Must work as a background for text overlays (not too busy)
- Must cover a range of aesthetics (clean, vibrant, moody, minimalist)
- Prefer abstract or landscape-style over literal/thematic
- Avoid anything that dates quickly (trendy graphic design elements)

**Process during build:** After extracting, do a visual review of all 100+ banners and select the strongest 15-20 that meet these criteria. Document the selections in a comment file inside `assets/banners/README.md`.

---

## To skip entirely

### BJ's-specific splash screens and branding
**Source:** Various `splash.png` files in `android/app/src/main/res/drawable-*`
**Reason:** FullHands has a new brand. Create new splash assets during the build.

### `assets/foodrunner.png`, `assets/runner.png`
**Reason:** Part of the old app's branding, not needed in the new one.

### Old encouragement messages
**Source:** `foodrunner97501-main/lib/messages.dart`
**Reason:** Explicit decision — LLM replaces static messages. Don't reference this file even for tone inspiration.

### Old code files (ALL of them)
**Reason:** Clean rebuild. The old code has useful *concepts* documented in the specs, but no code should be copied. The architecture was the problem. Start fresh.

### USB backup files, generated assets, build outputs
**Source:** Various build artifacts in the old repo
**Reason:** These regenerate as needed. Not source artifacts.

---

## New assets to create during build

### Tier badges (7 tiers × multiple sizes)
- Rookie, Runner, Regular, Closer, Veteran, Legend, Hall of Fame
- Each tier needs badge imagery that visibly escalates in sophistication
- Hall of Fame should include animated/shimmer elements
- SVG preferred for resolution independence

### Celebration backgrounds
- Full Hands, Triple, Quad, achievement rarity tiers
- Each with distinctive visual treatment
- Consider motion graphics for Triple and above

### Team color palette
- Accessibility-safe color set with pattern/letter backups
- Minimum 4 distinct teams supported
- Each color tested for colorblind visibility

### Default app splash and icon
- FullHands branding (to be finalized during build)
- Android adaptive icon (foreground + background layers)

### Empty-state illustrations
- "Ready to run" idle state for the last-run area
- "No shifts planned" admin state
- "First run of the day" prompts

---

## Asset handling guidelines

- Store images in formats matched to purpose:
  - PNG for avatars and transparent elements
  - WebP for banners (smaller file size, good quality)
  - SVG for tier badges, icons, scalable elements
- All assets declared in `pubspec.yaml` under `flutter > assets`
- Consider asset variants for different screen densities if needed
- Keep total asset weight under 30-50 MB if possible (old app was closer to 10 MB after banners; we want to stay lean)

---

## Extraction command

When the repo is set up, extract the specific old-repo assets with:

```bash
# Extract just what we need from the old zip
unzip -j foodrunner97501-main.zip "foodrunner97501-main/assets/avatars/*" -d assets/avatars/
unzip -j foodrunner97501-main.zip "foodrunner97501-main/assets/banners/*" -d /tmp/old-banners/
unzip -j foodrunner97501-main.zip "foodrunner97501-main/assets/fonts/*" -d assets/fonts/

# Then manually curate the banners after reviewing them visually
```

Keep `/tmp/old-banners/` around during curation; discard after the 15-20 best are moved into `assets/banners/`.
