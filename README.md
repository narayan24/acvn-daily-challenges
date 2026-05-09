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

## License

Story content © narayan24.  
The ACVN engine is licensed under the [MIT License](https://github.com/narayan24/ACVN/blob/master/LICENSE).
