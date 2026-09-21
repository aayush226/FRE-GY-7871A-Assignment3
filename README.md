# FRE-GY 7871A — Assignment 3: Iran War Risk via Heteroskedasticity Identification + NLP

Estimates the sensitivity of U.S. financial variables to **Iran war risk since Feb 28, 2026**, combining:

1. **NLP news classification** — daily binary (and optional 3-way) flags for Iran-war-risk news intensity.
2. **Heteroskedasticity-based identification** (Rigobon, 2003, *"Identification through Heteroskedasticity"*, ReStat) — the theoretical identification strategy.
3. **The IV estimator applied in Rigobon & Sack (2003)**, *"The Effects of War Risk on U.S. Financial Markets"* (NBER WP 9609) — the empirical template we replicate, substituting Iran (2026) for Iraq (2003).

## What this replicates

The notebook reproduces the three core outputs of the Rigobon & Sack paper, updated for the Iran war-risk episode:

| Paper | Our output |
|---|---|
| Table 1 — dates of high war-risk news | `outputs/table1_high_news_dates.csv` |
| Table 2 — estimated coefficients (d21) per financial variable, 3 IV estimators, t-stats | `outputs/table2_sensitivity_estimates.csv` |
| Table 3 — variance decomposition (% of variance explained by war risk) | `outputs/table3_variance_decomposition.csv` |

It also includes:
- A **rank-condition diagnostic** (Rigobon 2003, Proposition 1) checking that the identifying assumption actually holds for each variable pair.
- A markdown section discussing **whether heteroskedasticity-based identification is the right method** for this problem, with alternatives (high-frequency identification, sign-restricted VAR, local projections, proxy-SVAR).
- Optional cells for the assignment's suggested extensions: a 3-regime split (good/bad/no news) and handling of novel/emerging terminology not covered by a fixed keyword dictionary.

## Repo structure

```
.
├── iran_war_risk_analysis.ipynb   # main notebook — run top to bottom
├── README.md
├── requirements.txt
├── .env.example                   # template for your Guardian API key — copy to .env
├── .gitignore
├── cache/                         # cached Guardian API results (safe to commit — no secrets, speeds up reruns)
└── outputs/                       # created by the notebook: CSVs of the 3 tables + merged data panel
```

## Setup

```bash
pip install -r requirements.txt
```

Or, inside the notebook itself, uncomment the `%pip install` line in the first code cell (handy for Colab).

### API key (Guardian Open Platform)

News data comes from [The Guardian Open Platform API](https://open-platform.theguardian.com/access/) (free, but requires a key — GDELT's DOC 2.0 API was tried first but has aggressive rate-limiting on its free tier that made it unreliable for this pipeline).

1. Get a free key at the link above (instant, no approval wait).
2. Copy `.env.example` to `.env` in the repo root:
   ```bash
   cp .env.example .env
   ```
3. Edit `.env` and paste your key:
   ```
   GUARDIAN_API_KEY=your_actual_key_here
   ```
4. **`.env` is already in `.gitignore`** — it will never be committed. Do not paste your key directly into the notebook or into `.env.example`.

If you're running in Colab (no local `.env` file), the notebook will prompt you to paste the key via a hidden input box instead — nothing gets written to disk there either.

## Running it

1. Open `iran_war_risk_analysis.ipynb` in Jupyter, VS Code, or **Google Colab**.
2. Run cells top to bottom. Section 1 (NLP) pulls news volume from The Guardian API; Section 2 pulls market data from FRED (via `pandas_datareader`, no key needed) and Yahoo Finance (via `yfinance`).
3. **Caching:** the Guardian fetch is cached to `cache/news_volume_<start>_<end>.csv` after the first successful run — re-running the notebook loads from that file instead of re-hitting the API (saves ~2 min and your daily request quota). Delete the file, or set `FORCE_REFETCH = True` in that cell, to pull fresh data.
3. The optional cells (TF-IDF novelty scan, zero-shot transformer classification, VADER sentiment 3-regime split) are **off by default** — flip the `RUN_*` flags to `True` and supply a `headlines_df` (date, headline) if you want to use them.
4. Fill in the two markdown cells that need manual input: the `event` column of Table 1 (a one-line description per flagged date) and the benchmark-comparison table in Section 4 (comparing your 2026 results to the 2003 Iraq numbers).
5. The last cell saves all three tables plus the merged daily data panel to `outputs/`.

## Do you need a GPU?

**No, not for the default pipeline.** GDELT news-volume pulls, FRED/Yahoo Finance data, and the heteroskedasticity IV estimator are all lightweight and run fine on CPU, including in a plain (non-GPU) Colab runtime.

The **one optional exception** is the zero-shot transformer classification cell (Section 1d, `RUN_ZERO_SHOT`), used only if you want a more robust way to catch novel Iran-war terminology than keyword matching. If you enable it on a large volume of headlines, running it on a Colab GPU runtime (T4 is enough) will be noticeably faster than CPU. It's off by default, so you can ignore this entirely unless you choose to use it.

## Data sources used

- **News volume:** [The Guardian Open Platform API](https://open-platform.theguardian.com/) — free, requires a key (see setup above). 5,000 requests/day, 12/sec.
- **Rates / spreads / dollar index:** [FRED](https://fred.stlouisfed.org/) via `pandas_datareader` (`DGS2`, `DGS10`, `T10YIE`, `BAMLC0A4CBBB`, `BAMLH0A0HYM2`, `DTWEXBGS`) — free, no key needed.
- **Equities / oil / gold:** [Yahoo Finance](https://finance.yahoo.com/) via `yfinance` (`^GSPC`, `CL=F`, `GC=F`) — free, no key needed.

**Known data-availability gap:** the original paper's on-the-run/off-the-run Treasury liquidity-premium series isn't available from free sources; this is called out in the notebook as a caveat rather than silently omitted.

## Key references

- Rigobon, R. (2003). "Identification through Heteroskedasticity." *The Review of Economics and Statistics*, 85(4), 777–792.
- Rigobon, R., & Sack, B. (2003). "The Effects of War Risk on U.S. Financial Markets." NBER Working Paper No. 9609.
