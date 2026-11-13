# Apex Fresh: Weekly Meal-Kit Production Mix (Linear Programming)

An Excel Solver model that picks the weekly production mix of four meal kits to maximise profit under labour, cold-storage and avocado limits. It includes sensitivity analysis and four "buy more capacity" scenarios, each tested against its cost.

**File:** `Apex_Fresh_LP.xlsx` | **Solver:** Simplex LP (continuous variables, non-negative)

## Model

**Decision variables** (kits per week): Vegan (V), Keto (K), Balanced (B), Protein+ (P)

**Objective:** maximise `15V + 18K + 12B + 20P` (profit per kit in $)

| Constraint | V | K | B | P | Limit |
|---|---|---|---|---|---|
| Prep time (min) | 3 | 2 | 2 | 4 | ≤ 7,200 (120 h) |
| Assembly time (min) | 2 | 4 | 2 | 3 | ≤ 6,000 (100 h) |
| Cold storage (L) | 2 | 1.5 | 2.5 | 2 | ≤ 6,000 |
| Usable avocado (g) | 200 | 300 | 0 | 100 | ≤ 200,000 (250 kg × 80% yield) |
| Balanced share | -0.4 | -0.4 | 0.6 | -0.4 | ≥ 0, i.e. B ≥ 40% of all kits |

## Baseline Result

**Maximum weekly profit: $38,988.68**

| Kit | Units/week |
|---|---|
| Vegan | 649.06 |
| Keto | 30.19 |
| Balanced | 1,373.58 |
| Protein+ | 611.32 |

Prep, assembly, cold storage and avocado are all **binding**. The 40% Balanced rule is **not** binding (Balanced is 51.6% of output, slack 307.9 units).

### Sensitivity

| Resource | Shadow price | Valid for RHS range |
|---|---|---|
| Assembly time | $2.755 / min | -94 to +1,075 min |
| Prep time | $2.585 / min | -1,473 to +133 min |
| Cold storage | $0.528 / L | -716 to +400 L |
| Avocado | $0.0034 / g | -32,000 to +129,524 g |

Profit-coefficient ranges over which the current mix stays optimal: Vegan $14.36-$19.56, Keto $14.40-$25.00, Balanced $11.07-$13.64, Protein+ $13.77-$21.00.

## Scenarios

Net gain = change in weekly profit minus the scenario's cost, as entered in the workbook.

| # | Change | Cost | New profit | Profit change | **Net gain** |
|---|---|---|---|---|---|
| 1 | Assembly time 6,000 → 7,200 min | $500 | $41,950.00 | +$2,961.32 | **+$2,461.32** |
| 2 | Cold storage 6,000 → 7,000 L | $600 | $39,200.00 | +$211.32 | **-$388.68** |
| 3 | Balanced minimum 40% → 30% | $250 | $38,988.68 | $0.00 | **-$250.00** |
| 4 | Usable avocado 200,000 → 237,500 g | $300 | $39,116.04 | +$127.36 | **-$172.64** |

**Takeaway:** only Scenario 1 pays off. Assembly time is the most valuable constraint ($2.75/min). The optimal mix then shifts to 0 Vegan, 375 Keto, 1,475 Balanced and 875 Protein+, and prep time becomes the bottleneck.

Notes:
- Scenario 1 raises assembly by 1,200 min, but the shadow price only holds for the first 1,075. The extra capacity was re-solved, not extrapolated.
- Scenario 3 gains nothing because the mix rule was already slack.
- Scenarios 2 and 4 add resources that are worth less than they cost.

### Avocado yield what-ifs

| Case | Usable avocado | Weekly profit | Method |
|---|---|---|---|
| 110% yield | 275,000 g | ≈ $39,243.39 (+$254.71) | Shadow-price estimate (within valid range) |
| 60% yield | 150,000 g | $38,700.00 (-$288.68) | Re-solved on the `60% Baseline` sheet |

## Workbook Guide

| Sheet | Contents |
|---|---|
| `Apex Fresh` | Baseline model, resource usage table, scenario net-gain calculations, yield what-ifs |
| `Scenario 1-4` | Solved solutions for each scenario |
| `60% Baseline` | Re-solved model at 60% avocado yield |
| `Answer Report 1` | Solver answer report (5 iterations) |
| `Sensitivity Report 1` | Solver sensitivity report |

## Reproduce

1. Open `Apex_Fresh_LP.xlsx` and enable the Solver add-in (File → Options → Add-ins).
2. Data → Solver. Set Objective `$J$5` to **Max**; variable cells `$D$4:$G$4`.
3. Constraints: `$H$8:$H$11 <= $J$8:$J$11`, `$H$12 >= $J$12`; tick "Make Unconstrained Variables Non-Negative".
4. Method: **Simplex LP**, then Solve.

## Limitations

- Kit counts are continuous, so results like 30.19 Keto kits would need rounding or an integer model for real production.
- Scenario costs are single figures with no time basis. Profit is weekly, so the net-gain comparison assumes the costs are weekly too.
- Scenario sheets hold pasted Solver outputs, not live formulas. Changing inputs requires re-running Solver.

**Author:** Faria Hassan, BBA (Finance), North South University
