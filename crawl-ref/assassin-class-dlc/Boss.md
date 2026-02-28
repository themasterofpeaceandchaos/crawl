### Vesperian the Usurper & Shadow Fortress Vault

**Boss & Vault Overview**  
Vesperian is Nyxara's traitorous champion, self-proclaimed "Shadow King" hoarding her regalia in a veiled fortress beyond the Dungeon. A pinnacle stealth boss: high perception/EV, summons minions, patrols throne room — but sleeps initially (prime stab window), vulnerable to light/poison/backstabs. Minions enforce stealth: patrols, scouts, alarms.  

Vault: **Nyxara's Shadow Fortress** — portal-accessed realm (one-way in, exit on boss death). Dark, multi-room layout rewards stealth: patrol paths, blind spots, shadow pools (+stealth). Alerting = suicide (boss wakes + swarms). Success: Boss corpse drops **Assassin King's Regalia** set.  

Aligned to 0.34 "Doomed Geometries" codebase (current stable per develz.org/downloads).

| Aspect          | Details |
|-----------------|---------|
| **Boss HD**     | 28 (Zot5-tier: tough but stab-able) |
| **Boss Speed**  | 13 (fast EV dodger) |
| **Minions**     | 8-12: Shadow Thugs (melee), Veil Dartists (ranged), Betrayer Shades (summoned) |
| **Vault Size**  | 10x12 map chunk (standard portal_vault) |
| **Difficulty**  | Stealth S-rank: Invis/stab/darts to isolate → boss backstab chain |
| **Rewards**     | Full Regalia set (OP unrandarts) + exit portal |

**Lore/Flavor Text** (dat/monsters/unique/shadow_kings.txt)  
```
Vesperian the Usurper: Once Nyxara's blade, now her betrayer. He slumbers on a throne of shadows, guarded by false heirs. Strike true—or wake the storm.
```

**Boss Speech** (database/monspeak/vesperian.txt)  
- Wake: "Intruder! The veil betrays you!" (summons swarm)  
- Low HP: "Nyxara's pawn... you dare?"  
- Death: "The shadows... claim me..." (regalia manifests)  

**Vesperian Monster Definition** (dat/monsters/unique/shadow_kings.txt — new file)  
```
--------------------------------------------------------------
VESPERIAN
--------------------------------------------------------------
vesperian the usurper                  (U) n  28    1
a vesperian                            (U) m  28    1
the shadow sovereign                   (U) m  28    1
--------------------------------------------------------------
 HD 28    sp  13    AC  22    EV  25    SH  15
Attacks:  50%  stab  50   1d18 (roll 2d9)
          50%  venom 50   1d18 (roll 2d9)
          100%  auxiliary  stab  50   1d18 (roll 2d9) [offhand]
--------------------------------------------------------------
Speed 13  | See Invis : Yes  | See Inv. : Yes | Shadow : Yes
Resist: Poison 100% | Rot 100% | Drown 100% | Stab 75%
Int: High | HD: 28 | XP: 20000 | Size: Medium
Intelligence: high
--------------------------------------------------------------
M: asleep (till noise) | patrol | royalty
A: summon_butterflies "shadow shade" 50% (4)
  shadow_blink 33%
  invis 25%
--------------------------------------------------------------
```

- **Key Features**: Dual-wield stabs (high dice for XL27), summons "shadow shade" (new minion or reskin nightstalker). Asleep flag (wakes on noise >10 or LOS). Patrols throne (MONS_PATROL flag). SH15 blocks frontal, but backstabs pierce (custom stab bonus).  
- **Minions** (add to same file or new shadow_assassins.txt):  
  - Shadow Thug: HD10, melee stab, patrol groups.  
  - Veil Dartist: HD8, throwing darts (venom), high see invis.  

**Shadow Fortress Vault** (dat/des/portal_vaults/nyxara_fortress.des — new file)  
Multi-room stealth gauntlet. Uses standard .des syntax (map chars + O = monster place + tags). Lua for patrols/dynamic boss sleep.

```
# Nyxara's Shadow Fortress - Stealth Assassin Challenge
# One-way portal entry; exit on boss death
PLACE: nyxara_fortress

; entry room: dark corridor
....................
......xx......xx....
......xP......x....
..xx..x........x..xx  P=player start (post-portal)
..x....x......x....
......x........x....
......xx......xx....

; patrol hallway
................
.xM.....x.M.x...  M=shadow_thug (patrol tag)
.x.......x.....x
.xM.....x...M.x.
................

; trap room: alarm wards
........
xW.xW.x  W=ward trap (alert on step)
x...x...
xW.xW.x

; boss throne: dark, asleep
.............
xx........xx
x..Vttt..x  V=vesperian (unique, asleep tag)
x..###..x  #=throne (statue feat)
x..ttt..x  t=veil_dartist
xx........xx
.............

# Lua for patrols/boss
LUA: setup_patrols("thug_patrol", "M", 4)
LUA: set_boss_asleep("V", true)
LUA: on_boss_death("V", "spawn_regalia(); place_exit_portal();")
MAP: join rooms with dark floors (low light)
```

- **Map Chars**: x=wall, .=dark floor (+stealth), M=monster tag (thug), V=unique vesperian, P=portal entry.  
- **Lua Hooks** (0.34 vaults use Lua extensively): Patrol AI (wander/shout on alert), boss sleep (wake on noise), death → rewards/exit.  
- **Features**: Shadow clouds (feat: shadow), alarm traps (noise gen), no light sources (extinguish on place).

**Codebase Integration Notes** (Phase 3: Custom Logic — 0.34 aligned)  
1. **Monster Enum/Data** (source/mon-enum.h):  
   ```
   MONS_VESPERIAN = MONS_SHADOW,  // After last unique
   MONS_SHADOW_THUG,
   MONS_VEIL_DARTIST,
   ```
   **mon-data.h**:  
   ```
   { { MONS_VESPERIAN, 28, 13, 22, 25, 15, ... }, M_PATRAL | M_SEE_INVIS | M_ROYALTY },
   ```

2. **Vault Placement** (ability.cc — Final Contract):  
   ```
   if (you.experience_level >= 27 && player_has_max_piety(you.religion)) {
       you.attribute[ATTR_NYXARA_CONTRACT] = 1;
       place_vault("nyxara_fortress", 100, 0, false, you.pos() + coord_def(5,5));  // Near player
       mpr("The veil rends! Enter the fortress.");
   }
   ```

3. **Mission Logic** (mon-death.cc — mons_die() hook):  
   ```
   if (mons_is(mons, MONS_VESPERIAN) && you.attribute[ATTR_NYXARA_CONTRACT]) {
       // Stealth metric? if (stealth_kill_bonus > 50%) extra loot
       create_regalia_set(grd(you.pos()));
       place_vault("exit_portal", 100, 0);  // Or feat: PORTAL
       you.attribute[ATTR_NYXARA_CONTRACT] = 0;
       simple_god_message("The King falls. Claim your crown.", GOD_NYXARA);
   }
   ```

4. **Build/Files**:  
   - dat/monsters/unique/shadow_kings.txt  
   - dat/des/portal_vaults/nyxara_fortress.des  
   - Recompile: make → test portal spawn, boss fight.  
   - Milestone: Add to milestone.txt for "killed Vesperian".

Fully reuses: Unique framework (e.g., like Geryon), portal_vaults (e.g., zotdef_helix.des templates), Lua (standard since 0.20+). Balance: XL27 + max piety = winnable with 80% stealth success.