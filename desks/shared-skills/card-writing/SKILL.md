# Card Writing — Skip Content Cards

How to create, check, update, and manage content cards in the Skip system.

## Before Writing Any Card

1. **Check if you already wrote one today** for this beat:
```bash
skip card check --agent-id=<your-agent-id> --date=<YYYY-MM-DD> --json
```

2. **Research first.** Use `web_search` and `web_fetch` to gather real, current data. Never fabricate fishing reports, restaurant details, or event dates.

## Creating a Card

```bash
skip card create \
  --agent-id=<your-agent-id> \
  --title="Short headline, 3-6 words" \
  --subtext="Preview text, 2-3 sentences. This shows in the card feed." \
  --content="Full markdown body" \
  --category=<category> \
  --location=<location> \
  --freshness=<timely|evergreen> \
  [--expires-at="YYYY-MM-DDT00:00:00Z"] \
  [--bg-color="#4A5568"] \
  [--style-id=<styleId>] \
  [--json]
```

### Required Fields
- **title**: 3-6 words. Punchy. Not clickbait.
- **subtext**: 2-3 sentences. This is the hook in the feed. Make it count.
- **content**: Full markdown. This is the card body — thorough, useful, local.
- **category**: One of the approved categories (see below)
- **location**: Geographic tag at the right level (see below)
- **freshness**: `timely` (will expire) or `evergreen` (stays relevant)

### Categories
- `fishing` — reports, species spotlights, spot guides
- `weather-tides` — marine forecasts, storm prep, tide guides
- `destinations` — day trips, anchorages, island guides
- `dining` — dock-and-dine, waterfront restaurants
- `port32-marinas` — marina amenities, member tips, first-timer guides
- `port32` — Port32 brand content
- `safety` — navigation hazards, regulations, equipment
- `events` — tournaments, boat shows, festivals (use `timely` + `expires-at`)
- `activities-events` — broader activities and happenings
- `lifestyle` — culture, gear, boating life
- `maintenance` — engine care, hull cleaning, seasonal prep
- `regulations` — fishing regs, boating laws, licensing
- `guide` — how-to guides

### Location Tags
**Marina-level** (specific to one marina):
`tierra-verde`, `tampa`, `naples`, `marco-island`, `cape-coral`, `fort-lauderdale`, `lighthouse-point`, `palm-beach-gardens`, `jacksonville`, `morehead-city`

**Market-level** (shared across marinas in a market):
`tampa-bay`, `sw-florida`, `se-florida`, `ne-florida`, `crystal-coast`

**State-level** (applies broadly):
`florida`

**Rule**: Use the most specific location that fits. A fishing report for Tampa Bay waters = `tampa-bay`. A guide to Hula Bay Club = `tampa`. Florida-wide anchoring regulations = `florida`.

### Freshness & Expiry
- **Timely cards** (fishing reports, weather, events): Set `--freshness=timely` and `--expires-at` to when the content goes stale
  - Fishing reports: expire in 7 days
  - Weather briefings: expire in 2-3 days
  - Events: expire day after the event
- **Evergreen cards** (destination guides, safety tips, marina guides): Set `--freshness=evergreen`, no expiry

## Card Content Quality Standards

### Structure
```markdown
# Title (matches --title)

Opening hook — 1-2 sentences that pull the reader in.

## Section Heading
Body content with specifics. Names, numbers, directions.

## Another Section
More useful detail.

---

**Sources:**
- [Source Name](URL) — brief note on what this sourced
```

### Do's
- Lead with the most useful information
- Use specific names, numbers, locations
- Include actionable details (where to anchor, what bait to use, reservation tips)
- Reference Port32 amenities naturally when relevant
- Cite sources at the bottom for factual claims
- Break up long content with headers and bullet points

### Don'ts
- Don't write generic content that could apply anywhere
- Don't fabricate data (fishing conditions, restaurant menus, event details)
- Don't make cards too long — aim for 500-1500 words depending on topic
- Don't be salesy about Port32 — helpful > promotional
- Don't duplicate cards that already exist (check first)

## Images

Cards get auto-generated images. You can influence the image by:
- Setting `--style-id` to one of the 133 available styles
- Setting `--bg-color` for the card background

To regenerate an image:
```bash
skip card regen-image <cardId> --prompt="custom image prompt" [--style=<styleId>]
```

### Style Selection Tips
Match style to content mood:
- Fishing reports → `style_field-guide-plate`, `style_warm-watercolor`
- Weather/storm → `style_storm-drama`, `style_lightning-strike`
- Destinations → `style_1950s-travel-poster`, `style_golden-hour-glow`
- Events → `style_gig-poster`, `style_memphis-80s`
- Safety → `style_scratchboard`, `style_blueprint-technical`
- Dining → `style_coral-reef-vibrant`, `style_impressionist-harbor`
- Port32 → `style_isometric-marina`, `style_cool-ocean-minimal`

## Managing Existing Cards

```bash
# List your cards
skip card list --agent=<your-agent-id> --json

# Update a card
skip card update <cardId> --title="New Title" --content="Updated content" --json

# Archive (soft delete)
skip card archive <cardId> --json

# Restore
skip card unarchive <cardId> --json

# Audit for issues
skip card audit --agent=<your-agent-id> --json
```

## After Writing

1. Confirm the card was created successfully (check response)
2. Log what you wrote in your memory (topic, location, date)
3. Note any gaps you identified while researching — tell Mr. Content
