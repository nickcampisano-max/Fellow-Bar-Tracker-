# Fellow Bar — Status & Open Items

**Current as of P11 Week 1 in progress (10/1/26)**

Live tracker: nickcampisano-max.github.io/Fellow-Bar-Tracker-/

---

## ⚠️ Read this first

### How to read the tracker
**Use the Claude in Chrome tools** (`navigate`, then read localStorage). **Do NOT use web_fetch** — it doesn't execute JavaScript and returns an empty template that looks like there's no data.

- Current period: `fellowbar_tracker_v5` — `{version, setup, weeks}`
- Archive: `fellowbar_archive_v1` — `{version, periods[]}`. Holds P1–P10.
- Weekly data: `weeks.p[1-4]` purchases, `weeks.s[1-4]` sales, 7-day arrays Mon→Sun

### ⚠️ Typed values don't save until an event fires
Values entered in the page do **not** persist to localStorage until a save event fires. At the P9 rollover the ending inventory was live in the form and blank in storage — a reload would have destroyed it. **Always check both and reconcile before writing anything.**

### Targets vs Expected Rates — these are now two different things
As of the 10/1/26 build:

- **Cost Targets** (25 / 25 / 14 / 5, blended 21.5%) = the **goals**. Used for cost % colouring, the period summary, pass/fail, and the email recap.
- **Expected Pour Rates** (34.93 / 23.50 / 13.56 / 4.23) = the **forecast**. Used only for estimated inventory, drift, the projection engine, and weekly order sizing.

Before this split, the estimate used the goal as its forecast, which is why mid-period drift was wrong by design. Leave an Expected field blank and it falls back to the target — that's the undo.

### Mid-period, only two numbers mean anything
**Raw cost % (purchases ÷ sales)** and **drift**. Adjusted pour cost is not knowable until the count. Never promise a period result before ending inventory is entered.

**Treat drift as a range, not a point.** Out-of-sample error has been ~±$300 on a ~$7,400 shelf. An estimate landing within $300 of the count is noise, not accuracy.

---

## Core Principles

**Adjusted pour cost** = (Beginning + Purchases − Ending) ÷ Sales. Measures what's *consumed*. Only real at period close.

**Adjusted pour cost is a property of pouring, not purchasing.** Buying more and letting it sit raises purchases and ending inventory by the same dollar — they cancel. Ordering more cannot lower the cost; it only raises raw cost and widens the gap between raw and adjusted.

**Land each category flat to opening.** When inventory ends flat, raw ≈ adjusted, so the weekly number is trustworthy.

**Beer is kegs and pony kegs only.** Plan it in units — pints on hand ÷ pints per day = days until that tap blows. Half-barrel ≈ 124 pints; 20L/sixtel ≈ ⅓ of that. Dollars hide an empty line.

**Build to a cover target, not a dollar target.** Weeks of cover at projected busy-season volume is the test.

**Arizona inverse seasonality** — busy Oct–April, slow May–Sept. **Note:** Nick expects October 2026 to run roughly flat to September, not the usual jump.

**Breakeven is $36,000/week TOTAL sales** (food + bar).

---

## Goals

| | |
|---|---|
| Vanessa's stated target | **21.5%** adjusted |
| Internal goal | **20% or under** adjusted, every period |

The target set (25 / 25 / 14 / 5) blends to ~21.3%, so hitting 20% requires at least one category to beat its target. In P10 liquor did the work at 12.41%.

---

## Period Results

| | P9 (audited) | P10 |
|---|---|---|
| Sales | $30,169 | **$34,521.01** |
| Purchases | $6,489.62 | **$6,412.03** |
| Raw cost | 21.51% | **18.57%** |
| Beginning | $7,860.89 | $7,826.00 |
| Ending | $7,826.00 | $7,406.86 |
| Drift | −$34.89 | −$419.14 |
| **Adjusted** | **21.63%** | **19.79%** |

### Actual pour rates by category

| | P9 | P10 | 2-period avg |
|---|---|---|---|
| 🍺 Beer | 38.22% | 31.64% | **34.93%** |
| 🍷 Wine | 24.71% | 22.30% | **23.50%** |
| 🥃 Liquor | 14.71% | 12.41% | **13.56%** |
| ⚗️ Consumables | 3.60% | 4.87% | **4.23%** |

**Beer has missed its 25% target in both measured periods.** Wine, liquor and consumables beat theirs every time. The targets are wrong in a consistent direction — which is exactly why the old estimate missed the same way each period.

---

## What went wrong with the P10 estimate

Mid-period drift read **−$927** against an actual **−$419**. More than double.

| | Est. ending | Actual | Miss |
|---|---|---|---|
| 🍺 Beer | $364 | $212 | +$152 |
| 🍷 Wine | $2,101 | $2,390 | −$290 |
| 🥃 Liquor | $3,421 | $3,763 | −$343 |
| ⚗️ Consumables | $1,013 | $1,041 | −$28 |
| **Total** | **$6,899** | **$7,407** | **−$508** |

Cause: the estimator assumed every category poured at target. Wine and liquor beat theirs, so real inventory was higher than predicted; beer missed, pulling the other way. The errors didn't cancel.

**Fix shipped 10/1/26** — the Expected Pour Rates split described above. Backtested on P10 the miss drops from −$508 to −$317, and the clean out-of-sample test (P9 rates predicting P10) was −$329. **Better, not solved.** Expect ±$300.

---

## P11 (9/28/26 – 10/25/26) — IN PROGRESS

**Opening inventory $7,406.86** — Beer $211.87 · Wine $2,390.27 · Liquor $3,763.33 · Consumables $1,041.39

Expected weekly sales: $8,000 · Prior period reference: $8,065 / $7,596.51 / $7,683 / **$11,176.50**

⚠️ **ppW4 includes the Wednesday event.** The tracker uses Week 4 as the Week 1 ordering reference, so it overstates. Don't take the Week 1 game-plan number at face value.

**Week 1 partial (Mon 9/28 – Wed 9/30):** sales $4,330.50 · purchases $1,880.08. Three days only, with ordering front-loaded — don't read a cost % off it yet.

---

## Open Items

1. **Push the new `index.html` to GitHub** and hard-refresh. Verify: Expected Pour Rates section appears, Cost Targets unchanged, Week 1 data intact — then refresh *again* and confirm the rates persist.
2. **Compare P11 Week 1 drift** under the new rates against what the old target math would have said. First real test of the fix.
3. **Weekly invoice reconciliation** against MarginEdge before sending Vanessa anything. Every material error so far has been a missing or late invoice, and they all flatter the number.
4. **Roll the Expected Rates to a 3-period average** when P11 closes, and report whether the estimate's miss improved.
5. **Log events** in the tracker's Event field so party nights don't enter the velocity engine as run rate.
6. **Decide on the beer target.** 25% hasn't been met in two measured periods. Small in dollars, wrong as a number.

---

## Settled — do not reopen

- **Beer investigation: CLOSED** at the P9 audit. Do not raise Stella/Shope or the beer count sheet.
- **Cost targets confirmed** — Beer 25 / Wine 25 / Liquor 14 / Consumables 5. Liquor at 14% is achievable. The old 33% wine / 20% liquor figures are stale.
- **No mid-period physical counts.**
- The tracker's "order this week" figure **excludes consumables by design**, so it reads lower than the emailed all-in target.
- Relabeling the pour-cost tile: offered, declined.

---

## Vanessa Weekly Email Format

1. Opening line — how the week went, sales figure, raw cost, brief genuine note
2. **Where we stand** — what the week's ordering did to inventory, risks, drift vs opening
3. Per-category game plan with dollar targets — 🍺 Beer · 🍷 Wine · 🥃 Liquor · ⚗️ Consumables
4. **Total order target** and **Sales target**
5. **Big picture** — period health, category risk, what the remaining weeks need
6. Signed: Nick

Tone: direct, warm, operational. No jargon. Specific dollar figures throughout.

**Pre-frame any week whose raw cost will look unusual.** A drawdown week reads low, a restock week reads high, and both are correct. Judge the period, not the week.
