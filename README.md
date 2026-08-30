# S&P 500 Momentum Platform

A collection of quantitative equity strategies and a machine-learning forecaster
built over the S&P 500 universe. Author: **Abraham Sobowale**.

The strategy notebooks were built from the FreeCodeCamp *Algorithmic Trading in
Python* templates and reworked into my own implementations; the RandomForest
forecaster extends that foundation into supervised machine learning.

> **Status:** exploratory Jupyter notebooks. There is no packaged `src/` library
> and no automated test suite yet (see [Tests](#tests) and the TODOs below).

---

## What's in here

| Notebook | What it does |
|---|---|
| `notebooks/sp500-strategies/equal_weight_sp500_index_fund.ipynb` | Takes a portfolio value and computes how many shares of each S&P 500 constituent to buy for an **equal-weight** version of the index. Exports recommended trades to Excel. |
| `notebooks/sp500-strategies/quantitative_momentum_strategy.ipynb` | Selects the 50 highest-**momentum** S&P 500 stocks (using multi-horizon return scoring) and sizes an equal-weight portfolio of them. |
| `notebooks/sp500-strategies/quantitative_value_strategy.ipynb` | Ranks S&P 500 stocks on **value** metrics (P/E and a composite of valuation multiples) and builds an equal-weight portfolio of the cheapest names. |
| `notebooks/ml-forecaster/ML Stock Predictor.ipynb` | A **RandomForest** classifier that predicts stock direction from engineered features, evaluated with precision. |
| `notebooks/archive/` | Superseded / unrelated material kept for provenance: an earlier duplicate of the momentum notebook, an unrelated MATH2603 neural-network lab, and orphaned copies of the data files. Not part of the platform. |

## Data & API

- Market data is pulled from the **IEX Cloud API** using a **sandbox** token
  (randomly generated, free-to-call data — not suitable for real trading).
- The token lives in a local `sec.py` (`IEX_CLOUD_API_TOKEN = '...'`) which is
  **git-ignored** so it is never committed. A template is provided — copy it and
  paste your own token before running:

  ```bash
  cd notebooks/sp500-strategies
  cp sec.py.example sec.py   # then edit IEX_CLOUD_API_TOKEN in sec.py
  ```
- The S&P 500 constituent lists (`SP500.csv`, `sp_500_stocks.csv`) live alongside
  the notebooks that read them.

## Architecture

```
sp500-momentum-platform/
├── README.md
├── requirements.txt
├── .gitignore
├── pytest.ini
└── notebooks/
    ├── sp500-strategies/     # equal-weight, momentum, value strategies (+ sec.py, CSVs)
    ├── ml-forecaster/        # RandomForest direction classifier
    └── archive/              # superseded / unrelated notebooks kept for provenance
```

Each notebook is self-contained: it imports its constituent list from a CSV in
its own directory, calls the IEX Cloud API for pricing/fundamentals, computes the
strategy, and (for the strategy notebooks) writes recommended trades to an Excel
file via `xlsxwriter`.

## Results

_Figures below are taken directly from the saved notebook outputs._

**RandomForest forecaster** (`notebooks/ml-forecaster/ML Stock Predictor.ipynb`)

Predicts next-period price direction from OHLC, volume, `changePercent` and VWAP,
using `RandomForestClassifier(n_estimators=100, class_weight="balanced", min_samples_split=2, random_state=67)`.

| Evaluation | Precision |
|---|---|
| Single train/test split (train on first ~20%, test on the remainder) | **0.588** |
| Walk-forward backtest (expanding window, `start=2500`, `step=100`) | **0.417** |

The walk-forward backtest (~0.42) is the realistic out-of-sample figure — below
0.5, i.e. on this feature set the model does not reliably beat chance. That's the
honest takeaway; the single-split number flatters the model.

**Strategy screeners** — each produces a recommended portfolio with per-stock
share counts for a chosen portfolio value (not a backtested return series):

- **Momentum** — a 50-stock *high-quality momentum* (HQM) portfolio, scored across
  1-year / 6-month / 3-month / 1-month returns (example run sized to a $59,000 book).
- **Value** — a 50-stock *robust value* (RV) portfolio, scored on a composite of
  P/E, P/B, EV/EBITDA and EV/GP (example run sized to a $1,000,000 book).
- **Equal-weight index fund** — shares of every S&P 500 constituent needed for an
  equal-weight portfolio at a given value, exported to Excel.

> Note: market data comes from the IEX Cloud / FinancialModelingPrep sandbox
> (free) tiers, so specific tickers and prices in the saved outputs are
> illustrative rather than live.

## Running locally

Requires Python 3.10+ and Jupyter.

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r requirements.txt
```

Then create your `sec.py` (see [Data & API](#data--api)) and launch Jupyter:

```bash
jupyter lab   # or: jupyter notebook
```

Open any notebook under `notebooks/` and run the cells top to bottom.

## Tests

There are currently **no automated tests** — the project is exploratory
notebooks. `pytest.ini` is included so that tests placed under a future `tests/`
directory are discovered automatically. Running pytest today collects nothing:

```bash
pytest
```
