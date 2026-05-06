# Sentence Memorability in Continuous Recognition
### An Analysis of Noun Memorability and Grammatical Voice
**Roll Numbers:** 2023101131 · 2023101014 · 2023101034

---

## About the Experiment

This project analyses data from a **continuous recognition memory experiment** involving 114 participants. Participants were shown subject-verb-object sentences and asked to recognise whether they had seen each sentence before.

Sentences were designed under three experimental conditions based on the memorability profile of their nouns:

| Condition | Subject Noun | Object Noun |
|-----------|-------------|-------------|
| **HVL** | High Verbal memorability | Low Non-verbal memorability |
| **LVH** | Low Verbal memorability | High Non-verbal memorability |
| **LVL** | Low Verbal memorability | Low Non-verbal memorability |

Each sentence appeared in both **active and passive voice**, creating a 3 × 2 design. The core theoretical motivation comes from **Paivio's dual-coding theory** — that concrete, imageable words are encoded both verbally and visually, giving them a memory advantage.

Two behavioural measures were recorded per trial:
- **Item Recognition (IR):** Did the participant recognise the sentence as previously seen?
- **Word Recognition (WR):** Did the participant recognise the exact wording, or detect a voice transformation?

The primary outcome measure is **CR_IR (Corrected Recognition)** = Hit Rate − False Alarm Rate, which controls for response bias.

---

## Repository Structure

```
.
├── eda.ipynb                        # Notebook 1: Preprocessing + EDA
├── Inferential_Statistics.ipynb     # Notebook 2: Inferential analyses (H1–H7)
└── README.md
```

The analysis is split across two notebooks that must be run **in order** — the EDA notebook produces the cleaned data files that the inferential notebook depends on.

---

## Notebook 1 — `eda.ipynb` (Preprocessing & Exploratory Data Analysis)

This notebook handles everything from raw log files to a clean, analysis-ready dataset. It is structured as 16 sequential steps:

| Step | What it does |
|------|-------------|
| **Step 1** | Loads and merges all 114 participant `.log` files into a single `raw_df` |
| **Step 2** | Parses stimulus IDs into `condition`, `item_num`, and `voice` columns |
| **Step 3** | Assigns block numbers (0 = practice, 1–3 = experimental) using `Rest Phase started` event boundaries; runs block validity checks |
| **Step 4** | Computes per-participant × condition × voice: IR hit rates, false alarm rates, and **CR_IR** = Hit Rate − FA Rate |
| **Step 5** | IQR-based outlier detection on CR_IR (1.5 × IQR rule); observations retained as non-parametric tests are used |
| **Step 6** | Cleans reaction times (removes impossible/extreme values); saves `cr_long_CLEAN.csv` and `summary_CR_wide_CLEAN.csv` |
| **Step 7** | Univariate distributions and descriptive statistics for all key variables: `cr_ir`, `hit_rate_ir`, `fa_rate`, `hit_rate_wr`, `mean_rt_ir_clean`, `mean_rt_wr_clean` |
| **Step 8** | Shapiro-Wilk normality tests and Q-Q plots per condition × voice cell — justifies use of non-parametric tests |
| **Step 9** | Condition-level analysis: boxplots, interaction plots (Memorability × Voice), violin plots across all outcome variables |
| **Step 10** | Participant-level quality checks: flags participants with extreme CR_IR, zero FA rates, or too few valid conditions |
| **Step 11** | Block effects and learning/fatigue check: hit rate trends across Blocks 1–3 |
| **Step 12** | Bivariate analysis: Spearman correlation heatmap, pairwise CR_IR scatterplots, CR_IR vs RT and FA scatterplots |
| **Step 13** | WR (Word Recognition) analysis: distributions, condition × voice comparisons, WR vs IR relationship |
| **Step 14** | False alarm rate deep dive: per-participant FA distributions and condition-level comparisons |
| **Step 15** | Preliminary Kruskal-Wallis tests for memorability condition effect on CR_IR |
| **Step 16** | Preliminary voice effect analysis: paired Wilcoxon tests within each memorability condition |

**Outputs produced by this notebook (used as inputs to Notebook 2):**
- `cr_long_CLEAN.csv` — long-format participant × condition × voice data with all metrics
- `summary_CR_wide_CLEAN.csv` — wide-format participant summary for Kruskal-Wallis style tests

---

## Notebook 2 — `Inferential_Statistics_1_.ipynb` (Hypothesis Testing)

This notebook takes the cleaned outputs from `eda.ipynb` and runs all formal inferential analyses. It tests **7 hypotheses** (with sub-hypotheses).

**Inputs required:** `cr_long_CLEAN.csv` and `summary_CR_wide_CLEAN.csv`

### Hypotheses and corresponding notebook sections:

| Hypothesis | Description | Test Used |
|-----------|-------------|-----------|
| **H1** | Effect of noun memorability condition on CR_IR | Friedman test + Bonferroni post-hoc Wilcoxon |
| **H1a** | Subject vs object noun position effects | Wilcoxon signed-rank |
| **H1b** | OLS regression: CR_IR ~ subject_high + object_high | OLS with full assumption diagnostics |
| **H2** | Effect of voice (active vs passive) on CR_IR | Paired t-test (difference scores were normal) |
| **H3** | Memorability × Voice interaction on CR_IR | Repeated-measures ANOVA with Greenhouse-Geisser correction |
| **H4** | Effect of memorability on WR hit rate | Friedman test; one-sample tests vs chance |
| **H4a** | Voice effect on WR accuracy | Wilcoxon signed-rank |
| **H4b** | Independence of CR_IR and WR | Spearman correlation |
| **H5** | Condition effect on false alarm rate | Friedman + Bonferroni post-hoc |
| **H5a** | Voice effect on false alarm rate | Wilcoxon signed-rank |
| **H6** | Block × Condition fatigue effect | Friedman ANOVA per condition across blocks 1–3 |
| **H7a** | Overall voice effect on reaction time (RT_IR) | Wilcoxon signed-rank |
| **H7b** | Voice effect on RT within each memorability condition | Wilcoxon + Bonferroni |
 
---

## How to Run

1. Place all participant `.log` files in the `DATA_DIR` path defined at the top of `eda.ipynb`
2. Run `eda.ipynb` from top to bottom — this generates the two CSV outputs
3. Confirm `cr_long_CLEAN.csv` and `summary_CR_wide_CLEAN.csv` exist in the expected path
4. Run `Inferential_Statistics_1_.ipynb` from top to bottom
