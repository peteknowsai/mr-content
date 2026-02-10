# Web Research — Skip Content Research

How to research content for Skip cards using web tools.

## Tools Available
- `web_search` — Brave Search API. Fast, good for news and local results.
- `web_fetch` — Fetch a URL and extract readable content as markdown.

## Research Patterns

### Fishing Reports
```
web_search: "[location] fishing report [month] [year]"
web_search: "[location] fishing report this week"
web_search: "[species] [location] fishing"
web_fetch: [local charter captain blog URLs]
web_fetch: https://myfwc.com/fishing/ (for regulations)
```

### Weather & Marine Conditions
```
web_fetch: https://api.weather.gov/zones/forecast/AMZ154 (Tampa Bay example)
web_fetch: https://tidesandcurrents.noaa.gov/... (tide data)
web_search: "[location] marine forecast"
```

### Restaurants & Dining
```
web_search: "[location] waterfront restaurant dock boat"
web_search: "[restaurant name] dock dine boat"
web_search: "[location] dock and dine"
```

### Events
```
web_search: "[location] boat show [year]"
web_search: "[location] fishing tournament [month] [year]"
web_search: "[location] waterfront festival [year]"
```

### Destinations
```
web_search: "[island/sandbar name] boating guide"
web_search: "[location] best anchorages"
web_search: "[location] day trip boat"
```

### Regulations
```
web_fetch: https://myfwc.com/fishing/saltwater/recreational/ (FL fishing regs)
web_search: "Florida [species] fishing regulations [year]"
web_search: "[state] boating laws [year]"
```

### Magazine Articles (for inspiration — do NOT plagiarize)
```
web_search: site:saltwatersportsman.com "[topic]"
web_search: site:floridasportsman.com "[topic]"
web_search: site:boatingmag.com "[topic]"
```

## Research Rules
1. **Verify before writing.** If you can't confirm a fact, say "conditions unknown" or skip it.
2. **Cite sources.** Include source URLs at the bottom of every card.
3. **No fabrication.** If you don't have current fishing data, write an evergreen guide instead of a fake report.
4. **Check dates.** Make sure event info is for the current year. Old events are worse than no events.
5. **Cross-reference.** Don't trust a single source for factual claims (regulations, restaurant hours, event dates).
