### Assassin King's Regalia Set (Toned-Down OP Version)

**Set Overview**  
The **Assassin King's Regalia** is Vesperian's stolen hoard — a complete, overpowered unrandart set manifesting on his death (boss loot + god message). Individually top-tier for stealth/stab builds (+20 slaying, strong resists/EV/stealth); full equip (9 pieces) triggers **"Shadow Sovereign"** set bonus: Elite buffs like enhanced invis (low light), +50% stab damage, +40 EV, self+adj silence aura, rN+++/rPois, swiftness-like speed. Lore: Forged by Nyxara for her heir; Vesperian corrupted them, but reunited they dominate shadows without breaking reality. Fully equipable by humans (standard slots: weapon, body, helmet, cloak, gloves, boots, amulet, 2 rings — robe for stealth, no shield/large weapons).

| Aspect              | Details |
|---------------------|---------|
| **Set Pieces**      | 9 (weapon, body, helmet, cloak, gloves, boots, amulet, 2 rings) |
| **Rarity**          | Unique (boss drop only; no shops/randarts) |
| **Theme**           | Peak shadow assassin: +stealth/stab/EV, conditional invis/venom, anti-detection |
| **Individual Power**| Zot5/Hell tier (e.g., +20 slaying, rF++/rC++, summons on hit) — strong, not infinite |
| **Set Bonus**       | All 9 equipped → "You claim the Shadow Throne." (+40 EV, +50% stab, low-light invis, aura silence, rN+++/rPois, +20% speed) |
| **Crawl Integration**| Unrand enum; equip hook; BASE_BODY (human-viable) |

**Piece-by-Piece Design** (dat/artefact/nyxara_regalia.txt — new file)  
Maxed within bounds: +12 weapons, +20 EV/slayers, standard brands/resists. Trivializes branches, challenges extended endgame.

| Piece | Name | Slot | Plus/Stats | Properties/Brand | Flavor |
|-------|------|------|------------|------------------|--------|
| **Weapon** | **Veilpiercer** | Right hand (quick blade) | +12, +20% accuracy, +20 slaying | Venom brand; +50% stab vs sleeping/distracted; ignores 50% SH | "The Usurper's blade hungers for the unwary." |
| **Armour** | **Robe of the Infinite Veil** | Body (robe) | +10 AC, +15 EV | rF++/rC++/rPois/rN++; +15 stealth; shadow cloud on hit (10% chance) | "Nyxara's veil, swallowing light." |
| **Helmet** | **Crown of the Shadow Sovereign** | Helmet | +8 AC, +10 EV | See invis; +20% MP regen; rTorment; +5 Int/Dex | "The void's gaze pierces illusions." |
| **Cloak** | **Mantle of Eternal Night** | Cloak | +12 EV, +20 stealth | Invis+ (15% chance/turn in low light); rCorr; self-silence | "Night's embrace muffles all." |
| **Gloves** | **Gauntlets of the Veiled Hand** | Gloves | +10 EV, +10 Dex | +25% stab bonus; +throwing speed (darts 0.8x delay) | "Whispers that fell kingdoms." |
| **Boots** | **Treads of the Silent Sovereign** | Boots | +12 EV, +15 stealth | Swiftness (non-expiring, 10-turn duration); no drown; +10% speed | "Steps that defy the ear." |
| **Amulet** | **Locket of Nyxara's Heir** | Amulet | +10 Int/Dex, +Will++ | +MP regen; summon shadow on kill (1d3, 20% chance); rMut | "Her pulse summons the dark." |
| **Ring 1** | **Ring of the Veil's Fury** | Ring | +15 slaying, +15 stealth | rElec/rPois; backstab on thrown darts | "Veiled wrath strikes unseen." |
| **Ring 2** | **Ring of the Shadow Throne** | Ring | +15 EV, +Will++ | rN++; blink (5-turn CD, short range); +10 stealth | "The throne's shadow pulls you." |

**Set Bonus: "Shadow Sovereign"** (Custom effect in player.cc/item.cc)  
- **Triggers**: `if (all 9 scan_artefacts() && equipped)`  
- **Effects**:  
  | Buff | Details |
  |------|---------|
  | **Veiled Invisibility** | 50% invis chance in low light (<50% illum.); stacks with abilities |
  | **Stab Supremacy** | +50% stab damage; +25% vs alert foes |
  | **Evasion Mastery** | +40 EV (flat bonus) |
  | **Silence Aura** | Silence self + adjacent tiles (permanent while worn) |
  | **Resist Veil** | rN+++/rPois; +rCorr |
  | **Shadow Grace** | +20% move speed; +1 shadow level (Dith-like) |
- **Display**: `@: Shadow Sovereign enthroned.` (status); set counter in desc ("5/9 pieces").

**Lore/Flavor Text** (dat/artefact/nyxara_regalia.txt)  
```
The Assassin King's Regalia: Vesperian's betrayal ended. Don all to rule the veil.
Veilpiercer: It thirsts for silent kills.
(etc.)
Set: The throne is yours. Shadows serve.
```

**God Messages** (on full equip):  
- "Nyxara crowns you Sovereign. The veil is yours." (+ piety to max)  

**Codebase Integration Notes** (Phase 3/4: Unrands + Hooks — 0.34+ aligned)  
Reuses unrand props (e.g., like Wrath of the Tempest or Maxwell's set).

1. **artefact.h**:  
   ```
   UNRAND_VEILPIERCER = NUM_UNRANDARTS,
   UNRAND_INFINITE_VEIL,  // robe
   UNRAND_SHADOW_CROWN,   // helm
   UNRAND_ETERNAL_NIGHT,  // cloak
   UNRAND_VEILED_HAND,    // gloves
   UNRAND_SILENT_TREADS,  // boots
   UNRAND_NYXARA_LOCKET,  // amulet
   UNRAND_VEIL_FURY,      // ring1
   UNRAND_SHADOW_THRONE,  // ring2
   NUM_UNRANDARTS += 9,
   ```

2. **artefact.cc**: `artefact_properties()`:  
   ```
   case UNRAND_VEILPIERCER:
       return { PARM_BRAND_VENOM, PARM_SLAYING_PLUS20, PARM_STAB_BONUS };
   // Similar for others: PARM_EVASION_INTEREST, PARM_STEALTH, PARM_INVIS_CHANCE
   ```

3. **player.cc**: `calc_move_delay()` / `update_vision()` hooks for bonuses; `you.set_has_full_regalia()` flag.

4. **describe.cc**: `"Part of the Regalia (%d/9 equipped)"`.

5. **mon-death.cc** (Vesperian):  
   ```
   vector<unrand_enum> regalia = {UNRAND_VEILPIERCER, ...};
   for (auto unr : regalia) items.add(generate_unrand(unr));
   ```

6. **Files**: dat/artefact/nyxara_regalia.txt; source/artefact.cc updates.  

Balance: OP reward (e.g., ~+150 EV total, venom quick blade BiS) — steamrolls Pan/Zot, viable Hell, but summon-heavy foes/mirrors still threaten. Fun mod power fantasy without crashes.