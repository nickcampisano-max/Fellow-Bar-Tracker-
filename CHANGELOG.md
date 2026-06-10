# Fellow Bar — Period Tracker Changelog

All notable changes to the Fellow Bar 28-Day Period Tracker are documented here.

---

## [v5.7] — 2026-06-10
### Added
- **Event / Buyout Sales field** — per-week input to flag one-time event revenue (buyouts, private events, etc.). Amount is excluded from velocity/projection calculations so trend data stays accurate. Pour cost math is unaffected — event revenue still counts as real sales. Forecast header shows "event-adjusted" when active.

## [v5.6] — 2026-06-10
### Added
- **Inventory Drift narrative** — plain-language alert in the forecast section describing where inventory is tracking and what to do about it. References the weekly order targets (which already account for sales velocity) rather than giving a separate trim number. Auto-hides once actual ending inventory is entered at period close.
  - ✅ Green: balanced (±$200) — "stay the course"
  - 📦 Yellow: building — "purchasing ahead of cost rate, follow weekly targets"
  - 📉 Green: drawing down slightly — "healthy sign, watch stock levels"
  - ⚠️ Yellow: drawing down hard (>$500) — "sales outpacing purchasing, verify stock"

## [v5.5] — 2026-06-10
### Added
- **Prior Period Reference section** in Period Setup — four weekly sales inputs (one per week) from the prior period. Used to display daily average reference rows in the daily tracker.
- **Reference rows in daily tracker** — two muted rows appear below Total Sales:
  - *↑ Prior Week* — exact daily sales from the previous week within the current period (automatic, weeks 2–4 only, no input needed)
  - *↑ Prior Period Avg/day* — prior period weekly total ÷ 7 shown as a flat daily average, with weekly total in the right column

## [v5.4] — 2026-06-10
### Added
- **Expected Weekly Sales field** in Period Setup (default: $8,000) — used as the velocity baseline for projections while Week 1 is still in progress. Distributes across Beer (7%) / Wine (29%) / Liquor (64%) based on P6 actuals. Once Week 1 completes, projections automatically switch to real velocity.
### Fixed
- Projection weekly targets showing ~$6,286 sell / ~$971 order during Week 1 in-progress — was using partial week data (e.g. Monday only = $837) as velocity, causing severely understated targets.

## [v5.3] — 2026-06-10
### Fixed
- **Reset button** now clears both `fellowbar_tracker_v5` and `fellowbar_tracker_v4` localStorage keys, then reloads the page — loading cleanly from HTML defaults (Period 7 values).
- **Removed v4 migration fallback** from `loadFromStorage` — old Period 6 data was re-migrating on every page load after Reset, overwriting Period 7 defaults.
- Previously, Reset only zeroed in-memory state and re-saved Period 6 setup fields back to localStorage. Page reload would then find v4 key, re-migrate, and restore all old data.

## [v5.2] — 2026-06-08
### Changed
- **Period 7 defaults** set in HTML: Period name "Period 7", start date 2026-06-08, beginning inventory Beer $1,007.53 / Wine $2,869.13 / Liquor $4,696.00 (from Vanessa's final recount confirming P6 at 21.41%).

## [v5.1] — 2026-06-07
### Fixed
- **Inventory drift math** — was using per-category cost targets weighted by sales mix (blended ~24–25%), causing drift to show ~-$947. Corrected to use total purchases (including Consumables) vs. blended 21.5% of total sales: `drift = totP - (totS × 21.5%)`. Result: ~-$23 (essentially balanced).
- **Scenario planner disappearing at period end** — was wrapped in `if(remWeeks.length > 0)`. Changed to `if(true)` so it always renders.
- **Scenario planner order target wrong** — old code used remaining-budget math. Fixed to simple: `wkP = wkSales × (tgt/100)`.
- **Projection showing 28% instead of ~20%** — old code used inventory-rebuild math. Replaced with pour-cost-target approach: `remainingBudget = max(0, tgt% × totalPeriodSales − actualCostPurch)`.
### Added
- **Estimated ending inventory breakdown** in Inventory Drift KPI tile — shows Beer / Wine / Liquor / Est. Total.
- **Custom % column** in Scenario Planner — allows entering any target %, weekly sales, or order target manually for what-if planning in late-period weeks.
- **Pinned "Your Estimate" column** in Scenario Planner — only updates when manually changed, not on every render.

## [v5.0] — 2026-05 (session rebuild)
### Added
- **localStorage persistence** — state auto-saves to `fellowbar_tracker_v5` key on every input. Data restored on page load with a toast notification.
- **Velocity baseline fix** — projections now use only fully complete weeks (Sunday sales/purchases > 0). Incomplete current week excluded from average.
- **Rebuilt forecast section** — rewritten for store-level managers. Replaced operator-facing category projections with plain-language weekly target cards.
- **In-progress week card** — shows ordered/sold so far vs. weekly target with "still needed" for both purchases and sales.
- **Per-week target cards** for remaining weeks (Weeks 2–4) showing order and sell targets by category.
- **Pour-cost-target projection** — back-calculates remaining purchase budget to land full period at blended target (21.5%), given actual spend and projected sales. Replaced inventory-rebuild model.
- **Scenario Planner** — shows Your Estimate / Velocity / Stretch scenarios with order target and cost % for each.
- **Status badge** — ✅ On Track / ⚠️ Stay Tight / 🔴 Push Sales based on projected full-period pour cost.
### Changed
- Renamed `Shamrock/Mutual` → `Consumables` throughout.
- All numeric inputs changed from `type="number"` to `type="text" inputmode="decimal"` to eliminate browser spinner arrows and allow comma-formatted values.
- Added `parseNum()` helper to strip commas before parsing all numeric inputs.
### Migration
- On first load, checks for `fellowbar_tracker_v4` key and migrates data, renaming Shamrock/Mutual → Consumables in purchase rows.

---

## [v4] — 2026-04 (prior version)
### Core features established
- 28-day period structure (4 weeks × 7 days)
- Daily purchase and sales entry: Beer, Wine, Liquor, Shamrock/Mutual (purchase-only)
- Per-category cost targets: Beer 25% / Wine 33% / Liquor 20% / Consumables 3%
- Blended target: 21.5%
- Period Setup: period name, start date, beginning inventory (Beer/Wine/Liquor), ending inventory (period close)
- KPI tiles: Period Purchases, Period Sales, Adj. Pour Cost (est. or locked), Inventory Drift
- Week tabs with auto-calculated date ranges
- Daily Cost % by Category rows (color-coded vs. target)
- Consumables Cost % row (measured against Liquor sales)
- Adjusted pour cost: estimated mid-period using cost targets, locked at close when actual ending inventory entered
- Period Summary table (beginning/ending/difference/purchases/sales/cost%/adj pour cost)
- Export to CSV
- Dark mode toggle
- Responsive layout (375px mobile)
- Satoshi font / Nexus Design System (warm beige, teal accent)

---

## Math Reference

### Estimated Ending Inventory (per category, mid-period)
```
Est. Ending = Beginning Inventory + Purchases to Date − (Sales to Date × Category Cost Target %)
```

### Adjusted Pour Cost
```
Adj. Pour Cost = (Beginning Inventory + Purchases − Ending Inventory) ÷ Sales
```
- Mid-period: uses estimated ending inventory (labeled "est.")
- Period close: uses actual ending inventory from MarginEdge (locked)

### Inventory Drift
```
Drift = Total Purchases (incl. Consumables) − (Total Sales × Blended Target %)
```

### Period Cost % (Raw)
```
Period Cost % = Purchases ÷ Sales
```

### Pour-Cost Projection (remaining weeks)
```
Total Purchase Budget = Blended Target % × Projected Full-Period Sales
Remaining Budget = max(0, Total Purchase Budget − Actual Purchases to Date)
Per-Week Budget = Remaining Budget ÷ Weeks Remaining
```
Per-category allocation weighted by `(catTarget% × projSales)` for each category.
