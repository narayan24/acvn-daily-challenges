# Daily Challenges — An ACVN Story

A story package for the [ACVN Visual Novel Engine](https://github.com/narayan24/ACVN).

> **Adult content** — this story requires age verification on startup (`adultgame: yes`).

---

## What is this?

*Daily Challenges* is a slice-of-life visual novel built on the ACVN engine.  
You play as Alex Wren, navigating everyday life at home — mornings, family dynamics,
small choices, and the occasional unexpected situation.

- Multiple rooms: bedroom, bathroom, living room, kitchen, hallway, parents' room, brother's room
- Full NPC schedule system (family members move through the house based on time of day)
- Clothing system with inhibition gating
- Quest and journal system
- Phone with calls and messages

---

## Requirements

| Requirement | Details |
|-------------|---------|
| **ACVN Engine** | [github.com/narayan24/ACVN](https://github.com/narayan24/ACVN) — built and running |
| **Windows** | 10 or 11 |
| **.NET** | 9.0 SDK or newer |

---

## Installation

### Option A — Clone side-by-side (recommended for development)

```
# 1. Clone the ACVN engine
git clone https://github.com/narayan24/ACVN.git
cd ACVN

# 2. Clone this story into the story folder
git clone https://github.com/narayan24/acvn-daily-challenges.git story/daily-challenges

# 3. Build and run
dotnet run
```

On first launch, the ACVN story picker will list *Daily Challenges* automatically.

### Option B — Download ZIP

1. Download the [ACVN engine](https://github.com/narayan24/ACVN/archive/refs/heads/master.zip) and extract it
2. Download this repository as a ZIP and extract its contents into  
   `ACVN/story/daily-challenges/` — the folder must contain `config.json` directly
3. Run `dotnet run` inside the ACVN folder (or open `ACVN.sln` in Visual Studio and press F5)

---

## Adding images

Images are not included in this repository (personal art assets).  
Place your images in the matching subfolder under `images/` — the engine picks one at
random and falls back up the folder tree if a subfolder is empty.

```
images/
  home/
    room/               ← shown for the bedroom (any action)
      bed/
        naked/          ← shown specifically for the "sleep naked" action
    bathroom/
      shower/           ← shown during the shower scene
    livingroom/
    kitchen/
    brother/
    hallway/
    parentroom/
  chars/
    brother.jpg         ← NPC portrait
  clothes/
    bra_white.jpg       ← clothing item thumbnails
```

Supported formats: `.png .jpg .jpeg .webp .gif .bmp .mp4 .avi .mkv .wmv .mov`

---

## Story structure

```
daily-challenges/
├── config.json         Start room, language, adult-game flag
├── chars.json          MC + NPC definitions with attributes
├── items.json          Collectable items
├── clothes.json        Clothing catalogue (slots, inhibition, tags)
├── quests.json         Quest definitions with step descriptions
├── schedules.json      NPC daily timetables
├── style.css           Custom CSS for all rendered scenes
├── images/             Art assets (gitignored — add your own)
├── mods/               Drop mod folders here to extend the story
└── rooms/
    ├── intro.acvn      Title screen
    ├── start.acvn      Post-setup welcome
    └── home/
        ├── room.acvn         Bedroom
        ├── bathroom.acvn     Bathroom
        ├── livingroom.acvn   Living room
        ├── kitchen.acvn      Kitchen
        ├── hallway.acvn      Hallway
        ├── brother.acvn      Brother's room
        └── parentroom.acvn   Parents' room
```

---

## Mods

This story supports the ACVN mod system.  
Drop a mod folder into `mods/` — see the [ACVN mod documentation](https://github.com/narayan24/ACVN#mod-system) for the full format.

---

## Event Reference

Complete list of all random and conditional events in the story, grouped by location.
Each entry shows what triggers the event and what it does.

**Key variables:**
- `inhibition` — starts at 90 (very inhibited), decreases over time; lower = more daring
- `relationship` — per-character relationship score
- `mood` — per-character mood (affects availability of some options)
- `clothing_state` — `"dressed"` / `"underwear"` / `"naked"`
- `vars.*` — daily flags (reset on `reset_daily` / new day)
- `random_int(1, N)` — uniform roll from 1 to N inclusive

---

### Home — Hallway (`rooms/home/hallway.acvn`)

| Event | Trigger conditions | Effect |
|---|---|---|
| Family encounter while underdressed | `clothing_state != "dressed"` **and** `vars.hallway_enc_done == 0` **and** `random_int(1,3) == 1` (33 %) | Rolls who is home: 1 = Robert, 2 = Sandra, 3 = Thomas |
| ↳ Robert — warm reaction | State `"underwear"` **and** `robert.relationship >= 30` | Approving look, mild tension |
| ↳ Robert — embarrassed reaction | State `"underwear"` **and** `robert.relationship < 30` | Awkward retreat |
| ↳ Robert — controlled reaction | State `"naked"` **and** `robert.relationship >= 30` | Composed, lingers a moment |
| ↳ Robert — backing away | State `"naked"` **and** `robert.relationship < 30` | Stumbles back, flustered |
| ↳ Sandra — amused | State `"underwear"` **and** `sandra.mood >= 65` | Light teasing comment |
| ↳ Sandra — disapproving | State `"underwear"` **and** `sandra.mood < 65` | Firm look |
| ↳ Sandra — quiet laugh | State `"naked"` **and** `sandra.mood >= 65` | Turns away smiling |
| ↳ Sandra — firm boundary | State `"naked"` **and** `sandra.mood < 65` | Stern tone |
| ↳ Thomas — uncomfortable retreat | State `"underwear"` **and** `thomas.mood >= 55` | Backs out quietly |
| ↳ Thomas — firm directive | State `"underwear"` **and** `thomas.mood < 55` | Sharp request to get dressed |
| ↳ Thomas — avoidance | State `"naked"` | Unconditional; leaves immediately |

---

### Home — Bathroom (`rooms/home/bathroom.acvn`)

| Event | Trigger conditions | Effect |
|---|---|---|
| Robert walks in while showering | Robert is home **and** `random_int(1,100) > 65` (35 %) **and** `!vars.shower_interrupted_today` | Choice branch based on inhibition |
| ↳ Kick him out | Always available | −5 mood both; Robert leaves embarrassed |
| ↳ Freeze | `inhibition < 60` | +8 horny (mc); Robert quietly leaves; +5 relationship |
| ↳ Tease him | `inhibition < 30` | +10 horny (mc), +15 horny (Robert); Robert flustered |
| ↳ Invite him to stay | `inhibition < 30` | +20 horny (mc), +25 horny (Robert), +10 mood (Robert) |
| Positive mirror moment | `random_int(1,3) == 1` (33 %) | +8 mood, +1 attractiveness, +5 horny |

---

### Home — Bedroom (`rooms/home/room.acvn`)

| Event | Trigger conditions | Effect |
|---|---|---|
| Private time — fantasy | `random_int(1,3) == 1` | +30 horny, −20 horny after climax, +12 mood |
| Private time — erotic story | `random_int(1,3) == 2` | +25 horny, −15 horny after, +8 mood |
| Private time — unrestricted | `random_int(1,3) == 3` | +35 horny, −30 horny after, +15 mood |
| Positive mirror reflection | `random_int(1,3) == 1` (33 %) | +5 horny |

---

### Home — Living Room (`rooms/home/livingroom.acvn`)

**Video game betting with Robert**

| Event | Trigger conditions | Effect |
|---|---|---|
| Bet offer appears | `vars.bro_wins >= 3` **and** `!vars.bet_offered` | Robert proposes dares for wins |
| Win a round (serious mode) | `random_int(1,100) <= 40` (40 %) | MC wins; Robert performs dare |
| Lose a round (fun mode) | `random_int(1,100) > 25` (75 %) | MC loses; performs Robert's dare |

**Dare escalation by stage** (`vars.bet_stage`):

| Stage | MC-wins dare | MC-loses dare | Inhibition gate |
|---|---|---|---|
| 1 | Robert makes sandwich | MC tells a secret | None |
| 2 | Robert sings a song | MC lets Robert check phone | None |
| 3 | Robert gives shoulder massage | MC takes shirt off | MC: `inhibition >= 50` to refuse only |
| 4 | Robert gives back massage | MC plays topless | None |
| 5 | Robert puts hand on MC's leg | MC kisses Robert | MC kiss: `inhibition <= 40` |
| 6 | Robert takes jeans off | MC "come here" | MC: `inhibition <= 30` |
| 7+ | Sexual escalation | Sexual escalation | `inhibition < threshold` |

**Late-night Sandra encounter**

| Event | Trigger conditions | Effect |
|---|---|---|
| Sandra appears in robe | Alone in living room **and** `hour >= 22` **and** `sandra.mood >= 60` **and** `!vars.late_encounter_done_today` | Dialog branch by inhibition |
| ↳ Friendly chat | `inhibition >= 70` | +5 mood both |
| ↳ Flirt | `inhibition < 70` | +10 mood both, Sandra sits closer, +5 relationship |
| ↳ Invite to sit | `inhibition < 40` | +12 mood Sandra, +10 mood mc, +10 relationship |

---

### Home — Stairwell (`rooms/home/stairwell.acvn`)

| Event | Trigger conditions | Effect |
|---|---|---|
| Neighbor encounter | `clothing_state != "dressed"` **and** `random_int(1,10) == 1` (10 %) | Neighbor reacts to state |
| ↳ In underwear | State `"underwear"` | Awkward exchange |
| ↳ Naked | State `"naked"` | Neighbor mortified |
| Leave building blocked | State `"underwear"` **and** `inhibition > 15` | Redirected; must get dressed |
| Leave building blocked | State `"naked"` **and** `inhibition > 5` | Redirected; must get dressed |

---

### Outdoor — Park (`rooms/outdoor/park.acvn`)

| Event | Trigger conditions | Effect |
|---|---|---|
| Mugging while jogging (dark) | `hour >= 21 or hour < 6` **and** `!vars.mugged_today` **and** `random_int(1,100) <= 30` (30 %) | Phone removed, −10 health, −25 mood; sets `mugged_today` |
| Mugging while resting (dark) | `hour >= 21 or hour < 6` **and** `!vars.mugged_today` **and** `random_int(1,100) <= 20` (20 %) | Phone removed, −8 health, −20 mood; sets `mugged_today` |

---

### Outdoor — Street (`rooms/outdoor/street.acvn`)

| Event | Trigger conditions | Effect |
|---|---|---|
| Frank invite to shelter | `vars.kron_given_frank >= 30` | Sets `frank_shelter_unlocked`, `frank_invited` |
| Frank recognises MC | `vars.kron_given_frank > 0` | Recognition message displayed |

---

### Outdoor — Pool Entrance (`rooms/outdoor/pool.acvn`)

| Event | Trigger conditions | Effect |
|---|---|---|
| Work shift available | `item_count("pool_job") > 0` **and** `!vars.shift_done_today` | Shows "Work cleaning shift" option |
| Shift blocked — wrong hour | `hour < 7 or hour > 19` | Info message; no work |
| Post-shift Patrick encounter | Shift completed **and** `char_at("patrick", "outdoor_pool_area")` | Chat or leave branch |
| ↳ Patrick chat — basic | `patrick.relationship < 15` | +4 relationship, +4 mood |
| ↳ Patrick chat — Friday invite | `patrick.relationship >= 15` | +7 relationship, +6 mood, +8 horny |
| Leave blocked — swimwear | `clothing_state == "underwear"` **and** `inhibition > 15` | Redirected to changing room |
| Leave blocked — naked | `clothing_state == "naked"` **and** `inhibition > 5` | Redirected to changing room |
| Leave freely — low inhibition | `clothing_state == "underwear"` **and** `inhibition <= 15`, or `"naked"` **and** `inhibition <= 5` | Can leave underdressed |

---

### Outdoor — Pool Area (`rooms/outdoor/pool/area.acvn`)

**Arrival ambient events** (re-roll each visit):

| Event | Trigger conditions | Effect |
|---|---|---|
| Group stares | `has_swimwear` **and** `area_roll == 1` (1 in 6, ~17 %) | +4 horny |
| Man watches openly | `has_swimwear` **and** `area_roll == 2` (1 in 6, ~17 %) | +5 horny |

**Swim laps** (`#begin swim`):

| Event | Trigger conditions | Effect |
|---|---|---|
| Patrick "inspection lap" collision | `random_int(1,10) == 1` (10 %) **and** `!vars.pool_harassed_today` **and** Patrick in pool area | −8 mood, +10 horny (mc), +10 horny (Patrick); sets `pool_harassed_today` |
| Random swimmer gropes | `random_int(1,10) == 1` (10 %) **and** `!vars.pool_harassed_today` **and** Patrick absent | −10 mood, +5 horny; sets `pool_harassed_today` |
| Swimmer matches your pace | `random_int(1,7) == 1` (~14 %) **and** `!vars.pool_lane_today` | +6 horny; sets `pool_lane_today` |

**Whirlpool** (`#begin whirlpool`):

| Event | Trigger conditions | Effect |
|---|---|---|
| Stranger sits too close | `random_int(1,5) == 1` (20 %) **and** `!vars.pool_wp_event` | +8 horny; sets `pool_wp_event` |

**Whirlpool nap** (`#begin whirlpoolnap`):

| Event | Trigger conditions | Effect |
|---|---|---|
| Patrick touches thigh | `random_int(1,100) <= 30` (30 %) **and** `!vars.pool_harassed_today` **and** Patrick in pool area | −10 mood, +15 horny (both); sets `pool_harassed_today` |
| ↳ Move his hand — reject | Always available | −5 mood, +5 horny, −5 horny (Patrick) |
| ↳ Warn him | `inhibition < 60` | +3 mood, +12 horny (both), +3 relationship |
| ↳ Stay silent — accept | `inhibition < 30` | +5 mood, +25 horny (both), +10 relationship |
| Stranger touches thigh | `random_int(1,100) <= 30` (30 %) **and** `!vars.pool_harassed_today` **and** Patrick absent | −15 mood, +10 horny; sets `pool_harassed_today` |

**Water slides** (`#begin slides`):

| Event | Trigger conditions | Effect |
|---|---|---|
| Man stands too close in queue | `random_int(1,4) == 1` (25 %) **and** `!vars.pool_slide_event` | +5 horny; sets `pool_slide_event` |
| Man steadies MC at splash pool | `random_int(1,4) == 2` (25 %) **and** `!vars.pool_slide_event` | +7 horny; sets `pool_slide_event` |

**Talking to Patrick** (`#begin patrick_talk`):

| Event | Trigger conditions | Effect |
|---|---|---|
| Basic chat | `patrick.relationship < 15` | +4 relationship, +3 mood |
| He steps down, gets closer | `patrick.relationship` 15–24 | +5 relationship, +4 mood, +5 horny |
| Friday dinner invite | `patrick.relationship >= 25` | Choice: maybe (+3 rel) / yes (+8 rel, +8 horny) / no (−3 rel) |

**Flirting with Patrick** (`#begin patrick_flirt`):

| Event | Trigger conditions | Effect |
|---|---|---|
| Flirt available | `inhibition <= 60` | +10 mood, +12 horny (mc), +8 relationship, +20 horny (Patrick) |

---

### Frank's Shelter (`rooms/outdoor/frank/shelter.acvn`)

| Event | Trigger conditions | Effect |
|---|---|---|
| First meeting | `quest_step("good_person") == -1` | Starts "good_person" quest |
| Deliver food | Quest stage 1 **and** `item_count("groceries") > 0 or item_count("pizza") > 0` | +35 mood (Frank), +15 relationship; advances quest |
| Refuse to bring food | Quest stage 1, decline option | −8 mood (mc), −15 mood (Frank), −8 relationship |
| Offer shower | Quest stage 2 | Frank requests shower access; advances quest |
| Stay overnight | Quest stage ≥ 3 **and** `hour >= 20 or hour < 5` | Escalation sequence begins |

**Overnight escalation** (cumulative nights counter):

| Milestone | Trigger | Choice | Effect |
|---|---|---|---|
| Night 5 | `nights == 5` | Hand touching | Accept: +10 horny (both), +8 relationship / Reject: −3 relationship |
| Night 10 | `nights == 10` | Arm around during sleep | Accept: +15 horny (both), +12 relationship / Reject: −4 relationship |
| Night 15 | `nights == 15` | Kiss | Accept: +30 horny (mc), +35 horny (Frank), +20 relationship / Reject: −5 relationship, −8 mood (Frank) |
| Night 20+ | `nights >= 20` | Intimate encounters | Ongoing |

---

## License

Story content © narayan24.  
The ACVN engine is licensed under the [MIT License](https://github.com/narayan24/ACVN/blob/master/LICENSE).
