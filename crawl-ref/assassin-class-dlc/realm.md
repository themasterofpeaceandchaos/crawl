### Refined Mission: The Final Contract & Shadow Fortress Realm

**Mission Overview**  
The Final Contract is Nyxara's capstone trial: At XL 27 + maximum piety (******), the player accepts a divine hit mission to assassinate Vesperian the Usurper in his veiled Shadow Fortress. This is a stealth-focused endgame challenge emphasizing assassin playstyle — backstabs, invis, darts, avoiding alerts. Success rewards the full Assassin King's Regalia set (as refined). Refusal: Major piety loss (to ***).  

Refinements from initial:  
- **Stealth Emphasis**: Added stealth metrics for bonus rewards (e.g., perfect no-alert kill → extra piety/gift).  
- **Difficulty Tuning**: Boss/minions balanced for XL27 (tough but fair; alerts ramp up minions).  
- **Realm Layout**: Expanded vault for more tactical depth (rooms, patrols, exploits).  
- **Integration**: Ties tighter to Nyxara abilities (e.g., Eclipse Mark auto-targets boss).  
- **Scope**: Stays in codebase (vault des + minor hooks; no new mechanics beyond existing stealth/noise/stab).

| Aspect          | Details |
|-----------------|---------|
| **Trigger**     | God ability: "The Final Contract" (0 MP, once; XL27 + ****** piety check) |
| **Entry**       | Accept → temporary portal spawns (5-turn window; one-way). "The veil rends — enter swiftly!" |
| **Objective**   | Assassinate Vesperian (stealth kill encouraged; full alert viable but 3x harder) |
| **Failure**     | Death/exit without kill: Piety to *; portal collapses. Retry after piety regain. |
| **Success Metrics** | Stealth score (0-100): Based on undetected kills, no alerts. 80+ → bonus unrand (e.g., extra ring variant). |
| **Duration**    | Self-contained vault (no branches; 1-2 floors equivalent). |
| **Lore Tie-in** | God messages guide: "Strike the sleeper... avoid the watch." |

**Refined Piety/Ability Tie-ins**  
- **Pre-Mission**: At max piety, Nyxara hints: "Vesperian's throat calls. Prepare your veil."  
- **During**: Nyxara abilities boosted in realm (+20% duration; e.g., Silent Veil lasts 8 turns). Eclipse Mark auto-applies to Vesperian if in LOS.  
- **Post-Success**: "The betrayer falls. Rise, my Sovereign." (Regalia manifests on corpse; piety locked at ****** forever).  

**Refined Vesperian the Usurper (Boss Unique)**  
Toned for challenge: High EV/perception, but stealth exploits (initial sleep, shadow-blind spots).  

| Stat            | Value | Notes |
|-----------------|-------|-------|
| **HD/Speed**    | 25 / 12 | Zot-tier speed; dodgy but stab-vulnerable |
| **AC/EV/SH**    | 18 / 28 / 12 | High EV; SH halved vs backstabs |
| **Attacks**     | 2x stab (1d20 + venom); auxiliary shadow blink | Venom drains MP; blinks on alert |
| **Abilities**   | Summon shades (3-5 on wake); invis (50% turn); detect noise (high threshold) | Summons: HD15 shades (stealthy melee) |
| **Weaknesses**  | Asleep start (stab x3); -EV in shadows; rPois- (dart synergy) | Stealth kill: No summons if undetected |
| **XP/Loot**     | 15000 / Regalia set | Corpse: All 9 pieces + gold pile |

- **AI Refinements**: Patrol throne (standard patrol flag); wakes on noise >8 or LOS without invis. On alert: Shouts ("Thief in the veil!") → realm-wide minion rush.  
- **Flavor**: "A shadowed figure slumbers on a throne of whispers, his stolen crown aglow."  

**Refined Minions & Guards**  
8-15 placed + summons: Mix for stealth gauntlet (patrols, scouts, static wards).  

| Minion Type     | HD/Speed | Abilities | Placement |
|-----------------|----------|-----------|-----------|
| **Shadow Thug** | 12 / 10 | Melee stab; patrol AI | Groups of 3 in halls; shouts on detect |
| **Veil Dartist**| 10 / 11 | Venom darts; see invis | Snipers in overlooks; isolated |
| **Betrayer Shade** | 8 / 14 | Invis; summon self (chain) | Boss summons only; ethereal (rN needed) |

- **Refinements**: Minions have noise thresholds (thugs loud, shades silent). Killing undetected: +piety (ties to god). Alerts cascade (one shout wakes nearby).  

**Refined Shadow Fortress Vault** (dat/des/portal_vaults/nyxara_fortress.des)  
Expanded to 15x20 multi-room layout: Entry corridor → patrol halls → trap antechamber → boss throne. Dark tiles (+stealth); exploits like shadow pools (hide spots), chokepoints for darts. Lua for dynamics.  

```
# Nyxara's Shadow Fortress - Refined Stealth Realm
PLACE: nyxara_fortress
ORIENT: enclose  # Walled pocket realm

# Entry: Dark veil corridor (player start)
SUBVAULT: entry_corridor
MAP
xxxxxxxxxxxxxxx
x.............x
x.P...........x  P=player (post-portal)
x.............x
xxxxxxxxxxxxxxx
ENDMAP

# Patrol Halls: Twisting paths with thugs
SUBVAULT: patrol_halls
MAP
xMx.xMx.xMx.x
x...x...x...x  M=shadow_thug (patrol tag, group=3)
xMx.xMx.xMx.x
ENDMAP

# Trap Antechamber: Wards + dartists
SUBVAULT: trap_room
MAP
xWxDxWx
x...x..  W=alarm ward (noise trap), D=veil_dartist (sniper)
xWxDxWx
ENDMAP

# Boss Throne: Shadowed chamber
SUBVAULT: boss_throne
MAP
xxxxxxxxxxxxxxx
x...ttt...ttt.x  t=betrayer shade (static)
x..V###..ttt.x  V=vesperian (asleep, patrol limited)
x...ttt...ttt.x
xxxxxxxxxxxxxxx
ENDMAP

# Connections: Doors/shadows
KFEAT: . = floor_dark (low light +stealth)
KFEAT: x = wall_shadow (illusory, passable with invis)

# Lua Refinements
LUA: setup_patrols("thug_group", "M", 3, "loop_path")  # Circular patrols
LUA: set_mon_asleep("V", true)  # Initial sleep
LUA: on_alert("any", "wake_boss(); summon_swarm(5)")  # Cascade
LUA: track_stealth_score("undetected_kills")  # For bonus
LUA: on_boss_death("V", "drop_regalia(); place_exit_portal(); award_bonus(stScore >80 ? extra_gift() : none)")
```

- **Vault Refinements**: Subvaults for modularity (easy expand). Dark floors: Illum <30% (+invis chance). Traps: Alarm wards shout on trigger. Exit: Portal spawns on boss death (or escape hatch if partial success).  
- **Stealth Score Calc**: Lua hook: +10 per undetected kill, -20 per alert. Displayed on entry: "Nyxara judges your veil (0/100)."  

**Codebase Integration Notes** (Phase 3: Mission Logic — 0.34+ aligned)  
1. **ability.cc** (Final Contract):  
   ```
   if (accept) {
       you.attribute[ATTR_NYXARA_MISSION] = 1;  // Flag active
       place_vault("nyxara_fortress", you.pos() + coord_def(2,2));  // Nearby portal
   } else piety_loss(100);  // To ***
   ```

2. **mon-death.cc** (Boss hook):  
   ```
   if (killer_is_player() && mons == MONS_VESPERIAN && you.attribute[ATTR_NYXARA_MISSION]) {
       int stScore = lua_getglobal("stealth_score");  // From vault Lua
       drop_regalia_set();  // All pieces
       if (stScore >= 80) god_gift_item(GOD_NYXARA, "bonus_venom_darts");  // Extra
       place_feat(DNGN_PORTAL_EXIT);  // Safe out
       you.attribute[ATTR_NYXARA_MISSION] = 0;
       simple_god_message("Well struck, Sovereign.");
   }
   ```

3. **vault.cc / luavaults.cc**: Add Lua funcs (e.g., `lua_stealth_score()` tracking `env.alerts` + `kill_flags`).  
4. **Files**: dat/des/portal_vaults/nyxara_fortress.des (expanded); minor source hooks.  
5. **Test/Balance**: Playtest stealth paths (invis route: 70% success); alert mode: Swarm like Pan lord.  

This refines the mission into a cohesive, replayable climax — true assassin fantasy without over-scope. Rewards pure stealth with extras, but allows brute force at cost.