---
name: skip-cli
description: Full Skip CLI command reference for the editor agent.
metadata: { "openclaw": { "emoji": "💻", "requires": { "bins": ["skip"] } } }
---

# Skip CLI Reference v0.4.0

The `skip` CLI connects to the Skip API (Convex backend).

**API**: `https://api.cells.md` (prod) | `https://calm-basilisk-210.convex.site` (dev)
**Auth**: `SKIP_API_TOKEN` env var

## Cards

### Create a card
```bash
skip card create \
  --agent-id=<agent-id> \
  --title="Short headline, 3-6 words" \
  --subtext="Preview text, 2-3 sentences" \
  --content="Full markdown content" \
  --category=<category> \
  --location=<location> \
  --freshness=<timely|evergreen> \
  [--image=<url>] \
  [--bg-color=<hex>] \
  [--expires-at=<ISO-8601|unix>] \
  [--date=<YYYY-MM-DD>] \
  [--style-id=<id>] \
  [--data=<json>] \
  [--summary=<text>] \
  [--task-id=<id>] \
  [--json]
```

### List/query cards
```bash
skip card list [--agent=<id>] [--limit=<n>] [--json]
skip card list-today [--json]
skip card get <cardId> [--json]
skip card check --agent-id=<id> [--date=YYYY-MM-DD] [--json]
```

### Update/manage cards
```bash
skip card update <cardId> [--title=X] [--subtext=X] [--content=X] [--image=X] [--category=X] [--location=X] [--freshness=X] [--expires-at=X] [--json]
skip card archive <cardId> [--json]
skip card unarchive <cardId> [--json]
skip card audit [--agent=<id>] [--location=<loc>] [--json]
skip card regen-image <cardId> [--prompt="..."] [--style=<id>] [--timeout=<sec>] [--json]
skip card pull --user=<userId> [--json]
```

### Categories
`fishing`, `safety`, `port32-marinas`, `port32`, `maintenance`, `activities-events`, `destinations`, `events`, `lifestyle`, `dining`, `regulations`, `guide`, `safety-equipment`, `weather-tides`

### Locations
Marina: `tierra-verde`, `tampa`, `naples`, `marco-island`, `cape-coral`, `fort-lauderdale`, `lighthouse-point`, `palm-beach-gardens`, `jacksonville`, `morehead-city`
Market: `tampa-bay`, `sw-florida`, `se-florida`, `ne-florida`, `crystal-coast`
State: `florida`

## Beat Reporters

### Create permanent beat agent
```bash
skip beat create --topic=<topic> --location=<location> [--name="..."] [--voice="..."] [--json]
```

### Dispatch existing beat agent
```bash
skip beat dispatch --agent-id=<agent-id> --prompt="..." [--json]
```

### One-off dispatch (generic reporter)
```bash
skip beat run --topic=<topic> --location=<location> [--instructions="..."] [--json]
```

### List beat agents
```bash
skip beat list [--json]
```

## Agents
```bash
skip agent list [--type=<type>] [--json]
skip agent get <agentId> [--json]
skip agent run <agentId> "<prompt>" [--json]
```

## Sources (per agent)
```bash
skip source list --agent-id=<id> [--json]
skip source add --agent-id=<id> --name="..." --url="..." [--notes="..."] [--json]
skip source remove <sourceId> [--json]
```

## Images (async generation)
```bash
skip image queue [--prompt="..."] [--json]
skip image status <imageId> [--json]
skip image wait <imageId> [--timeout=<sec>] [--json]
```

## Styles
```bash
skip style list [--json]
skip style get <styleId> [--json]
```

## Users
```bash
skip user list [--json]
skip user get <userId> [--json]
skip user update <userId> [--json]
skip user onboard-status <userId> [--json]
skip user upload-boat-image <userId> [--json]
```

## Advisors
```bash
skip advisor list [--json]
skip advisor memories [--user=<userId>] [--json]
skip advisor pool [--json]
```

## Briefings
```bash
skip briefing list [--user=<userId>] [--limit=<n>] [--json]
skip briefing get <briefingId> [--json]
skip briefing assemble [--json]
skip briefing validate [--json]
```

## Editions
```bash
skip edition list [--json]
skip edition get <editionId> [--json]
```

## Messages
```bash
skip message send [--json]
```

## JSON Output
All commands support `--json` for machine-readable output. Always use `--json` when parsing programmatically.

## Environment Variables
- `SKIP_API_TOKEN` — Admin token for authenticated access
- `SKIP_API_URL` — Override API URL
- `AGENT_ID` — Default agent ID (skip `--agent-id` flag)
