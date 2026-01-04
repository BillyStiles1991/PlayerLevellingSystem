# PlayerLevellingSystem

# Unity Scripts and Unity Inspector information  — Player Stats, EXP Levelling + UI Bars

## 1) Overview 
This script provides a complete **player stats + levelling** system with UI support:
- Tracks **health, magic, EXP, level, and stat points**
- Handles **level-up logic with EXP carry-over**
- Calculates **increasing EXP requirements** using a curved growth system with 5-level “tiers”
- Updates **UI sliders + text displays** for health/magic/EXP
- Shows a **Level Up icon** that fades out after levelling

This is designed for RPG / action-RPG / progression-based games where the player collects EXP and levels up.

---

## 2) Script description

### PlayerStats.cs
Manages player progression, resources, and UI.

**Core stats**
- Stores:
  - `currentHealth / maxHealth`
  - `currentMagic / maxMagic`
  - `currentExp / maxExp`
  - `level`
  - `currentStatPoints`
  - attributes like `strength, speed, stamina, dexterity, agility`

**levelling logic (Update loop)**
- Checks if `currentExp >= maxExp`.
- If yes, it levels up **as many times as needed** using a `while` loop (handles large EXP gains).
- On each level-up:
  - Subtracts the EXP requirement (`currentExp -= maxExp`) → **carry-over EXP remains**
  - Increases `level` by 1
  - Adds `+5` stat points
  - Recalculates the next `maxExp`

**EXP requirement curve**
- Uses `CalculateMaxExpForLevel(level)`:
  - Starts with a gentle base growth (`baseGrowthPerLevel`)
  - Adds extra growth bonuses in **5-level tiers** (1–5, 6–10, 11–15, etc.)
  - Tier bonuses increase progressively using a multiplier (`tierBonusMultiplier`)
  - Final result is exponential-style scaling:
    `baseExpRequirement * growthPerLevel^(level - 1)`

**UI updating**
- `ChangeSliderUI()` updates:
  - Slider current values (health/magic/exp)
  - Slider max values (maxHealth/maxMagic/maxExp)
  - TextMeshPro labels like `"current / max"`
- Updates the level label (`levelText`) in Start and after level-ups.

**Level Up icon**
- On level-up:
  - Enables `levelUpIcon`
  - Ensures it starts fully visible (alpha = 1)
  - Fades out over ~3 seconds with `FadeOutLevelUpIcon()` coroutine
  - Hides the icon at the end

---

## 3) How to set up the project in the Unity Inspector

### A) Add the script to your Player
1. Create / Select your Player GameObject
2. Attach `PlayerStats.cs`

---

### B) Create the UI (Sliders + Text)
In your Canvas, create:
- **Health Slider** (Unity UI → Slider)
- **Magic Slider**
- **EXP Slider**

For each slider:
1. Set a sensible min/max in the Slider component (the script will overwrite max at runtime)
2. (Recommended) Remove slider handle + style as you like

Create TextMeshPro UI labels:
- `healthSliderDisplay` (e.g., "50 / 100")
- `magicSliderDisplay` (e.g., "10 / 25")
- `expSliderDisplay` (e.g., "4 / 20")
- `levelText` (e.g., "Lvl : 1")

> Make sure TextMeshPro is installed (Window → Package Manager → TextMeshPro).  
> Create TMP text via UI → Text - TextMeshPro.

---

### C) Assign UI references in the PlayerStats Inspector
On the Player object (with PlayerStats attached), drag these into the fields:

**Sliders**
- `healthBar` → your Health Slider
- `magicBar` → your Magic Slider
- `expBar` → your EXP Slider

**TMP text**
- `healthSliderDisplay` → TMP text for health display
- `magicSliderDisplay` → TMP text for magic display
- `expSliderDisplay` → TMP text for exp display
- `levelText` → TMP text for level label

**Level Up icon**
- Create an Image (UI → Image) that says/looks like “Level Up”
- Assign it to `levelUpIcon`
- The script will hide it at Start and fade it out when levelling occurs

---

### D) Set initial values
In the Inspector, set starting stats so the UI displays correctly immediately:

**Example starting values**
- `maxHealth = 20`, `currentHealth = 20`
- `maxMagic = 20`, `currentMagic = 20`
- `currentExp = 0`
- `level = 1` (serialized private)
- `currentStatPoints = 0`

**EXP settings**
- `baseExpRequirement = 20` (EXP needed from level 1 → 2)
- `baseGrowthPerLevel = 1.80` (early curve)
- `firstTierBonus = 0.1` (extra growth beginning at levels 6–10)
- `tierBonusMultiplier = 0.2` (controls how tier bonuses ramp)

> Note: Values below 1 can produce unwanted curves. If your EXP requirements is off, try values **> 1** (e.g., 1.1–1.3) for a more typical acceleration of the curve.

---

### E) To test

**Adjust values on the Player>Player Stats (Script) GameObject when running the unity scene** This will show the component working, gradually increase the exp 'Current exp' field in order to see levels progressing and experience needed accelerating as levels get higher.

## Files included
- `PlayerStats.cs' — levelling, EXP curve calculation, stat points, and UI (sliders + text + level-up icon fade)
