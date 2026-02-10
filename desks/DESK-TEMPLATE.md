# Desk Template

Use this to create new market desk agents. Copy the `tampa-bay/` directory structure and customize.

## Directory Structure
```
desks/<market-name>/
  SOUL.md              # Agent personality, territory, values
  AGENTS.md            # Operating rules, workflow, card hierarchy
  MEMORY.md            # Coverage tracking, local intel, feedback log
  TOOLS.md             # CLI reference (shared across desks)
  skills/
    fishing/SKILL.md   # Species, seasons, spots, card types
    weather/SKILL.md   # NOAA zones, local patterns, card types
    dining/SKILL.md    # Dock-and-dine, restaurants, card types
    destinations/SKILL.md  # Day trips, sandbars, islands, card types
    port32/SKILL.md    # Marina amenities, services, card types
    safety/SKILL.md    # Local hazards, navigation, card types
    events/SKILL.md    # Tournaments, shows, local happenings
    lifestyle/SKILL.md # Culture, gear, new boater content
```

## Markets to Build
- [x] Tampa Bay (tierra-verde, tampa)
- [ ] SW Florida (naples, marco-island, cape-coral)
- [ ] SE Florida (fort-lauderdale, lighthouse-point, palm-beach-gardens)
- [ ] NE Florida (jacksonville)
- [ ] Crystal Coast NC (morehead-city)

## Customization Checklist
1. SOUL.md — territory, waters, key spots, culture
2. AGENTS.md — location hierarchy tags for this market
3. Each skill — local species/restaurants/spots/hazards/events
4. MEMORY.md — seed with any known intel from location-intel skill
5. NOAA zones in weather skill
6. Port32 amenity details in port32 skill
