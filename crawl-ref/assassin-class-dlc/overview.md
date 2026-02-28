Here is a complete, self-contained **overview** of the "Assassin King" DLC/mod concept we have built together for Dungeon Crawl Stone Soup (DCSS). Everything is designed to fit reasonably within the existing codebase structure (data files + targeted C++ extensions), reusing existing systems (stealth, piety, stabbing, unrandarts, portal vaults, god abilities, etc.) while adding a coherent late-game assassin prestige path.

### Core Concept
A full thematic extension centered around **stealth assassination** as an endgame power fantasy.  
- New background: **Assassin** (As)  
- New god: **Nyxara, the Eternal Shadow**  
- Capstone mission at XL 27 + max piety: **The Final Contract** — assassinate the boss **Vesperian the Usurper** in a custom stealth realm  
- Reward on success: Full **Assassin King's Regalia** set (9-piece overpowered unrandart set)  
- Full set bonus: **Shadow Sovereign** — elite late-game buffs rewarding pure assassin playstyle

### 1. Background: Assassin (As)
- **Focus**: Pure stealth/stab/throwing build  
- **Starting stats**: +0 Str / +2 Int / +8 Dex  
- **Starting skills**:
  - Fighting 1
  - Short Blades 4
  - Throwing 3
  - Dodging 2
  - Stealth 5  
- **Starting gear**: +2 venom dagger / quick blade variant, stack of poisoned darts + curare darts, leather armour, cloak, potion of invisibility  
- **Flavor**: Silent killer trained in Nyxara's veiled order, destined for the Final Contract  
- **Best species**: Spriggan, Vampire, Felid (with dart swap), Naga, Human  
- **Code location**: bg.cc / player-start.cc + dat/database/job_title.txt

### 2. God: Nyxara, the Eternal Shadow
- **Alignment**: Chaotic / Neutral (stealth purity)  
- **Symbol**: Black dagger piercing a veiled eye  
- **Favoured weapons**: Daggers, short blades, darts  
- **Piety sources**:
  - Backstab kills (+3×HD)
  - Undetected stealth kills (+2×HD)
  - Noiseless / sleeping kills (+1)
  - Penalties: Alerting guards, noise, heavy armour, light sources  
- **Abilities** (scale with piety stars):
  - * Veil of Shadows (passive stealth/EV in low light)
  - ** Whisper Step (short shadow blink + silence chance)
  - *** Silent Veil (invis + silence aura)
  - **** Shadow Cascade (multi shadow dart throw, venom)
  - ***** Eclipse Mark (mark target for stab/shadow damage bonus)
  - ****** The Final Contract (XL 27+ only — mission portal)  
- **Gifts**: Venom darts, cloak of shadows, quick blade, amulet of veil  
- **Flavor**: Ancient patron of perfect unseen death; betrayed by Vesperian who stole her regalia  
- **Code location**: god-type.h, religion.cc, god-abil.cc, piety_gain_on_kill.cc, dat/religion/nyxara.txt

### 3. The Final Contract (Capstone Mission)
- **Trigger**: XL 27 + ****** piety → accept divine ability (0 MP, once)  
- **Consequence of refusal**: Major piety loss (down to ***)  
- **Realm**: **Shadow Fortress** — one-way portal vault (dark, multi-room stealth gauntlet)  
- **Objective**: Assassinate **Vesperian the Usurper** (preferably stealth kill)  
- **Stealth scoring**: Undetected kills give points; high score (80+) → bonus loot  
- **Realm features**:
  - Dark floors (+stealth)
  - Patrol groups (Shadow Thugs)
  - Snipers (Veil Dartists)
  - Alarm wards
  - Shadow pools / blind spots
  - Boss throne chamber (Vesperian starts asleep)  
- **On success**: Regalia set manifests on corpse + exit portal  
- **Code location**: ability.cc (portal spawn), mon-death.cc (boss death hook), dat/des/portal_vaults/nyxara_fortress.des + Lua for patrols/score

### 4. Boss: Vesperian the Usurper
- **HD / Speed**: 25 / 12  
- **AC / EV / SH**: 18 / 28 / 12 (SH halved vs backstabs)  
- **Attacks**: Dual venom stabs + shadow blink  
- **Abilities**: Summon shades, invis, high noise detection  
- **Weaknesses**: Starts asleep (prime stab window), weaker in shadows, rPois-  
- **AI**: Patrols throne; wakes on noise/LOS; alerts summon swarm  
- **Flavor**: Nyxara's former champion turned usurper, sleeping on stolen throne  
- **Code location**: mon-data.h, dat/monsters/unique/shadow_kings.txt

### 5. Assassin King's Regalia (9-piece unrandart set)
**Pieces** (all human-equippable, no shield/large weapon to preserve stab theme):

| Slot          | Name                           | Key Properties                                      | Power Level |
|---------------|--------------------------------|-----------------------------------------------------|-------------|
| Weapon        | Veilpiercer                    | +12, +20 slaying, venom, +50% stab vs sleeping     | BiS quick blade |
| Body          | Robe of the Infinite Veil      | +10 AC / +15 EV, rF++/rC++/rPois/rN++, +15 stealth | Top-tier robe |
| Helmet        | Crown of the Shadow Sovereign  | See invis, +20% MP regen, rTorment, +5 Int/Dex     | Strong utility |
| Cloak         | Mantle of Eternal Night        | +12 EV / +20 stealth, invis chance (low light), rCorr | Stealth cloak |
| Gloves        | Gauntlets of the Veiled Hand   | +10 EV / +10 Dex, +25% stab, faster dart throws    | Stab gloves |
| Boots         | Treads of the Silent Sovereign | +12 EV / +15 stealth, swiftness, no drown          | Mobility boots |
| Amulet        | Locket of Nyxara's Heir        | +10 Int/Dex, MP regen, summon shadow on kill chance, rMut | Utility amulet |
| Ring 1        | Ring of the Veil's Fury        | +15 slaying / +15 stealth, rElec/rPois, dart backstab | Slaying ring |
| Ring 2        | Ring of the Shadow Throne      | +15 EV / +Will++, rN++, short blink (CD), +10 stealth | Evasion ring |

**Set Bonus: Shadow Sovereign** (all 9 equipped)
- +40 EV (flat)
- +50% stab damage (+25% even vs alert)
- 50% invis chance in low light
- Silence aura (self + adjacent tiles)
- rN+++ / rPois / +rCorr
- +20% move speed
- +1 shadow level (Dithmenos-like)
- Flavor: "You claim the Shadow Throne."

**Code location**: artefact.h/cc, player.cc (equip check), mon-death.cc (drop on boss kill), dat/artefact/nyxara_regalia.txt

### Overall Power Curve & Playstyle
- Early → Mid: Standard stealth assassin (darts + stabs + invis pots)  
- Mid → Late: Nyxara piety grind (clean backstabs, no alerts) → abilities ramp up  
- Endgame climax: XL 27 + ****** piety → accept Contract → stealth realm → boss assassination  
- Reward: Regalia set turns character into one of the strongest possible stealth builds in extended (very strong but not literally invincible)

### Development Structure Summary
- Data files: backgrounds, religion, monsters, vaults, artefacts, speech  
- C++ extensions: god enum/abilities/piety, mission flag & portal spawn, boss death hook → regalia drop, set bonus equip check  
- Vault Lua: Patrols, sleep state, stealth scoring, conditional rewards  
- Fully reuses existing systems: stealth/noise, stabbing, unrand framework, portal vaults, god piety/abil patterns

This gives a complete, thematic, late-game prestige path that feels like a natural (if very powerful) extension of DCSS assassin fantasy.