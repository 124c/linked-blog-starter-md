# Labor Market Data: Methodology & Formulas

**Two independent surveys, two independent measures.** Nonfarm Payrolls (NFP) and the Unemployment Rate come from *different surveys with different methodologies*. They can — and regularly do — move in opposite directions. This document treats each separately, then explains why they diverge.

---

## Part 0 — The Two Surveys (Why NFP ≠ Unemployment)

| Feature | Establishment Survey (CES) | Household Survey (CPS) |
|---|---|---|
| **Produces** | Nonfarm Payrolls (NFP) | Unemployment Rate, Labor Force, Gross Flows |
| **Respondent** | ~119,000 businesses & government agencies | ~60,000 households |
| **Counts** | Jobs (payroll slots) | People (by labor force status) |
| **Self-employed** | Excluded | Included |
| **Agricultural workers** | Excluded | Included |
| **Multiple jobholders** | Counted once per job (double-counts people) | Counted once per person |
| **Reference** | Pay period including the 12th of the month | Week including the 12th of the month |

**Core consequence:** NFP measures *filled payroll positions at businesses*; the unemployment rate measures *the share of people in the labor force without work who are looking*. A person can be missing from payrolls (self-employed, gig) yet employed in the household survey. This is the primary source of divergence.

---

# Part 1 — Nonfarm Payrolls (NFP)

## 1.1 Definition

NFP = the net change in the number of paid employees on nonfarm payrolls versus the prior month.

$$
\text{NFP}_t = E^{CES}_t - E^{CES}_{t-1}
$$

where $E^{CES}_t$ = total nonfarm payroll employment level in month $t$ (establishment survey).

The headline number is a **net change**, not a level. "+150k" means payroll employment rose by 150,000 from the previous month.

## 1.2 What's included / excluded

**Included:** all full- and part-time employees who received pay for any part of the reference pay period, across private sector and government.

**Excluded (the "non" in nonfarm and the survey scope):**
- Farm workers
- Self-employed / unincorporated
- Unpaid family workers
- Private household employees
- Military

## 1.3 Construction of the estimate

$$
E^{CES}_t = \underbrace{S_t}_{\text{sample-based estimate}} + \underbrace{B_t}_{\text{birth-death model}}
$$

- **Sample-based estimate $S_t$** — weighted, benchmarked count from responding establishments.
- **Birth-death model $B_t$** — a statistical adjustment estimating net jobs created by *new* businesses (births) minus jobs lost to *closures* (deaths) that the sample cannot yet observe. This is a common source of noise and later revision.

## 1.4 Revisions

Each monthly NFP print is revised **twice** in the following two months as more establishments report, then annually at benchmark.

$$
\text{Revision}_t = E^{CES}_{t,\text{final}} - E^{CES}_{t,\text{first print}}
$$

Annual benchmark revision aligns CES to the Quarterly Census of Employment and Wages (QCEW), a near-universe administrative count. The gap between the first print and the benchmark can be large — a key reason to treat single prints as noisy.

## 1.5 Related establishment-survey metrics

**Average weekly hours** — intensive margin. Payroll headcount can hold while hours fall (soft signal without job losses):

$$
\text{Total labor input} \approx E^{CES}_t \times \text{Avg weekly hours}_t
$$

**Average hourly earnings (AHE)** — wage pressure proxy:

$$
\text{AHE growth}_t = \frac{\text{AHE}_t - \text{AHE}_{t-1}}{\text{AHE}_{t-1}}
$$

**Diffusion index** — share of industries adding jobs; breadth check on whether gains are broad or concentrated.

---

# Part 2 — Unemployment Rate

## 2.1 Definitions

$$
\text{Unemployment Rate}_t = \frac{U_t}{LF_t}
$$

$$
LF_t = E^{CPS}_t + U_t \qquad\qquad \text{LFPR}_t = \frac{LF_t}{\text{Civilian noninstitutional population}_t}
$$

- $U_t$ — unemployed: not working, available, **actively searched** in prior 4 weeks
- $E^{CPS}_t$ — employed (household definition)
- $LF_t$ — labor force = employed + unemployed
- **LFPR** — labor force participation rate

**Critical property:** the rate has a numerator ($U$) *and* a denominator ($LF$). It can fall because $U$ falls **or** because $LF$ falls (people exit the labor force). These have opposite economic meanings.

## 2.2 The stock–flow identity

The stock of unemployed changes each month via **gross flows** between three states: **E** (employed), **U** (unemployed), **N** (not in labor force), plus population entry/exit.

$$
\Delta U_t = \underbrace{(EU_t + NU_t + \text{UI\_in}_t)}_{\text{inflows to }U\;(+)} - \underbrace{(UE_t + UN_t + U\_out_t)}_{\text{outflows from }U\;(-)}
$$

### Flow definitions

| Flow | Symbol | Direction | Meaning |
|---|---|---|---|
| Employed → Unemployed | $EU$ | inflow (+) | Job loss, layoffs, quits into search |
| Not in LF → Unemployed | $NU$ | inflow (+) | **Reentrants** — prior work history, resume searching |
| Marginal Inflows to U | $\text{UI\_in}$ | inflow (+) | **New entrants** (first-time, aging-in 16yo) + **immigrants** entering population directly as unemployed |
| Unemployed → Employed | $UE$ | outflow (−) | Job finding |
| Unemployed → Not in LF | $UN$ | outflow (−) | **Withdrawal** — stop searching (discouragement, retirement, school, caregiving, health) |
| Marginal Outflows from U | $U\_out$ | outflow (−) | Deaths, emigration, institutionalization of the unemployed (~2k/mo) |

### Reentrants vs New entrants (precise distinction)

- **Reentrants** → captured in **$NU$**. Previously worked, left the labor force, now returning to search. Existed in the NILF stock last month.
- **New entrants + immigrants** → captured in **$\text{UI\_in}$**. Never in the labor force before (first job search) or newly arrived in the population. Were *not* in the measured population last month.

## 2.3 Sign convention (as implemented)

All **outflows stored negative**, all **inflows stored positive**, so:

$$
\Delta U_t = \sum(\text{signed flows}) = EU_t + NU_t + \text{UI\_in}_t + UE_t + UN_t + U\_out_t
$$

(where $UE, UN, U\_out$ already carry a minus sign). A single `SUM()` returns the net change.

## 2.4 Decomposing the *rate* change (percentage points)

The change in the unemployment rate decomposes into a contribution from each flow plus a **labor-force-growth correction** (because the denominator also moves).

**Contribution of flow $X$ (in pp):**

$$
\text{contrib}_X = \frac{X_t}{LF_{t-1}}
$$

Using $LF_{t-1}$ (prior-month labor force) as denominator is the correct timing: the flow during month $t$ is scaled by the labor force it acted upon.

**Labor-force growth correction:**

$$
\text{LF corr}_t = -\,u_{t-1} \times \frac{\Delta LF_t}{LF_{t-1}}
\qquad\text{where } u_{t-1} = \frac{U_{t-1}}{LF_{t-1}}
$$

This term captures the mechanical effect on the *rate* of the denominator growing even if $U$ is unchanged.

**Full identity (reconciles to official rate change):**

$$
\Delta\!\left(\text{U rate}\right)_t \;=\; \underbrace{\frac{EU_t + NU_t + \text{UI\_in}_t - UE_t - UN_t - U\_out_t}{LF_{t-1}}}_{\text{signed flow contributions}} \;+\; \text{LF corr}_t \;+\; \varepsilon_t
$$

$\varepsilon_t$ is a small residual from published-rate rounding (rate reported to 0.1%) and CPS rotation-group adjustments — typically < 0.05 pp when all flows are included.

## 2.5 Cross-check against the stock

$$
\Delta U^{\text{true}}_t = U^{\text{level}}_t - U^{\text{level}}_{t-1}
\qquad\qquad
\text{Residual}_t = \Delta U^{\text{true}}_t - \Delta U^{\text{model}}_t
$$

With all six flows present (including $\text{UI\_in}$), $\text{Residual} \approx 0$ (rounding only). A large residual signals a missing flow series.

## 2.6 Decomposing the UN withdrawal flow (by reason)

$UN$ is a single published number; **BLS does not publish it split by reason**. To interpret *why* people withdrew, cross-reference stocks from **CPS Table A-16** (not in labor force by reason):

| Bucket | Reasons | Rising signals |
|---|---|---|
| **Involuntary / distress** | Discouraged workers, health/disability | Weak labor demand |
| **Voluntary / structural** | Retirement, return to school, caregiving, spousal income | Life-cycle/supply-side, *not* demand weakness |

Interpretive rule: a falling unemployment rate driven by the **voluntary** bucket overstates labor-market strength (rate falls for benign reasons, not hiring); driven by the **involuntary** bucket, it *masks* weakness (people giving up). A true flow-by-reason decomposition requires **IPUMS-CPS microdata** (link consecutive months on `CPSIDP`, read `WHYNWLY`).

---

# Part 3 — Why NFP and Unemployment Diverge

Common scenarios where the two surveys tell different stories:

1. **Denominator effect** — LFPR falls; unemployment rate drops even with weak hiring, because exits leave the labor force ($UN$ up), not because jobs were created ($UE$ flat/down). *This is the classic "weak payrolls + falling unemployment" puzzle.*

2. **Survey scope** — payrolls miss self-employed and gig workers; a rise in self-employment lifts household employment but not NFP.

3. **Hours vs headcount** — firms cut hours, not staff: NFP holds, but labor input weakens.

4. **Birth-death noise** — the CES model over/understates new-business jobs; not replicated in CPS.

5. **Sampling error** — two different samples, two different error bands. Month-to-month, sampling noise alone can push them apart.

**Reading rule:** trends over 3–6 months matter more than any single print; when the surveys conflict, decompose the unemployment change via gross flows (Part 2.4) to see *why* — that reveals whether a rate move is healthy (driven by $UE$/$EU$) or mechanical (driven by $UN$/$LF$).

---

# Appendix — Series Reference (BLS)

| Series ID | Content | Role |
|---|---|---|
| CES0000000001 | Total nonfarm payroll employment | NFP level → $\Delta$ = headline |
| LNS12000000 | Employment level (household) | $E^{CPS}$ |
| LNS13000000 | Unemployment level | $U$ — stock cross-check |
| LNS14000000 | Unemployment rate | Official rate |
| LNS11000000 | Civilian labor force level | $LF$ denominator |
| LNS11300000 | Labor force participation rate | LFPR |
| LNS17100000 | Unemployed → Employed | $UE$ (outflow −) |
| LNS17400000 | Employed → Unemployed | $EU$ (inflow +) |
| LNS17600000 | Not in LF → Unemployed | $NU$ (inflow +, reentrants) |
| LNS17700000 | Marginal inflows to Unemployed | $\text{UI\_in}$ (inflow +, new entrants/immigrants) |
| LNS17900000 | Unemployed → Not in LF | $UN$ (outflow −, withdrawal) |
| LNS18300000 | Marginal outflows, unemployed | $U\_out$ (outflow −) |
| Table A-16 | Not in LF by reason | UN reason interpretation |

*All gross-flow series are seasonally adjusted, 16 years and over, in thousands of persons. NFP is seasonally adjusted, in thousands.*
