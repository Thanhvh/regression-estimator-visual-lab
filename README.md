# Regression Estimator Visual Lab

Interactive, browser-based teaching tools for **financial econometrics**. Each lesson is a **single, self-contained HTML file** that runs fully offline — no external libraries, no CDN, no network calls, no build step. Open the file and it works.

These modules are designed for graduate students, PhD candidates, and faculty who already know basic OLS regression and want to *see* — not just read — what the core properties of an estimator actually mean. They are companions to the panel-data econometrics textbook series described under [Author & citation](#author--citation).

> **Live demo:** https://thanhvh.github.io/regression-estimator-visual-lab/
> (Or clone the repository and double-click any `.html` file — see [How to open](#how-to-open).)

---

## What you will learn

The three lessons share one backbone. Lesson 1 establishes the central idea — that an OLS estimate `b₁` is a **random variable** with a *sampling distribution*. The next two lessons examine the properties we want that distribution to have:

| Property | What it describes | Regime | Lesson |
|---|---|---|---|
| **Unbiasedness** | Is the distribution *centred* on the truth `β₁`? | Finite sample | 1 |
| **Efficiency** | How *tight* is the distribution? (among unbiased estimators) | Finite sample | 2 |
| **Consistency** | Does the distribution *collapse* onto `β₁` as `n → ∞`? | Large sample | 3 |

The unifying thread is the decomposition **`MSE = Var + Bias²`**: Lesson 2 shows it at a fixed sample size; Lesson 3 shows what happens to it as the sample grows (`MSE → 0` for a consistent estimator). Finite-sample properties (Lessons 1–2) and the asymptotic property (Lesson 3) are kept conceptually distinct on purpose.

**Suggested order:** 1 → 2 → 3.

**Prerequisites:** basic OLS regression, and introductory probability/statistics (random variables, expectation, variance, the idea of a sampling distribution).

---

## Lessons

### 1 · Sampling Distribution & Unbiasedness of OLS
File: [`01_sampling_distribution_unbiasedness.html`](01_sampling_distribution_unbiasedness.html) · Spec: [`specs/01_sampling_distribution_unbiasedness.md`](specs/01_sampling_distribution_unbiasedness.md)

*Guiding question: if OLS gives a different answer for every sample, in what sense is it "right"?*

The learner discovers that `β₁` is a fixed, unobservable truth of the population, that OLS is a **rule** producing a different **estimate** `b₁` for every sample (so `b₁` is a random variable), and that averaging `b₁` over many samples converges to `β₁` — the intuition of **unbiasedness**, `E(b₁) = β₁`. Flipping the *Endogeneity* switch shows how an exogeneity violation makes `b₁` systematically biased — and how that bias **does not self-correct** as the number of samples grows.

Three regions: **(A)** population / data-generating process, **(B)** samples (the OLS rule → estimate `b₁`), **(C)** sampling distribution (histogram of the `b₁` values).

### 2 · Efficiency, the Bias–Variance Grid, and MSE
File: [`02_efficiency_mse.html`](02_efficiency_mse.html) · Spec: [`specs/02_efficiency_mse.md`](specs/02_efficiency_mse.md)

*Guiding question: between an unbiased-but-wide estimator and a slightly-biased-but-tight one, which is "better"?*

Two estimators are compared on the same data: **full-sample OLS** (the BLUE benchmark) and a second estimator with **two independent knobs** — a bias offset `δ` (shifts the centre) and a subsample size `m` (controls the spread). Because the knobs are independent, the estimator can be placed in any cell of a **2×2 bias × precision grid**. A live **MSE meter** decomposes `MSE = Var + Bias²`, and a **relative-efficiency** readout quantifies how much precision is lost. A separate **shrinkage** panel (`b̃₁ = c · b₁`) demonstrates the bias–variance trade-off — that a little bias can *lower* MSE — with an honest "oracle" caveat connecting it to ridge / James–Stein estimation.

Note on terminology: *efficiency* in the Gauss–Markov sense is defined **among unbiased estimators**; for biased estimators the module compares variance or MSE instead.

### 3 · Consistency
File: [`03_consistency.html`](03_consistency.html) · Spec: [`specs/03_consistency.md`](specs/03_consistency.md)

*Guiding question: if I keep collecting data forever, is my estimator guaranteed to reach the truth — and what does "reach" mean?*

A sample-size slider `n` (log scale, 10 → 10,000) drives a **collapsing histogram** (overlaid with the analytic Normal density) across four estimator cases that together fill a complete **{finite-sample bias} × {consistency}** grid:

- **A — OLS, exogenous:** unbiased **and** consistent (collapses onto `β₁`).
- **B — OLS, endogenous:** biased **and** inconsistent — collapses onto the *wrong* value `β₁ + ρσ_ε/σ_x`. *More data does not fix endogeneity.*
- **C — OLS + c/n:** biased at finite `n` but **consistent** — the bias vanishes as `n` grows (illustrative of small-sample IV/MLE bias, dynamic-panel bias ~1/T).
- **D — first k observations only:** unbiased but **inconsistent** — the spread never shrinks.

Cases C and D break the common confusion that "consistent" and "unbiased" are the same thing, in *both* directions. Two further panels deepen the picture: a **convergence funnel** (the classic `b₁`-vs-`n` view, the signature image of consistency) and an **`MSE(n)` curve** that ties back to Lesson 2. An honest note explains that consistency says nothing about your *finite* sample, and that this is why IV, GMM, and dynamic-panel estimators are justified by consistency (via `plim` / Slutsky) rather than unbiasedness.

---

## How to open

**No installation required.** **Double-click** any `.html` file and it opens in your default browser (Chrome, Edge, Firefox, Safari), running immediately over `file://`. You can also drag-and-drop the file onto a browser window, or right-click → *Open with*. To browse all lessons from one place, open [`index.html`](index.html) or visit the [live demo](https://thanhvh.github.io/regression-estimator-visual-lab/).

---

## How to use a lesson (for learners)

Each module is built for *active* exploration, not passive reading:

- **Draw and watch.** Use "Draw one sample" / "Draw 500" (Lessons 1–2) or the **`n` slider** (Lesson 3) and watch the sampling distribution build up, tighten, shift, or collapse.
- **Turn one knob at a time.** Change `δ` *or* `m` *or* `n` *or* `ρ` in isolation to see exactly which feature of the distribution each one controls — bias moves the centre, sample size moves the spread.
- **Read the verdict.** The bias/precision and bias/consistency grids name where the current estimator sits; the on-screen messages explain what you are seeing.
- **Trust but verify.** Press **"Run verification"** in any lesson: it runs a large Monte-Carlo experiment (10,000–20,000 replications) *inside the page* and checks the empirical mean, SD, and MSE against the closed-form theory. Every number you see is reproducible and self-checked.

**Scope & honesty.** These are deliberately small "toy" models that isolate one idea at a time. The bias devices `δ` (Lesson 2) and `c/n` (Lesson 3) are *illustrative*: real bias in applied work comes from structural sources (omitted variables, endogeneity, dynamic panels). Consistency is an asymptotic statement and guarantees nothing at the sample size you actually have.

---

## Notation (locked — following Greene / Stock–Watson)

| Concept | Symbol |
|---|---|
| Population parameter | `β₀, β₁` |
| Population error | `ε` |
| Estimate | `b₀, b₁` |
| Sample residual | `e` |

Do **not** use `u` for the error; do **not** swap `ε` and `e`.

---

## Technical notes

- **Reproducible randomness.** All random numbers come from a seeded PRNG (mulberry32) — same seed + same parameters ⇒ same result. `Math.random()` is never used.
- **Built-in verification.** Each lesson ships a Monte-Carlo verification panel that compares empirical vs theoretical values against a relative-deviation tolerance, so the displayed formulas are validated in-app.
- **Display.** Numbers use 3 decimal places with a dot separator (e.g. `0.035`); the user interface is in English.
- **Self-contained.** Each file inlines its own CSS and JavaScript and draws with plain SVG/Canvas — no libraries, no CDN, no network. The shared engine (`RNG → DGP → OLS → SAMPLING → CHARTS → MSE → UI`) is duplicated per file by design, so each lesson remains a single portable artifact.
- **Specifications & decisions.** Every lesson has a full specification in [`specs/`](specs/); design decisions are logged in [`notes/decisions.md`](notes/decisions.md).

---

## Repository structure

```
regression-estimator-visual-lab/
├── index.html                                   # landing page linking the lessons
├── 01_sampling_distribution_unbiasedness.html
├── 02_efficiency_mse.html
├── 03_consistency.html
├── specs/                                        # full specification per lesson
│   ├── 01_sampling_distribution_unbiasedness.md
│   ├── 02_efficiency_mse.md
│   └── 03_consistency.md
├── notes/
│   └── decisions.md                              # decision log
├── CLAUDE.md                                     # build conventions
└── README.md
```

---

## Author & citation

**Author:** Vu Huu Thanh
**Affiliation:** Faculty of Finance and Banking, Ho Chi Minh City Open University, Vietnam
**Contact:** thanh.vh@ou.edu.vn (institutional) · thanh.vh@powerbi.vn · [ORCID 0000-0003-2306-2565](https://orcid.org/0000-0003-2306-2565)
**GitHub:** [@Thanhvh](https://github.com/Thanhvh)

Part of a panel-data econometrics textbook series for finance and economics.

**How to cite:**

> Vu, H. T. (2026). *Regression Estimator Visual Lab* [interactive teaching software]. Ho Chi Minh City Open University. https://thanhvh.github.io/regression-estimator-visual-lab/

---

## License

Released under [**CC-BY-4.0**](https://creativecommons.org/licenses/by/4.0/). You are free to use and adapt these materials for teaching and research, provided appropriate credit is given.

> To make this effective, add a `LICENSE` file at the repository root (on GitHub: *Add file → Create new file → type `LICENSE`*, then choose the template). Change this line if you prefer a different license.

---

## Feedback

Questions, corrections, and suggestions are welcome via [GitHub Issues](https://github.com/Thanhvh/regression-estimator-visual-lab/issues) or the contact above.
