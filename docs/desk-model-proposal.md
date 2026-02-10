# Skip Content Architecture: Desk Model Proposal

**Author:** Mr. Content (editor-in-chief agent)
**Date:** 2026-02-10
**Status:** Proposal for engineering review

---

## Summary

We're replacing the old beat reporter model (100+ individual agents per beat × location) with a **desk model**: 5 persistent market-level agents, each covering all beats for their territory. This requires changes to the card schema, CLI, and briefing system.

---

## The Old Model (What We're Replacing)

- One agent per beat per location: `beat-fishing-tierra-verde`, `beat-weather-tampa-bay`, etc.
- 20+ registered agents, potentially 100+ at scale
- No memory between runs — fire and forget
- Cards tagged with `agentId` and `location` as flat strings
- No concept of markets or content sharing

**Problems:**
- Agent sprawl (too many to manage)
- No institutional knowledge (agents don't learn)
- Inconsistent coverage (some locations have 214 cards, others have 10)
- No content sharing across related locations

## The New Model: Desks

### 5 Market Desks

| Desk | Marinas Covered |
|------|----------------|
| `tampa-bay` | Tierra Verde, Tampa |
| `sw-florida` | Naples, Marco Island, Cape Coral |
| `se-florida` | Fort Lauderdale, Lighthouse Point, Palm Beach Gardens |
| `ne-florida` | Jacksonville |
| `crystal-coast` | Morehead City |

Each desk is a persistent OpenClaw agent with:
- Memory (tracks what it's covered, local intel, seasonal patterns)
- Heartbeats (periodic check-ins)
- Beat-specific skills (fishing, weather, dining, destinations, port32, safety, events, lifestyle)
- Direct Convex write access via Skip CLI

### Hierarchy

```
Mr. Content (editor-in-chief)
├── tampa-bay-desk
│   ├── tierra-verde (marina)
│   └── tampa (marina)
├── sw-florida-desk
│   ├── naples (marina)
│   ├── marco-island (marina)
│   └── cape-coral (marina)
├── se-florida-desk
│   ├── fort-lauderdale (marina)
│   ├── lighthouse-point (marina)
│   └── palm-beach-gardens (marina)
├── ne-florida-desk
│   └── jacksonville (marina)
└── crystal-coast-desk
    └── morehead-city (marina)
```

---

## Card Schema Changes

### Current Schema
```
agentId: "beat-fishing-tierra-verde"   // the beat agent
location: "tierra-verde"               // flat string, inconsistent hierarchy
category: "fishing"
```

### Proposed Schema
```
desk: "tampa-bay"            // required — the market desk that owns this card
category: "fishing"          // required — the beat/topic
marina: "tierra-verde"       // optional — pin to a specific marina within the desk
```

### Field Definitions

**`desk`** (required, string)
The market desk that wrote/owns this card. One of:
- `tampa-bay`
- `sw-florida`
- `se-florida`
- `ne-florida`
- `crystal-coast`
- `shared` — for state-level or cross-market content (written by Mr. Content or any desk, available to all)

**`category`** (required, string enum)
The content beat. Standardized list:
- `fishing` — reports, species, techniques, spots
- `weather` — marine forecasts, storm prep, tides
- `destinations` — day trips, islands, anchorages, sandbars
- `dining` — dock-and-dine, waterfront restaurants
- `port32` — marina amenities, services, member tips
- `safety` — navigation hazards, equipment, regulations
- `events` — tournaments, boat shows, festivals
- `lifestyle` — culture, gear, boating life
- `maintenance` — engine care, hull, seasonal prep

**`marina`** (optional, string)
Pins the card to a specific marina within the desk's territory. Only set when the card is specifically about one marina (e.g., a restaurant guide for Tampa's Hula Bay Club, or a first-timer guide to Port32 Tierra Verde).

Valid marinas per desk:
- `tampa-bay`: `tierra-verde`, `tampa`
- `sw-florida`: `naples`, `marco-island`, `cape-coral`
- `se-florida`: `fort-lauderdale`, `lighthouse-point`, `palm-beach-gardens`
- `ne-florida`: `jacksonville`
- `crystal-coast`: `morehead-city`

When omitted, the card serves all marinas in the desk's territory.

### Examples

| Card | desk | category | marina |
|------|------|----------|--------|
| Tampa Bay Weekly Fishing Report | `tampa-bay` | `fishing` | _(omit)_ |
| Hula Bay Club Dock-and-Dine Guide | `tampa-bay` | `dining` | `tampa` |
| Shell Key Day Trip Guide | `tampa-bay` | `destinations` | `tierra-verde` |
| First-Timer's Guide to Port32 TV | `tampa-bay` | `port32` | `tierra-verde` |
| Florida Anchoring Regulations | `shared` | `safety` | _(omit)_ |
| Hurricane Prep for Boaters | `shared` | `safety` | _(omit)_ |
| Naples Snook Fishing Spots | `sw-florida` | `fishing` | `naples` |
| SW Florida Offshore Forecast | `sw-florida` | `weather` | _(omit)_ |

---

## Briefing Resolution

When assembling a briefing for a captain at a specific marina:

1. **All cards from their desk** (e.g., everything from `tampa-bay` desk)
2. **Marina-pinned cards from their marina get boosted** in ranking
3. **All `shared` desk cards** (state-level content)
4. **Cross-desk pulls** (future: desks can curate cards from other desks that are relevant)

**Example: Captain at Tierra Verde sees:**
- Tampa Bay fishing report (desk card, no marina pin) ✅
- Shell Key guide (desk card, pinned to tierra-verde) ✅ **boosted**
- Hula Bay guide (desk card, pinned to tampa) ✅ lower priority
- Florida anchoring regs (shared) ✅
- Naples fishing report (sw-florida desk) ❌ not shown unless curated

---

## Data Migration

376 existing cards need migration from old schema:

### Mapping Rules
1. `agentId` contains a location → extract desk:
   - `beat-*-tierra-verde`, `beat-*-tampa-bay`, `beat-*-tampa` → `desk: tampa-bay`
   - `beat-*-naples`, `beat-*-marco-island`, `beat-*-cape-coral` → `desk: sw-florida`
   - `beat-*-fort-lauderdale`, `beat-*-lighthouse-point`, `beat-*-palm-beach-gardens` → `desk: se-florida`
   - `beat-*-jacksonville` → `desk: ne-florida`
   - `beat-*-morehead-city`, `beat-*-crystal-coast` → `desk: crystal-coast`
   - `beat-*-florida` → `desk: shared`
   - `beat-reporter` → `desk: shared`

2. `location` field → maps to `marina` if it's a marina name, or confirms `desk` if it's a market name:
   - `location: tierra-verde` → `marina: tierra-verde`
   - `location: tampa-bay` → no marina (market-level)
   - `location: florida` → `desk: shared`, no marina
   - `location: sw-florida` → no marina (market-level)

3. `agentId` gets updated to the new desk agent id

### Migration Numbers
Current distribution by proposed desk:
- `tampa-bay`: ~234 cards (tierra-verde: 214, tampa-bay: 20)
- `shared` (florida-wide): ~40 cards
- `sw-florida`: ~33 cards
- `se-florida`: ~25 cards
- `ne-florida`: ~14 cards
- `crystal-coast`: ~13 cards
- Unmapped/ambiguous: ~17 cards (need manual review)

---

## CLI Changes Needed

### Card Create
```bash
# Before
skip card create --agent-id=beat-fishing-tierra-verde --location=tierra-verde ...

# After
skip card create --desk=tampa-bay --category=fishing --marina=tierra-verde ...
```

New flags:
- `--desk` (required) — replaces `--agent-id` for card ownership
- `--marina` (optional) — replaces `--location` for marina-level pinning
- Keep `--category` as-is

### Card List / Filter
```bash
skip card list --desk=tampa-bay [--category=fishing] [--marina=tierra-verde] [--json]
```

### Coverage Report (new command)
```bash
skip card coverage --desk=tampa-bay --json
```
Returns:
```json
{
  "desk": "tampa-bay",
  "marinas": ["tierra-verde", "tampa"],
  "coverage": {
    "fishing": { "total": 12, "tierra-verde": 3, "tampa": 2, "market": 7 },
    "weather": { "total": 2, "market": 2 },
    "dining": { "total": 0 },
    "destinations": { "total": 8, "tierra-verde": 5, "tampa": 1, "market": 2 }
  },
  "gaps": ["dining", "events", "lifestyle"]
}
```

### Card Search (new command)
```bash
skip card search --query="sheepshead" [--desk=tampa-bay] --json
```
Text search across titles and content. Prevents desk agents from writing duplicate cards.

### Desk Registration (new or fix existing)
```bash
skip agent create --id=tampa-bay-desk --type=desk --name="Tampa Bay Desk" \
  --marinas=tierra-verde,tampa --json
```
Registers the desk in Convex so it can create cards. Associates it with its marinas.

### Card Audit (fix — currently 404)
```bash
skip card audit --desk=tampa-bay --json
```
Checks for: missing fields, expired timely cards, duplicate topics, cards without images.

---

## Agent Model

Each desk runs as an OpenClaw agent on the same machine as Mr. Content.

### Agent Config (gateway)
```json
{
  "id": "tampa-bay-desk",
  "name": "tampa-bay-desk",
  "workspace": "/root/workspaces/mr-content/desks/tampa-bay",
  "agentDir": "/root/.openclaw/agents/tampa-bay-desk/agent"
}
```

### Agent Capabilities
- **Memory**: persistent MEMORY.md tracking coverage, local intel, seasonal patterns
- **Heartbeat**: periodic check-in (check for assignments, run scheduled content)
- **Skills**: 8 local beat skills + 2 shared skills (card-writing, web-research)
- **Tools**: web_search, web_fetch, exec (skip CLI), sessions_send (report to Mr. Content)
- **Sub-agents**: can spawn ephemeral sub-agents for deep research via sessions_spawn

### Communication Flow
```
Mr. Content                      Desk Agent
    │                                │
    ├── assigns story ──────────────►│
    │                                ├── researches (web)
    │                                ├── writes card (skip CLI)
    │                                ├── generates image
    │◄── reports completion ─────────┤
    │                                │
    ├── reviews coverage ───────────►│
    │◄── reports gaps ───────────────┤
    │                                │
    │   (heartbeat)                  │
    │                                ├── checks for stale cards
    │                                ├── monitors local news
    │◄── flags opportunities ────────┤
```

---

## Priority for Engineering

### P0 (Blockers)
1. **Desk registration** — `skip agent create --type=desk` (currently 404)
2. **Card create with desk/marina** — new `--desk` and `--marina` flags
3. **Card list filtering by desk** — `--desk` filter

### P1 (Important)
4. **Data migration script** — migrate 376 cards from old schema
5. **Card audit fix** — currently 404
6. **Auto image generation on card create** — `--generate-image` flag or auto-trigger
7. **Coverage report** — `skip card coverage --desk=X`

### P2 (Nice to Have)
8. **Card search** — text search across titles/content
9. **Card stats** — serve counts, thumbs up/down by desk
10. **Cross-desk content sharing** — mechanism for desks to pull cards from other desks

---

## Open Questions for Engineering

1. **Should `desk` replace `agentId` on cards, or coexist?** We think replace — the desk IS the agent. But want to confirm there's no downstream dependency on the old `agentId` format.

2. **Briefing assembly** — does the briefing system currently use `location` to filter cards for users? If so, it needs to switch to desk + marina resolution.

3. **Image generation** — currently `card create` takes `--image=<url>` or uses a placeholder. What triggers actual image generation? Is it a separate `skip image queue` call? We'd like it to be automatic on card create.

4. **Environment variables for desk agents** — desk agents need `SKIP_API_TOKEN` to use the CLI. Can this be set in the gateway agent config, or does it need to be in each agent's environment?

5. **`freshness` and `expires-at`** — do these stay as-is, or do they change with the desk model? We think they stay.

---

## Timeline Suggestion

1. **Week 1**: Schema changes + migration + CLI updates (P0 + P1)
2. **Week 2**: Register all 5 desks, test card creation flow end-to-end
3. **Week 3**: Mr. Content starts dispatching assignments, desks producing cards
4. **Ongoing**: Coverage reports drive editorial strategy, desks build local knowledge over time
