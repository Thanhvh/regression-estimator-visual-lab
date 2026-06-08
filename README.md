# Regression Estimator Visual Lab

Visual teaching tools for **financial econometrics**. Each lesson is a **single,
self-contained HTML file** that runs fully offline — no external libraries, no
CDN, no network calls.

## Lessons

### 1. Sampling Distribution & Unbiasedness of OLS
File: [`01_sampling_distribution_unbiasedness.html`](01_sampling_distribution_unbiasedness.html)

The learner discovers that `β₁` is a fixed, unobservable truth of the population,
that OLS is a **rule** producing a different **estimate** `b₁` for every sample
(so `b₁` is a random variable), and that repeating many samples makes the mean of
`b₁` converge to `β₁` — the intuition of **unbiasedness** `E(b₁)=β₁`. Flip the
*Endogeneity* switch to see how an exogeneity violation makes `b₁` systematically
biased and **does not self-correct** as the number of samples grows.

The screen has three regions: **(A) Population / data-generating process**,
**(B) Samples** (the OLS rule → estimate `b₁`), and **(C) Sampling distribution**
(histogram of the `b₁` values). A built-in **verification panel (10,000 reps)**
compares empirical vs theoretical values inside the app.

## How to open

**No installation required.** Just **double-click** the `.html` file — it opens in
your default browser (Chrome, Edge, Firefox, Safari) and runs immediately over
`file://`.

> Tip: you can also drag-and-drop the file onto a browser window, or right-click →
> *Open with*.

## Notation (locked — following Greene / Stock–Watson)

| Concept | Symbol |
|---|---|
| Population parameter | `β₀, β₁` |
| Population error | `ε` (blue) |
| Estimate | `b₀, b₁` |
| Sample residual | `e` (orange) |

Do NOT use `u` for the error; do NOT swap `ε` and `e`.

## Technical notes

- Random numbers come from a seeded PRNG (mulberry32), so results are
  **reproducible** (same seed + same parameters ⇒ same result).
- Displayed numbers: 3 decimal places, dot separator (e.g. `0.035`).
- The code inside the file is split into reusable blocks: `TERMINOLOGY → RNG →
  DGP → OLS → SAMPLING → CHARTS → UI`.
- Full specification: [`specs/01_sampling_distribution_unbiasedness.md`](specs/01_sampling_distribution_unbiasedness.md).
- Decision log: [`notes/decisions.md`](notes/decisions.md).
