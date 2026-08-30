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
  **git-ignored** so it is never committed. Create your own before running:

  ```python
  # sec.py
  IEX_CLOUD_API_TOKEN = 'your_sandbox_token_here'
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

<!-- TODO: fill in real numbers from your own runs. I have not run the strategies
     or the model end-to-end, so I have not invented any performance figures. -->

- Momentum / value / equal-weight strategies: **TODO — add the portfolio output
  (e.g. number of positions, example recommended-trades table, or any backtest
  return you have).**
- RandomForest forecaster: **TODO — add the precision score (and any other
  metrics) you obtained on your test set.**

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
