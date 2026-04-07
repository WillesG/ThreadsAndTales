# DM Toolkit — D&D 3.5 Campaign Manager

A browser-based DM binder for running D&D 3.5 campaigns at the table. No server, no install, no account needed — open the page and start managing your adventure.

## Features

- **Locations** — hierarchical tree (city → ward → building → room), with status tracking
- **NPCs** — full 3.5 stat blocks, faction links, relationship mapping
- **Monsters** — complete Monster Manual-style stat blocks (HD, AC triplet, attacks, special abilities)
- **Encounters** — rich rendering with read-aloud text, DM notes, phases, DC chips, treasure, and traps
- **Factions** — member tracking, territory, involvement in encounters
- **Sessions** — dated notes with bullet points
- **Party Dashboard** — PC cards with HP bars, 3.5 combat stats (AC/Touch/Flat, BAB, Grapple, Spot, Listen), abilities, saves, skills, spell slots
- **Adventure Storyline** — visual node graph with drag-to-connect ports, auto-synced with encounters
- **Combat Tracker** — side panel with turn queue, drag reordering, round counter, and effect/spell duration tracking
- **Status Tracking** — mark locations visited/cleared, NPCs alive/dead, encounters completed, with sidebar filtering
- **Import/Export** — JSON-based, with merge support for combining multiple adventures
- **Works offline** — pure HTML, all data stored in your browser's localStorage

## How to Use

1. Visit the hosted page (or open `index.html` locally)
2. Import an adventure JSON, or add entries manually
3. Use the toolbar to switch between Content, Party, and Storyline views
4. Toggle the Combat Tracker (clock icon) during encounters — it stays open while you browse

## Adventure Extraction

Use the included extraction prompt (`DM_Toolkit_Extraction_Prompt_v5.md`) with Claude or another AI to convert adventure PDFs into importable JSON. The prompt extracts locations, NPCs, monsters, encounters, factions, and storyline nodes with full cross-referencing.

## Data Storage

All data is stored in your browser's `localStorage`. Each browser/device has its own separate database. Use Export/Import to move data between devices.

## Tech

Single HTML file. React 18 + Babel (CDN). No build step, no dependencies to install, no server required.

## License

Personal use. Built for the table.
