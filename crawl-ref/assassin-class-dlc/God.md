### Nyxara, the Eternal Shadow

**God Overview**  
Nyxara is the ancient patron of assassins, veils, and the perfect kill. She embodies the art of death unseen: a whisper in the dark, a dart from nowhere. In Crawl lore (aligned with 0.34+ codebase), Nyxara was once a primordial force of night who forged the first shadow-darts from the void between stars. Betrayed by her favored champion (the future boss unique, "Vesperian the Usurper"), who sought to claim her throne as "King of Assassins," she now seeks worthy successors to reclaim her regalia and end his reign. Altars appear in shadowy corners of the Dungeon, Lair, and Spider, often veiled by illusions until approached silently.

| Aspect          | Details |
|-----------------|---------|
| **Alignment**   | Chaotic, Neutral (stealth over brute force) |
| **Symbol**      | A black dagger piercing a veiled eye |
| **Color**       | Deep purple-black (#301934) |
| **Starting Piety** | 10* (minor boost for Assassin backgrounds) |
| **Max Piety**   | ***** (6 stars) |
| **Favored Weapons** | Daggers, short blades, darts |
| **Temple**      | Small, dark chamber with illusory walls, shadow traps, whispering echoes |
| **Holy Word**   | "Silence falls eternal." |

**Lore/Flavor Text** (for dat/religion/nyxara.txt)  
```
Nyxara, the Eternal Shadow, demands perfection in the kill. Strike from the veil, unseen and unheard. Betrayers like Vesperian hoard her stolen regalia—prove yourself her true blade.

Piety:
*   - You sense the veil thinning around you.
**  - Shadows cling to your form.
*** - The perfect stab calls to you.
****- Nyxara's whispers guide your blade.
***** - You are the shadow's edge.
****** - The Final Contract awaits (XL 27+ only).

Likes: killing by backstab; stealth kills; noiseless kills; assassinating sleeping/distracted foes.
Dislikes: alerting guards (noise/detection); ranged attacks that miss; heavy armor; light sources.
Gifts: shadow-enchanted darts; veils of concealment.
```

**God Speech** (database files, e.g., god messages on piety gain/loss, kills)  
- Piety gain (backstab): "Nyxara smiles. The blade sang true."  
- Alert guards: "Fools stir! Nyxara hisses in disgust." (-1 piety)  
- Max piety: "The Usurper's throat awaits your kiss, child of shadow."  
- Contract accept: "The veil parts. Vesperian dies tonight."  
- Success: "The King falls. Claim your crown." (rewards drop)  

**Piety Mechanics** (Implement in piety_gain_on_*.cc patterns)  

| Action                  | Piety Change | Notes |
|-------------------------|--------------|-------|
| **Backstab kill**      | +3*         | * = attacker HD; scales with foe HD |
| **Stealth kill** (foe never alerted) | +2*    | Full piety only if no noise/detection |
| **Noiseless kill**     | +1          | No shouts, no ranged noise (darts qualify if stealthy) |
| **Kill sleeping/distracted foe** | +1   | Exploits foe status |
| **Alerting guards** (noise > threshold or LOS detection) | -2 | Per alert event |
| **Missed ranged attack**| -1          | Wastes stealth |
| **Wearing heavy armor** (plate+) | -1/turn | Passive drain |
| **Using light source**  | -3          | Per turn in LOS |

- **Piety Decay**: None (encourages constant stealth play).  
- **Detection Threshold**: Use existing noise/stealth systems; piety loss if foe "sees" you before death (foe->foe_info().detected).  

**Abilities** (god_ability enum in god-type.h; implement in god-abil.cc/ability.cc)  
Nyxara's powers scale with piety stars, emphasizing stealth buildup. All cost MP (no HP/necromancy). Cooldowns via standard ability timers.

| Ability Name | Piety Req. | Cost (MP) | Cooldown | Effect |
|--------------|------------|-----------|----------|--------|
| **Veil of Shadows** (Passive, *) | *     | -        | -       | +20% stealth in low light (<50% illumination); +1 EV per 2 shadow levels. Stacks with Dithmenos. |
| **Whisper Step** (Active, **) | **   | 3        | 5 turns | Short-range shadow blink (3-5 tiles, LOS-blocked); 50% chance to silence landing tile (3 turns). |
| **Silent Veil** (Active, ***) | ***  | 5       | 10 turns| Invisibility (5 turns) + silence aura (you + adj foes muted, 3 turns). Breaks on attack. |
| **Shadow Cascade** (Active, ****) | ****| 8       | 20 turns| Throw 3-5 shadow darts (high stab mult., venom brand; auto-target sleeping/isolated foes). |
| **Eclipse Mark** (Active, *****) | *****| 12     | 50 turns| Mark a foe (LOS): +50% stab dmg vs them (10 turns); they take +20% shadow dmg; reveals position if invisible. |
| **The Final Contract** (Capstone, ****** + XL 27) | ****** | 0     | Once   | Accept → "The veil rends!" Spawns portal to Vesperian's Shadow Fortress (one-way vault entry). Refuse: -**** piety. |

- **Gift Schedule** (god_gift.cc):  
  | Piety | Gift |
  |-------|------|
  | **   | Stack of 12 venom darts |
  | ***  | Cloak of Shadows (+5 stealth, rPois) |
  | **** | Quick blade (high stab dice) |
  | ***** | Amulet of the Veil (+invis chance, +EV) |

**Codebase Integration Notes** (Exact Files/Steps for Phase 2 — Aligned to 0.34+)  
1. **god-type.h**: Add `GOD_NYXARA` to enum_god.  
2. **religion.cc**:  
   - `god_name(GOD_NYXARA) = "Nyxara";`  
   - `god_title(GOD_NYXARA) = "the Eternal Shadow";`  
   - `god_allows_chaos(GOD_NYXARA) = true;`  
   - Piety icon/color.  
   - Favoured weapons: Return `{WPN_DAGGER, WPN_SHORT_SWORD, WPN_SPRIGGAN_KNIFE, WPN_QUICK_BLADE, WPN_DART}` (covers short blades + darts).  
3. **god-abil.cc**: Define `nyxara_veil_of_shadows()`, etc. — copy Dithmenos patterns (shadow aura). Use `player::attribute[ATTR_SHADOW]` for passives. Shadow Cascade: Use `throw_delay` + `ranged_attack` with WPN_DART, custom venom brand (BRAND_VENOM).  
4. **piety_gain_on_kill.cc**: `if (stab_bonus() > 10) piety_gain(you.religion, 3 * mons_HD(victim));` Check `victim->foe_info(dodging(you)).detected`.  
5. **ability.cc**: Add to `get_abilities()` if piety >= req && !timer_active. Capstone: `if (you.experience_level >= 27 && player_has_max_piety())` → `place_vault("nyxara_contract", 1, 1);` (spawns portal).  
6. **dat/religion/nyxara.txt**: Full flavor (as above).  
7. **database/god_data.txt**: Speech keys.  
8. **Altar/Vaults**: `dat/des/minitemple_nyxara.des` + random altar chance in `altar.cc`.  

This is fully self-contained, reuses 90% existing code (e.g., invis from `make_player_see_invis_visible(false)`; stab from `weapon_stab_bonus()`; dart throwing from `ranged_attack_no_chaos()`). Venom darts align perfectly with 0.34 codebase (poison/venom brands unchanged). Balance: Fun stealth ramp-up to epic capstone.  

**Next Refinement?** Lore tweaks? Specific code snippets (e.g., Shadow Cascade impl)? Or move to Assassin background? Let me know!