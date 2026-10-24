# 1031 DST Portfolio Calculator

An interactive, single-file calculator that shows how a 1031 exchange into one or more Delaware Statutory Trusts (DSTs) can play out. Mix property categories, set loan-to-value, yield and appreciation, and see income, after-tax cash flow, appreciation, debt paydown and boot side by side. Part of [Post Millennium Renaissance](https://www.postmillenniumrenaissance.com/).

It is an educational illustration of how DST return mechanics work. It is not investment, tax or legal advice, and it does not describe any specific offering.

## What this tool does

- Splits a pool of exchange proceeds across any number of DSTs, each in a property category (multifamily, industrial, office, self-storage, senior living, student housing, medical office, retail, zero coupon).
- Projects each DST's distributions, appreciation and (for zero coupon) loan paydown over an assumed holding period, then blends them into a portfolio.
- Flags boot: unallocated cash and debt-relief boot when replacement debt falls short of the debt on the property sold, with an estimated tax.
- Estimates the tax an outright sale would have triggered, so the deferral is visible.
- Optionally simulates holding-period uncertainty with a Monte Carlo run.
- Stores every input in the page link, so a scenario can be shared exactly.

## Opening / hosting it

Double-click `index.html`. There is no build step, no install and no external dependency. On the PMR site it runs inside an iframe.

## Interface

**Exchange proceeds**
- *Total 1031 proceeds*: the amount being placed into DSTs.
- *Debt on relinquished property*: drives the debt-relief boot check.
- *Cap. gains rate*: applied to any boot.
- *Advanced: depreciation recapture*: splits boot into recapture (taxed first, at the recapture rate) and capital gain.

**Each DST card**
- *Allocated amount*: dollars committed to this DST.
- *Cash-on-cash (sets all years)*: one number that fills the whole yield schedule. Locked at 0% for zero coupon DSTs.
- *Loan-to-value (LTV)*: debt as a share of the property's value, the way offering documents quote it. Most categories allow 0–70%; zero coupon allows 60–90%.
- *Loan interest rate* and *Amortization term* (zero coupon only): the amortizing loan the rent pays down.
- *Year-by-year yield & tax shelter*: a collapsible ten-row table. For each year 1–10, set the yield (% of net equity) and the share of that year's distribution that is tax-sheltered (%). Rows beyond the holding period are dimmed but still used by the Monte Carlo.
- *Appreciation rate*: annual, compounded on the full property value.
- *Assumed holding period*: 4–10 years, shown against an illustrative bell curve.

**Projected results**
- *Tax rate on distributions*: applied to the part of each year's distribution that is not sheltered. It feeds only the after-tax cash flow figure.

**Fiduciary**
- Off: an upfront fee and a placement fee are taken off the top before capital is invested. On: those two fees are removed. See "What this tool does not do" for the limits of that switch.

## Theory

### The return model

Leverage is loan-to-value. A DST with net equity *E* at LTV *L* controls a property worth *V = E / (1 − L)* carrying debt *D = V − E*. At sale the investor receives the sale price minus the remaining loan balance, which splits into:

- **Cash flow income**: the sum over the hold of equity × that year's yield. A part-year (for example the last half of a 6.5-year hold) counts pro rata.
- **Appreciation gain**: *V × ((1 + g)^years − 1)*, so leverage amplifies appreciation in both directions.
- **Debt paydown**: *D − remaining balance*. It is zero for interest-only debt (the usual DST case). For zero coupon DSTs it uses the standard level-payment monthly amortization formula.

Profit and annualized return are measured against the gross amount committed, so fees act as a drag on return.

### Tax

- **Boot**: unallocated proceeds plus any shortfall of replacement debt against relinquished debt, taxed at the capital gains rate (or the recapture-then-capital-gains split).
- **After-tax cash flow**: total distributions minus (distributions − sheltered portion) × the tax rate on distributions.
- **"If you'd sold instead"**: the same rates applied to proceeds minus cost basis. It stays hidden until a cost basis is entered, so the tool never assumes a $0 basis.

### Model assumptions and their implications

- Non-zero-coupon debt is interest-only and stays flat until sale.
- Zero coupon DSTs pay no distributions. The rent is assumed to exactly cover debt service, so the return is appreciation plus principal paydown.
- The blended annualized return is the CAGR of the portfolio's ending multiple over the dollar-weighted hold. It is not an IRR, so it ignores when each distribution arrives.
- No disposition costs are deducted at sale.

## What each metric means

- **Net equity invested**: allocated dollars minus fees.
- **Cash flow income (year 1)**: year-1 distributions per year and per month.
- **After-tax cash flow**: distributions over the whole hold after tax on the unsheltered part, with the sheltered dollars shown underneath.
- **Appreciation gain**: the investor's share of the property's value growth.
- **Debt paydown**: principal repaid by exit (shown only when a zero coupon DST is in the portfolio).
- **Total profit**: everything returned minus the gross amount committed.
- **Projected sale value**: total cash returned over the hold, including distributions.
- **Blended annualized return**: see the assumptions above.

## Data source and confidence

All default figures are **illustrative placeholders**: cash-on-cash, LTV, appreciation, the zero coupon loan rate (6.0%) and term (20 years). They are plausible in magnitude but are not researched market data, not fitted to any index, and not a forecast. Real offerings vary widely.

- The category LTV defaults were converted one-for-one from an earlier debt-on-equity input (for example, 45% debt-on-equity became 31% LTV) so results didn't jump when the input changed. They read low next to typical offerings.
- The tax-sheltered share defaults to 0% on purpose. The real figure comes from an offering's own tax projections, and a typical-looking default would be the tool inventing a flattering input.
- Statutory defaults: a 20% capital gains rate, a 25% unrecaptured §1250 rate and a 37% top ordinary rate, all federal.

## Validation

The model was checked in a headless browser against hand calculations:

- $1M of equity at 75% LTV, 6% over 20 years, held 6.5 years: a $4M property, $3M of debt and $617.5K of principal repaid.
- A 5%/6% yield schedule over 6.5 years: $330K of distributions, of which $255K is sheltered at 80%/50%.
- Shared links round-trip exactly, and pre-LTV links still load and convert.

That shows the code computes what this README describes. It does not show the model is right about any real DST.

## Known structural quirks

- Links made before the LTV change carry the DST list under `d` with debt-on-equity leverage. They are converted on load (LTV = DE / (1 + DE)). An old zero coupon record lands below today's 60% LTV floor and is clamped up to it. New links use `l`; the `d` key must never be reused.
- Yield and sheltered schedules ride in the `ys` and `sh` keys. A record is left empty when it holds the default.

## What this tool does not do

- It does not model state or local tax, the 3.8% NIIT, passive-activity or at-risk limits, or the tax on zero coupon phantom income.
- It does not model the basis reduction from sheltered distributions, which raises the gain taxed at sale.
- It does not deduct disposition costs or sponsor promotes, or compute an IRR.
- It does not model the sponsor-level fees (acquisition, financing, asset management, disposition) or advisory fees that apply even with the Fiduciary switch on.
- It does not include data on any specific sponsor, program or offering.

## Files in this repository

- `index.html`: the whole tool (markup, styles, script and data).
- `README.md`: this file.

## Working with Claude on this file

Start a new chat with the current `index.html` and ask for the change, following the PMR build standard. Two rules apply:

- To add a property category, add one line to `CATEGORY_DEFAULTS` and one `--cat-*` CSS color.
- To add a portfolio-wide input, add one line to `SCALAR_FIELDS`.

Never change a category `code` or a URL key once shipped, because that breaks every shared link. Any new default figure must be labeled illustrative or cited.

## License and attribution

Built by Post Millennium Renaissance. No license file has been added to this repository yet.

## Contact and corrections

Corrections are welcome through [postmillenniumrenaissance.com](https://www.postmillenniumrenaissance.com/) or a GitHub issue on this repository.
