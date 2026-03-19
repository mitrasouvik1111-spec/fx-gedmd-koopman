# FX-GEDMD: Structure and Predictability in Foreign Exchange Markets

> **A Koopman-Operator and Graph-Embedded Dynamic Mode Decomposition Framework**

![Python](https://img.shields.io/badge/Python-3.9%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=for-the-badge&logo=jupyter&logoColor=white)
![License](https://img.shields.io/badge/License-Academic%20Use-green?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-MSc%20Dissertation-blueviolet?style=for-the-badge)
![University](https://img.shields.io/badge/University%20of%20Surrey-2025-darkblue?style=for-the-badge)

**Author:** Souvik Mitra  
**Supervisor:** Dr Dorje Brody — School of Mathematics and Physics, University of Surrey  
**Contact:** sm04251@surrey.ac.uk

---

## What This Project Does

This repository contains the full reproducible codebase for an MSc dissertation that applies **Graph-Embedded Dynamic Mode Decomposition (GEDMD)** — a Koopman-operator method — to forecast major USD currency pairs. It is the first comprehensive application of this framework to foreign exchange markets.

The model learns local linear operators from delay-embedded FX price trajectories, uses correlation-confidence neighbourhood graphs to incorporate cross-asset structure, and applies scaled Tikhonov regularisation to remain stable under heavy-tailed FX dynamics. The optimised pipeline achieves a **Sharpe ratio of ≈ 1.03** and **cumulative returns of ≈ 62.96%** over the out-of-sample period.

---

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/fx-gedmd-koopman.git
cd fx-gedmd-koopman
```

### 2. Create a Virtual Environment (Recommended)

```bash
# Mac/Linux
python -m venv venv
source venv/bin/activate

# Windows
python -m venv venv
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Set Up Your OANDA API Token

The data pipeline fetches daily FX prices from the **OANDA v20 REST API**. You need a free personal account at [oanda.com](https://www.oanda.com).

**Never hardcode your API token.** Set it as an environment variable:

```bash
# Mac/Linux
export OANDA_TOKEN="your-token-here"

# Windows (Command Prompt)
set OANDA_TOKEN=your-token-here

# Windows (PowerShell)
$env:OANDA_TOKEN="your-token-here"
```

The notebook reads it automatically:
```python
import os
TOKEN = os.environ.get("OANDA_TOKEN")
```

### 5. Launch the Notebook

```bash
jupyter notebook GEDMD_FX_Notebook.ipynb
```

Run all cells sequentially from top to bottom. The notebook is fully self-contained.

---

## Repository Structure

```
fx-gedmd-koopman/
│
├── GEDMD_FX_Notebook.ipynb     # Main pipeline notebook — start here
├── merged_forex_data.csv       # Pre-aligned OHLC panel (optional, see note below)
├── requirements.txt            # All Python dependencies
├── thesis.pdf                  # Full MSc dissertation
├── .gitignore                  # Excludes tokens, checkpoints, caches
└── README.md
```

> **Note on data:** If `merged_forex_data.csv` is not included in the repo (it can be large), the notebook's **Data Acquisition** section will fetch and build it automatically using the OANDA API. This requires a valid token.

---

## Notebook Walkthrough

The notebook is divided into clearly labelled sections:

| Section | What It Does |
|---------|-------------|
| **1. Data Acquisition** | Fetches daily OHLC for EUR/USD, USD/JPY, GBP/USD, USD/CNH, USD/CAD via OANDA API |
| **2. Preprocessing** | Aligns timestamps, forward-fills gaps, computes simple returns and normalised prices |
| **3. Graph Construction** | Builds Fisher-z-filtered correlation-confidence neighbourhood graphs per rolling window |
| **4. Operator Estimation** | Constructs Hankel embeddings and fits local Koopman operators with Tikhonov regularisation |
| **5. Scenario Comparison** | Runs all three scenarios (vanilla, FX-tuned, scaled regularisation) |
| **6. Robustness Analysis** | Monte Carlo perturbation testing and eigenvalue diagnostics |
| **7. Walk-Forward Forecasting** | Generates strictly out-of-sample τ-ahead return forecasts |
| **8. Portfolio Evaluation** | Threshold-based long-only strategy vs equal-weight benchmark |

---

## Dependencies

All dependencies are listed in `requirements.txt`. Key libraries:

```
numpy>=1.23
pandas>=1.5
scipy>=1.9
matplotlib>=3.6
scikit-learn>=1.1
jupyter>=1.0
requests>=2.28
```

Install all at once:
```bash
pip install -r requirements.txt
```

---

## Configuration

The optimal hyperparameters (Scenario 3) are set at the top of the notebook and can be adjusted:

```python
L            = 30      # Embedding depth (delay-coordinate window)
M            = 80      # Calibration window size (trading days)
TAU          = 20      # Forecast horizon (trading days)
K            = 3       # Maximum neighbourhood size per asset
C_THR        = 0.05    # Fisher-z correlation confidence threshold
LAMBDA_SCALE = 0.01    # Scaled Tikhonov regularisation γ  (λ = γ · s₁²)
THRESHOLD    = 0.01    # Portfolio activation threshold
```

---

## Results Summary

| Scenario | Setup | Cumulative Return | Sharpe Ratio |
|----------|-------|:-----------------:|:------------:|
| Scenario 1 | Equity-optimised params (Yip et al., 2023) | 4.87% | < benchmark |
| Scenario 2 | FX-tuned hyperparameters | Moderate | Positive |
| **Scenario 3** | **FX-tuned + scaled Tikhonov** | **62.96%** | **≈ 1.03** |

---

## Troubleshooting

**`OANDA_TOKEN` not found**  
Make sure you've exported the variable in the same terminal session before launching Jupyter.

**Missing data / API errors**  
OANDA's free accounts have rate limits. If fetching fails, wait a few seconds and re-run the data cell. The notebook handles batched requests automatically.

**Kernel crashes on large windows**  
Reduce `M` (window size) or `L` (embedding depth) if memory is tight. Results are robust to modest reductions.

**`.ipynb_checkpoints` appearing in git**  
These are excluded by `.gitignore` already. If they appear, run:
```bash
git rm -r --cached .ipynb_checkpoints
```

---

## Citation

If you use this code or build on this methodology, please cite:

```bibtex
@mastersthesis{mitra2025fx,
  author  = {Souvik Mitra},
  title   = {Structure and Predictability in Foreign Exchange Markets:
             A Koopman-Operator and Graph-Embedded Dynamic Mode Decomposition Framework},
  school  = {University of Surrey},
  year    = {2025},
  type    = {MSc Dissertation}
}
```

---

## Related Work

- Yip et al. (2023) — Graph-Embedded DMD for Stock Price Prediction *(the equity baseline this work extends)*
- Mann & Kutz (2016) — Dynamic Mode Decomposition for Financial Trading Strategies
- Schmid (2010) — Dynamic Mode Decomposition of Numerical and Experimental Data
- Cont (2001) — Empirical Properties of Asset Returns: Stylised Facts

---

## Licence

This repository is made available for **academic and research purposes**. Please contact the author before using the methodology or code in any commercial application.
