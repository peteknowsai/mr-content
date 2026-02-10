# Desk Creator

Create a new market desk agent for Hey Skip. Each desk is a persistent OpenClaw agent covering all beats for one Port32 market.

## When to Use
- Expanding to a new Port32 market
- Rebuilding a desk from scratch

## Steps

### 1. Research the Market
Before writing anything, learn the territory. Use `web_search` and `web_fetch`:
- `"Port32 [location]"` — marina details, amenities, address
- `"[location] fishing report"` — species, seasons, spots
- `"[location] waterfront restaurants dock and dine"` — dining options
- `"[location] boating destinations day trips"` — islands, sandbars, anchorages
- `"[location] marine weather hazards"` — local conditions, NOAA zones
- `"[location] boat show fishing tournament 2026"` — events
- Check `skills/location-intel/SKILL.md` for any existing intel on this market

Spend 10-15 minutes here. The quality of every skill depends on real local knowledge.

### 2. Create Directory Structure
```
desks/<market-name>/
  SOUL.md
  AGENTS.md
  MEMORY.md
  skills/
    fishing/SKILL.md
    weather/SKILL.md
    dining/SKILL.md
    destinations/SKILL.md
    port32/SKILL.md
    safety/SKILL.md
    events/SKILL.md
    lifestyle/SKILL.md
```

The desk also inherits shared skills from `desks/shared-skills/`:
- `card-writing/SKILL.md` — how to write and manage Skip content cards
- `web-research/SKILL.md` — how to research content using web tools

These do NOT need to be recreated per desk.

### 3. Write SOUL.md
Use `desks/tampa-bay/SOUL.md` as the template. Customize:
- Agent name (e.g., "SW Florida Desk")
- Territory — list every Port32 marina in this market with address
- Waters — bays, passes, inlets, offshore access
- Key spots — the 5-10 places every local captain knows
- Personality — make it feel local to THIS market, not generic
- What they care about (same core values, localized examples)

### 4. Write AGENTS.md
Use `desks/tampa-bay/AGENTS.md` as the template. Customize:
- Market name and marina list
- Card hierarchy tags — which location IDs map to marina vs market level
- Reference the location hierarchy from MEMORY.md

### 5. Write Each Skill
Use the corresponding Tampa Bay skill as the template. For each skill:

**fishing/SKILL.md** — Most research-intensive. Need:
- Year-round species with seasons, size limits, bag limits
- Seasonal species with peak months
- 5-10 key fishing spots with what to target and how
- Port32 tie-ins (proximity to spots, fuel, launch)

**weather/SKILL.md** — Need:
- NOAA marine forecast zones (find at weather.gov/marine)
- NDBC buoy stations nearby
- Local weather patterns (e.g., afternoon storms, fog, wind patterns)
- What matters to captains here (inlet conditions, Gulf stream proximity, etc.)

**dining/SKILL.md** — Need:
- Dock-and-dine restaurants accessible from each marina
- Waterfront restaurants reachable by boat
- Which ones have actual dock space vs anchor-off
- Organized by proximity to each Port32 marina in market

**destinations/SKILL.md** — Need:
- Day trip destinations with distance/time from marina
- Islands, sandbars, state parks, anchorages
- Navigation tips (draft, tide, approach)
- Seasonal considerations

**port32/SKILL.md** — Need:
- Full amenity details for each marina in market
- Storage types, boat size limits, fuel, services
- On-site restaurants/bars
- What makes THIS location special

**safety/SKILL.md** — Need:
- Local navigation hazards (shoals, bars, currents)
- Bridge clearances
- Weather hazards specific to this area
- Manatee/wildlife zones
- Key channel markers and navigation notes

**events/SKILL.md** — Need:
- Major annual events (boat shows, tournaments, festivals)
- Recurring events (weekly sunset sails, fishing derbies)
- Where to find local event info (papers, tourism boards, social media)

**lifestyle/SKILL.md** — Need:
- Local boating culture (what makes boating HERE different)
- Popular activities beyond fishing
- Social scene, boat clubs, community

### 6. Write MEMORY.md
Seed with:
- Coverage status (new desk = empty)
- Any local intel gathered during research
- Key seasonal notes

### 7. Verify Quality
Before declaring done:
- [ ] Every skill has real local data, not placeholders like "research needed"
- [ ] Species have correct FL/NC regulations (check myfwc.com or NC wildlife)
- [ ] Restaurant names are real and dock-accessible
- [ ] NOAA zones are correct for this area
- [ ] Port32 amenity details are accurate (check port32marinas.com)
- [ ] Location hierarchy tags match what's in the system

### 8. Register as OpenClaw Agent

Each desk is a real OpenClaw agent on this gateway. Registration requires three things:

**a) Create the agent directory:**
```bash
mkdir -p /root/.openclaw/agents/<market-name>-desk/agent
```

**b) Copy auth profiles** (so the desk can use the same API keys):
```bash
cp /root/.openclaw/agents/mr-content/agent/auth-profiles.json \
   /root/.openclaw/agents/<market-name>-desk/agent/auth-profiles.json
```

**c) Register in gateway config** via `config.patch`:
Add an entry to `agents.list` with:
```json
{
  "id": "<market-name>-desk",
  "name": "<market-name>-desk",
  "workspace": "/root/workspaces/mr-content/desks/<market-name>",
  "agentDir": "/root/.openclaw/agents/<market-name>-desk/agent"
}
```

Use the `gateway` tool with `action: config.patch` to add the agent. The workspace points to the desk directory (where SOUL.md, AGENTS.md, skills/ live). The agentDir holds auth credentials.

**d) Gateway restarts automatically** after config.patch. The new desk agent will be available for `sessions_send` and will show in `sessions_list`.

**Example** — registering a Tampa Bay desk:
```
gateway config.patch → agents.list append:
{
  "id": "tampa-bay-desk",
  "name": "tampa-bay-desk",
  "workspace": "/root/workspaces/mr-content/desks/tampa-bay",
  "agentDir": "/root/.openclaw/agents/tampa-bay-desk/agent"
}
```

### 9. Verify Registration
After gateway restart:
- `sessions_list` should show the new agent
- `sessions_send` to the new agent with a test message
- Confirm agent responds with correct SOUL.md personality and can access its skills

## Reference
- Tampa Bay desk (complete prototype): `desks/tampa-bay/`
- Location intel database: `skills/location-intel/SKILL.md`
- Location hierarchy: see MEMORY.md "Location Hierarchy" section
- Desk template checklist: `desks/DESK-TEMPLATE.md`
