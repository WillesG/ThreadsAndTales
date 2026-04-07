# DM Toolkit — Adventure Extraction Prompt (v5)

Copy and paste the prompt below into a new Claude conversation along with your adventure PDF.
Customize the optional instructions at the bottom to fit your needs.

---

## PROMPT (copy everything below this line)

I need you to extract this D&D adventure into a structured JSON format for my DM Toolkit app.

Read the entire document carefully, then produce a single JSON object with this exact structure:

```json
{
  "campaign": "Adventure Name Here",
  "locations": [
    {
      "id": "loc_001",
      "name": "Location Name",
      "type": "city|ward|neighborhood|building|room|wilderness|other",
      "parent": null,
      "description": "Full description including read-aloud text, room contents, secrets, traps, etc.",
      "npcs": ["npc_001"],
      "encounters": ["enc_001"],
      "factions": ["fac_001"],
      "monsters": ["mon_001"]
    }
  ],
  "npcs": [
    {
      "id": "npc_001",
      "name": "NPC Name",
      "locations": ["loc_001", "loc_005"],
      "factions": ["fac_001"],
      "related_npcs": ["npc_003", "npc_007"],
      "encounters": ["enc_001", "enc_004"],
      "description": "Appearance, personality, motivations, secrets, role in the adventure.",
      "stats": {
        "abilities": ["14", "10", "12", "16", "11", "8"],
        "hp": "45",
        "ac": "16",
        "bab": "+6",
        "init": "+2",
        "speed": "30 ft",
        "cr": "5",
        "saves": ["+5", "+2", "+4"]
      }
    }
  ],
  "monsters": [
    {
      "id": "mon_001",
      "name": "Monster Name",
      "description": "Flavor text, behavior, tactics, habitat.",
      "locations": ["loc_003"],
      "encounters": ["enc_002"],
      "stats": {
        "size": "Large",
        "type": "Magical Beast",
        "subtypes": "",
        "cr": "5",
        "hd": "6d10+18",
        "hp": "51",
        "ac": "15",
        "ac_touch": "10",
        "ac_flat": "14",
        "init": "+1",
        "bab": "+6",
        "speed": "40 ft",
        "fly": "",
        "swim": "",
        "climb": "",
        "burrow": "",
        "abilities": ["21", "12", "17", "2", "12", "6"],
        "saves": ["+8", "+6", "+3"],
        "attacks": "Claw +9 melee (1d6+5)",
        "full_attack": "2 claws +9 melee (1d6+5), bite +4 melee (1d8+2)",
        "damage": "",
        "special_attacks": "Pounce, improved grab, rake 1d6+2",
        "special_qualities": "Darkvision 60 ft, low-light vision, scent"
      }
    }
  ],
  "encounters": [
    {
      "id": "enc_001",
      "name": "Short Encounter Title",
      "type": "combat|social|event",
      "locations": ["loc_001"],
      "description": "General encounter overview for quick reference.",
      "npcs": ["npc_001"],
      "monsters": ["mon_001"],
      "factions": ["fac_002"],

      "boxed_text": "Read-aloud text the DM reads to players verbatim when they enter.",
      "dm_notes": "DM-only tactical context, secrets, and narrative notes.",
      "phases": [
        {
          "label": "Phase 1: The Approach",
          "trigger": "When players enter the room",
          "description": "The cultists haven't noticed the party yet..."
        }
      ],
      "checks": [
        { "skill": "Perception", "dc": 13, "passive": true, "note": "Spots the hidden lever" },
        { "skill": "Dexterity", "dc": 15, "type": "saving throw", "note": "Avoid falling bricks, 3d6 on fail" }
      ],
      "treasure": {
        "coins": { "pp": 0, "gp": 55, "ep": 0, "sp": 800, "cp": 0 },
        "items": [
          { "name": "Wand of Fear", "type": "magic", "qty": 1 },
          { "name": "Diamond", "type": "gem", "qty": 2, "value": "500 gp each" }
        ],
        "notes": "Locked in the skull box under the altar."
      },
      "traps": [
        {
          "name": "Scything Blades",
          "trigger": "Stepping between the pillars",
          "detect_skill": "Perception",
          "detect_dc": 13,
          "disarm_skill": "Strength",
          "disarm_dc": 10,
          "disarm_notes": "Hammer spikes into niches; requires 4 successful checks",
          "effect": "18 (4d8) slashing damage, DC 13 Dex save for half",
          "magical": false
        }
      ]
    }
  ],
  "factions": [
    {
      "id": "fac_001",
      "name": "Faction Name",
      "description": "Goals, methods, attitude toward party, resources.",
      "npcs": ["npc_001", "npc_002"],
      "locations": ["loc_003"],
      "encounters": ["enc_002"]
    }
  ],
  "storyNodes": [
    {
      "id": "stn_001",
      "name": "Encounter Title (must match linked encounter name)",
      "description": "DM notes about this scene's narrative flow and branching.",
      "type": "combat|social|event",
      "status": "upcoming",
      "x": 100,
      "y": 100,
      "encounter_id": "enc_001"
    }
  ],
  "storyEdges": [
    {
      "id": "ste_001",
      "from": "stn_001",
      "to": "stn_002"
    }
  ]
}
```

## Rules for extraction:

**IMPORTANT — All links are arrays (multi-value):**
- NPCs can appear in MULTIPLE locations, belong to MULTIPLE factions, and relate to MULTIPLE other NPCs
- Encounters can happen at MULTIPLE locations and involve MULTIPLE factions and monsters
- Factions can operate from MULTIPLE locations
- Monsters can appear at MULTIPLE locations and in MULTIPLE encounters
- Always use arrays, even for single values: `"locations": ["loc_001"]` not `"location": "loc_001"`

**Locations:**
- Build a hierarchy: top-level area -> sub-areas -> specific rooms/places
- Use `parent` to link children to parents (e.g., a room's parent is the building)
- Top-level locations have `parent: null`
- Include ALL rooms, areas, and named places from the adventure
- Put read-aloud text and DM notes in the description
- List all factions, NPCs, encounters, and monsters at this location

**NPCs (named characters with personality and story role):**
- Extract every named NPC — these are characters the party interacts with, not generic enemies
- Include stat blocks in the `stats` field (abilities array is [STR, DEX, CON, INT, WIS, CHA])
- Saves array is [Fort, Ref, Will]
- `locations`: ALL places this NPC can be found
- `factions`: ALL organizations they belong to or work with
- `related_npcs`: Other NPCs they have significant relationships with (bidirectional — if A lists B, B must list A)
- `encounters`: ALL encounters this NPC appears in
- If stats aren't provided, leave stats fields as empty strings

**Monsters (creatures with stat blocks — separate from NPCs):**
- Extract every distinct monster type that appears in the adventure
- Monsters are generic creatures (zombies, frost giants, dinosaurs), NOT named NPCs
- If multiple of the same monster appear (e.g. "4 zombies"), create ONE monster entry with the stats
- Use the full D&D 3.5 stat block format:
  - `size`: Tiny, Small, Medium, Large, Huge, Gargantuan, Colossal
  - `type`: Aberration, Animal, Construct, Dragon, Elemental, Fey, Giant, Humanoid, Magical Beast, Monstrous Humanoid, Ooze, Outsider, Plant, Undead, Vermin
  - `subtypes`: any subtypes (Aquatic, Fire, Shapechanger, etc.) — empty string if none
  - `cr`: Challenge Rating
  - `hd`: Hit Dice expression (e.g. "4d8+8")
  - `hp`: Total hit points
  - `ac`: Normal AC, `ac_touch`: Touch AC, `ac_flat`: Flat-footed AC
  - `init`: Initiative modifier
  - `bab`: Base attack bonus
  - `speed`: Land speed. Also `fly`, `swim`, `climb`, `burrow` if applicable (empty string if not)
  - `abilities`: [STR, DEX, CON, INT, WIS, CHA] as strings
  - `saves`: [Fort, Ref, Will] as strings with + prefix
  - `attacks`: Primary attack line
  - `full_attack`: Full attack routine
  - `damage`: Additional damage notes if any
  - `special_attacks`: All special attack abilities
  - `special_qualities`: All special qualities (DR, immunities, senses, etc.)
- `locations`: ALL locations where this monster can be found
- `encounters`: ALL encounters this monster appears in
- If stats aren't available, leave fields as empty strings

**Encounters:**
- Every combat, social encounter, trap, puzzle, or scripted event
- Type: "combat" for fights, "social" for roleplay/negotiation, "event" for everything else
- `description`: Brief general overview of the encounter for quick reference
- `locations`: ALL locations where this encounter can occur
- `npcs`: ALL NPCs involved
- `monsters`: ALL monster types involved (link to monster entries, not individual creatures)
- `factions`: ALL factions involved
- **New structured sub-fields (include when content is available):**
  - `boxed_text`: Read-aloud text the DM reads verbatim to players. Extract any italicized or boxed read-aloud passages from the adventure text. Only the text meant to be spoken aloud — no DM instructions.
  - `dm_notes`: DM-only tactical context, secrets, hidden information, and narrative notes. Things the DM needs to know but players should not hear.
  - `phases`: Array of encounter phases if the encounter has distinct stages. Each phase has `label` (e.g. "Phase 1: The Approach"), `trigger` (what causes this phase), and `description`. Only include if the encounter genuinely has 2+ distinct phases. Single-phase encounters should omit this field.
  - `checks`: Array of skill checks, ability checks, and saving throws that occur in this encounter. Each has `skill` (e.g. "Perception", "Dexterity"), `dc` (number), `passive` (boolean, true if it's a passive check), `type` ("check"|"saving throw", optional), and `note` (what happens on success/failure). Include ALL DCs mentioned in the encounter text.
  - `treasure`: Object with `coins` ({pp, gp, ep, sp, cp} — use 0 for denominations not present), `items` (array of {name, type: "magic"|"gem"|"mundane"|"art", qty, value}), and `notes` (string for where treasure is found or special conditions). Only include if there is actual treasure to be found.
  - `traps`: Array of traps in this encounter. Each has `name`, `trigger`, `detect_skill`, `detect_dc`, `disarm_skill`, `disarm_dc`, `disarm_notes`, `effect` (damage and save info), and `magical` (boolean). Only include if there are actual traps.

**Factions:**
- Any organization, group, or alliance mentioned in the adventure
- `npcs`: ALL member NPCs
- `locations`: ALL locations where they have a presence
- `encounters`: ALL encounters they are involved in

**Story Nodes (for the storyline graph — syncs with encounters):**
- Create one node per encounter, plus nodes for major non-encounter story beats
- **CRITICAL: If a story node links to an encounter (via encounter_id), the node's `name` and `type` MUST exactly match the encounter's `name` and `type`** — the app syncs these fields automatically
- The story node `description` is for DM notes about narrative flow (separate from encounter description)
- Arrange x positions left to right: 100, 400, 700, 1000, etc.
- Arrange y positions to show branching: parallel paths at different y values (100, 300, 500)
- Set all statuses to "upcoming"
- Every encounter SHOULD have a corresponding story node

**Story Edges:**
- Connect nodes to show which scene leads to which
- A scene can lead to multiple next scenes (branching paths)
- Multiple scenes can lead to the same next scene (converging paths)

**Cross-referencing is critical:**
- Every link must be consistent on BOTH sides
- If monster mon_001 lists location loc_003 in `locations`, then loc_003 must list mon_001 in `monsters`
- If encounter enc_002 lists monster mon_003 in `monsters`, then mon_003 must list enc_002 in `encounters`
- If NPC npc_001 lists encounter enc_004 in `encounters`, then enc_004 must list npc_001 in `npcs`
- Same rule applies for ALL link types across ALL entity types
- Double-check all cross-references before outputting

**IDs:**
- Use consistent prefixes: loc_, npc_, mon_, enc_, fac_, stn_, ste_
- Number sequentially: loc_001, loc_002, etc.

## Additional instructions:
- [OPTIONAL: Add custom instructions: "Rename the tavern owner to Valdris", "Skip Chapter 4"]
- [OPTIONAL: "Focus only on Chapter 2" or "Extract the entire module"]
- [OPTIONAL: "This is for D&D 3.5, convert any 5e stats to 3.5"]
- [OPTIONAL: "This adventure is part of a larger campaign, use ID range starting from loc_050, npc_030, etc. to avoid conflicts"]

Output ONLY the JSON, no explanation or commentary. Make it valid, parseable JSON.
