---
name: pantry-meal-planner
description: Plan 5 days of dinners that use up expiring pantry/fridge items first, respecting dietary rules, and produce a grocery list grouped by store aisle. Use when the user asks for a meal plan, what to cook, how to use up food before it expires, or a grocery/shopping list.
version: 1.0.0
author: Shankar Bharadwaj (CSCI 599, USC)
metadata:
  hermes:
    tags: [meal-planning, pantry, groceries, food-waste, cooking]
    category: productivity
---

# Pantry & Meal-Plan Optimizer

Turns "what's in my fridge + my diet rules" into a 5-day dinner plan that **uses the soonest-expiring ingredients first**, with recipes from TheMealDB (free, no key) and a **grocery list grouped by aisle**.

## When to Use
- "Make me a meal plan", "what should I cook this week", "use up what's in my fridge"
- "What groceries do I need?" after a plan
- A briefing asks which pantry items expire soon

## Inputs
1. **Pantry list** — in priority order:
   - a pantry JSON file the user names, else `~/pantry.json` (use `read_file`);
   - otherwise ask the user to paste items (name, quantity, expiry date).
   Format: `[{"item": "spinach", "qty": 1, "unit": "bag", "expiry": "YYYY-MM-DD"}, ...]` — see `references/example-pantry.json`.
2. **Dietary rules** — from the user's memory/profile (e.g. vegetarian, allergies, dislikes) and anything said in the request. Rules are hard constraints.

## Procedure
1. Load the pantry. Compute days until expiry for each item (today's date in the user's timezone). Sort ascending. Items expiring within 3 days are **priority items**.
2. For the 5 highest-priority ingredients, look up recipes on TheMealDB with the web tool (`web_extract` the URL):
   - by ingredient: `https://www.themealdb.com/api/json/v1/1/filter.php?i=<ingredient>` (use underscores for spaces, e.g. `bell_pepper`)
   - details: `https://www.themealdb.com/api/json/v1/1/lookup.php?i=<idMeal>` → `strMeal`, `strCategory`, `strIngredient1..20`, `strMeasure1..20`
   - vegetarian candidates: `https://www.themealdb.com/api/json/v1/1/filter.php?c=Vegetarian`
3. Choose **5 dinners** (one per day, no repeats) that:
   - obey every dietary rule (check *all* ingredients of each recipe — reject any with a forbidden one);
   - use priority items **on or before** their expiry day (earliest-expiring items in day 1–2);
   - maximize use of existing pantry items.
   If TheMealDB has no fitting recipe for a priority item, propose a simple dish yourself and label it "(own suggestion)".
4. Build the **grocery list**: every recipe ingredient not covered by the pantry (case-insensitive, ignore water/salt/pepper/oil), deduplicated, grouped by aisle using `references/aisles.json` (keyword match; unmatched → "Other").
5. Output (concise):
   - **Day 1–5**: dish name (TheMealDB id or "own suggestion") — *uses up:* items, with expiry
   - **Use soon (≤3 days)**: any priority item not used, with an idea
   - **Grocery list by aisle**: `Aisle: item, item`

## Pitfalls
- Never invent a TheMealDB id; if a lookup fails, say "own suggestion".
- TheMealDB `filter.php` returns only names/ids — always `lookup.php` before checking diet rules.
- Don't put pantry items on the grocery list unless the quantity is clearly insufficient.
- Expired items (negative days): mention them under "Check / discard", don't plan with them.

## Verification
- Each priority item appears in a day on/before its expiry date, or under "Use soon".
- No recipe violates a dietary rule.
- Every grocery item maps to an aisle heading.
