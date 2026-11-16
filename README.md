# pantry-skill — a Hermes Agent skill tap

Contains one skill: **`pantry-meal-planner`** — plans 5 days of dinners that use up expiring pantry items first (respecting dietary rules), with recipes from [TheMealDB](https://www.themealdb.com/api.php) and a grocery list grouped by store aisle.

## Install (Hermes Agent)
```bash
hermes skills tap add Shankar-Bharadwaj/pantry-skill
hermes skills install Shankar-Bharadwaj/pantry-skill/pantry-meal-planner
```
Then ask: *"Make me a 5-day meal plan from my pantry"* (put your pantry at `~/pantry.json` — format in `skills/pantry-meal-planner/references/example-pantry.json` — or paste the list).

Needs Hermes's standard `web` (for TheMealDB) and `file` toolsets. No API keys.

Built for CSCI 599 (Agentic AI) Assignment 3, USC, Fall 2026. MIT licensed.
