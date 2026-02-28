### Assassin Background (As)

**Background Overview**  
The Assassin is a master of silent death, trained in the shadows to strike unseen. Starting with superior stealth, precise short blades, and deadly darts, Assassins excel at backstabbing drowsy foes and vanishing before alarms sound. Perfect for Nyxara worshippers, emphasizing noiseless kills and veil mastery. In lore: Recruits of Nyxara's veiled order, marked for the Final Contract. Pairs ideally with stealthy species; avoids heavy races/armour.

| Aspect              | Details |
|---------------------|---------|
| **Abbreviation**    | As     |
| **Titles**          | "the Veiled Killer" → "the Shadow's Edge" → "the Eternal Shadow" (at high skill) |
| **Stat Modifiers**  | +0 Str, +2 Int, +8 Dex (Dex focus for stab/throw/EV; Int minor for Nyxara gifts) |
| **Total Starting Skill Points** | ~13 (balanced like Brigand/Stalker; adjusted by species aptitudes) |
| **Recommended Species** | Spriggan (+stealth/EV), Vampire (+Dex/stab), Felid (pure stealth, but no throwing — auto-swaps darts for extra blades), Barachi (+Dodging), Naga (venom synergy), Human (versatile) |
| **Restricted Species** | Felids swap darts for +1 Short Blades skill (can't throw) |
| **Playstyle**       | Stealth approach → stab/throw drowsy foes → Whisper Step away. Early game: Darts for kiting, blades for cleanup. Scales to Nyxara capstone. |

**Starting Skills** (XL 1; apt-adjusted)  
Inspired by Brigand (stealth/darts/blades) but pure stealth focus (higher Stealth/Throwing/Dodging, less Fighting). No magic/Shapeshifting to differentiate from Stalker/Brigand variants.

| Skill          | Level | Notes |
|----------------|-------|-------|
| **Fighting**   | 1     | Basic HP/survivability |
| **Short Blades**| 4    | Dagger/rapier stabs; Nyxara synergy |
| **Throwing**   | 3     | Dart accuracy/damage |
| **Dodging**    | 2     | EV for unarmoured mobility |
| **Stealth**    | 5     | Core: High detection avoidance/stab bonus |

**Starting Equipment/Inventory**  
Light, shadowy kit for immediate stealth kills. Nyxara-themed (venom/curare darts, shadow potion proxy).

- **Weapons**: +2 venom dagger (or spriggan knife for Spr), 8 poisoned darts, 2 curare darts
- **Armour**: +0 leather armour, +0 cloak
- **Consumables**: Potion of invisibility, potion of poison, bread ration
- **Gold**: 20 pieces
- **Species swaps**: Felid → extra +1, +1 dagger (no darts); Troll → great mace? No, stick to blades/darts.

**Flavor Text** (for database/backgrounds.txt or similar)  
```
Assassin: You are a silent killer, trained to end lives from the veil. Darts fly true, blades whisper death.
```

**Background Speech/Notes**  
- Start message: "The shadows welcome you, blade of Nyxara."  
- High skill: "Nyxara watches. The Contract draws near."  

**Codebase Integration Notes** (Phase 1: Data Foundations — 0.34+ aligned)  
Backgrounds are hardcoded in C++ (not data files). Add via simple array/struct extensions (patterns from src/bg.cc, enum.h).

1. **enum.h** (or player-enum.h): Add to `job_type` enum:  
   ```
   JOB_ASSASSIN,
   ```  
   Update `NUM_JOBS` or array sizes (+1).

2. **bg.h / bg.cc** (key file; defines `background_def` array):  
   Add new entry to `const background_def backgrounds[] = { ... };`  
   ```
   {
       .abbrev = "As",
       .name = "Assassin",
       .title = "the Veiled Killer",
       .qual_name = "Assassin",
       .role = ROLE_STABBING,  // Custom or ROLE_MELEE
       .skills = {
           { SK_FIGHTING, 1 },
           { SK_SHORT_BLADES, 4 },
           { SK_THROWING, 3 },
           { SK_DODGING, 2 },
           { SK_STEALTH, 5 },
           { SK_NONE, 0 }  // Terminator
       },
       .body_armour = ARM_LEATHER_ARMOUR,
       .weapon1 = make_item(WPN_DAGGER, 2, 0, BRAND_VENOM),
       // ... items vector: add darts via item_def packs
       .stat_mods = { 0, 2, 8 },  // Str/Int/Dex
   },
   ```  
   - Items: Use `items.init()` patterns (e.g., dart stacks: `make_item(OBJ_MISSILES, MI_DART, 0, BRAND_POISONED)`).  
   - See Brigand entry for dart/robe/cloak examples.

3. **player-start.cc**: Auto-handled via `job_to_name(JOB_ASSASSIN)`; add to `create_character()` checks.  
4. **database/job_title.txt**: Add titles.  
5. **Species restrictions**: In `species.cc`, Felid: `if (job == JOB_ASSASSIN) swap_darts_for_blade();` (simple if).  
6. **Build/Test**: Compile → new combo in char select (e.g., SpAs, VsAs). Verify skills/gear in @.

Fully reuses existing systems (skill bonuses from `calc_skill_level()`, item gen from `item_factory.cc`). No Lua/data needed. Balance: Strong early stealth (clears D:1-5 invis/stab), but fragile — fits Nyxara piety grind.