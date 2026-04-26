# ACC102 Mini Assignment — Track 2: GitHub Data Analysis Project

## Comparative Profitability Analysis of Two US-Listed Companies (2019–2023)

>**Student:** Xinyi Chen
>
>**Module:** ACC102 – Artificial Intelligence-Driven Data Analytics, Xi'an Jiaotong-Liverpool University
>
>**Track:** Track 2 – GitHub Data Analysis Project
>
>**Submission Deadline:** 27 April 2026

---

## 1. Problem & User

**Analytical Problem:**
For any two selected US-listed companies, which firm demonstrates stronger profitability and greater profitability stability over the period 2019–2023, based on Return on Equity (ROE) and Net Profit Margin — and what implications does this have for investment decision-making?

**Target User:**
Business students, junior analysts, or anyone interested in understanding firm performance using publicly available financial data. The notebook is designed as a reusable Python workflow with visual outputs and written interpretations, ensuring accessibility for users without advanced technical skills.

---

## 2. Data Source

| Field | Details |
|---|---|
| **Primary Source** | U.S. Securities and Exchange Commission (SEC) EDGAR — official public filing database |
| **Access Method** | Three free public REST API endpoints (no login or API key required) |
| **Access Date** | April 2026 |
| **Year Range** | 2019–2023 (five fiscal years) |
| **Filing Types** | 10-K, 10-K/A (US domestic); 20-F, 20-F/A, 40-F, 40-F/A (foreign companies listed in the US) |

**Three API endpoints used:**

| Endpoint | Purpose |
|---|---|
| `https://www.sec.gov/files/company_tickers.json` | Maps stock tickers to SEC company IDs (CIK numbers) |
| `https://data.sec.gov/submissions/CIK{cik}.json` | Retrieves company profile: name, exchange, industry |
| `https://data.sec.gov/api/xbrl/companyfacts/CIK{cik}.json` | Retrieves standardised annual financial figures |

**Financial fields extracted:** Revenue, NetIncomeLoss, StockholdersEquity, Assets (all in USD).

**Offline fallback:** Every successful live SEC fetch automatically saves an Excel backup to the `data/` folder. If the SEC server is unreachable on a future run, the notebook automatically loads the local backup instead, so the analysis can always be completed without internet access.

---

## 3. Methods

The notebook follows a structured eight-section pipeline:

| Section | What It Does |
|---|---|
| 1 — Project Overview | Defines the analytical problem, target user, and data source |
| 2 — Environment Setup | Installs packages; creates `figures/`, `data/`, and `outputs/` directories automatically |
| 3 — Data Acquisition | Fetches live data from SEC EDGAR with 3-retry logic; saves Excel backup to `data/`; falls back to `data/` if offline |
| 4 — Data Cleaning | Removes rows missing ROE or Net Profit Margin; clips extreme values to ±300% |
| 5 — Analysis & Visualisation | Generates 8 charts saved to `figures/` |
| 6 — Key Findings | Auto-generates written interpretation of profitability and stability results |
| 7 — Export | Saves two CSV result files to `outputs/` |
| 8 — Main Execution | Single function call runs the entire pipeline end to end |

**Metrics computed:**

| Metric | Formula |
|---|---|
| Net Profit Margin | Net Income ÷ Revenue × 100 |
| Return on Equity (ROE) | Net Income ÷ Shareholders' Equity × 100 |
| Revenue CAGR | (End Revenue ÷ Start Revenue)^(1/years) − 1 |
| Stability Score | 1 ÷ (std ÷ \|mean\|), capped at 10 — higher = more consistent |
| Composite Stability | Average of NPM Stability and ROE Stability scores |

---

## 4. Key Findings (Default Run: AAPL vs MSFT, 2019–2023)

> Change `TICKER_A` and `TICKER_B` in Cell 32 to compare any two US-listed companies.

| Metric | Apple (AAPL) | Microsoft (MSFT) |
|---|---|---|
| Mean Revenue (USD bn) | 335.62 | 169.42 |
| Mean Net Income (USD bn) | 80.83 | 57.98 |
| Mean Net Profit Margin | 23.73% | 33.89% |
| Mean ROE | **130.41%** | 39.54% |
| Revenue CAGR | 10.17% | 13.92% |
| Composite Stability Score | 6.05 / 10 | **10.00 / 10** |

- **Higher average profitability:** Apple (AAPL) leads with a mean ROE of 130.41%, driven by strong net income and low retained equity from share buybacks.
- **Greater stability:** Microsoft (MSFT) scores a perfect 10.00 Composite Stability Score — highly consistent and predictable profitability across all five years.
- **Investment implication:** Apple offers higher returns with greater variability; Microsoft offers lower but more stable returns — a classic risk-return trade-off that investors should weigh against their own risk tolerance.
- **Important note:** ROE should never be read in isolation. Apple's high ROE is partly driven by reduced equity from share buybacks, not purely earnings power.

---

## 5. Charts Generated

Eight figures are automatically produced and saved to the `figures/` folder:

| Figure | Description |
|---|---|
| `fig_track2_1_AAPL_MSFT_revenue.png` | Revenue trend, 2019–2023 |
| `fig_track2_2_AAPL_MSFT_net_income.png` | Net income trend, 2019–2023 |
| `fig_track2_3_AAPL_MSFT_equity.png` | Shareholders' equity trend, 2019–2023 |
| `fig_track2_4_AAPL_MSFT_roe.png` | Return on equity trend, 2019–2023 |
| `fig_track2_5_AAPL_MSFT_npm.png` | Net profit margin trend, 2019–2023 |
| `fig_track2_6_AAPL_MSFT_trajectory.png` | Profitability trajectory scatter: ROE vs NPM |
| `fig_track2_7_AAPL_MSFT_heatmap.png` | ROE and NPM heatmap by year |
| `fig_track2_8_AAPL_MSFT_summary.png` | Five-year summary bar charts |

---

## 6. How to Run

### Prerequisites
- Python 3.8 or above
- Internet connection (for live SEC data fetch) — or use the pre-downloaded `data/` Excel files for offline use

### Step 1 — Install dependencies

```bash
pip install -r requirements.txt
```

> **Note:** You can skip this step. Cell 3 of the notebook installs all required packages automatically when run.

### Step 2 — Open the notebook

```bash
jupyter notebook ACC102_Track_2_XinyiChen_MiniAssignment.ipynb
```

### Step 3 — Set your two companies

Open **Cell 32** and change the two ticker symbols:

```python
TICKER_A = 'AAPL'   # ← replace with your first company ticker
TICKER_B = 'MSFT'   # ← replace with your second company ticker
```

### Step 4 — Run all cells

Go to **Kernel → Restart & Run All**. The notebook will automatically:
- Download live financial data from SEC EDGAR
- Save Excel backups to `data/`
- Generate all 8 charts and save them to `figures/`
- Export 2 CSV result files to `outputs/`
- Print a full results summary

### Offline mode

If the SEC server is unreachable, the notebook automatically loads the Excel backup files from `data/`. The default AAPL and MSFT backup files are included in this repository so the notebook can run without internet access for this default comparison.

### Supported tickers

Any US-listed company with SEC EDGAR filings is supported. Examples: `AAPL`, `MSFT`, `GOOGL`, `AMZN`, `TSLA`, `JPM`, `NVDA`, `META`. Companies that listed after 2019 may have fewer than five years of data.

---

## 7. Repository Structure

```
acc102-track2-xinyi-chen/
│
├── notebook.ipynb   ← Main notebook (start here)
├── README.md                                        ← This file
├── requirements.txt                                 ← Python package dependencies
│
├── data/                                            ← Excel backups for offline fallback
│   ├── AAPL_financials_2019_2023.xlsx               ← Auto-generated by notebook
│   └── MSFT_financials_2019_2023.xlsx               ← Auto-generated by notebook
│
├── outputs/                                         ← Exported CSV result files
│   ├── AAPL_MSFT_comparison.csv                     ← Full year-by-year data table
│   └── AAPL_MSFT_summary.csv                        ← Five-year summary statistics
│
└── figures/                                         ← Auto-generated chart images (PNG)
    ├── fig_track2_1_AAPL_MSFT_revenue.png
    ├── fig_track2_2_AAPL_MSFT_net_income.png
    ├── fig_track2_3_AAPL_MSFT_equity.png
    ├── fig_track2_4_AAPL_MSFT_roe.png
    ├── fig_track2_5_AAPL_MSFT_npm.png
    ├── fig_track2_6_AAPL_MSFT_trajectory.png
    ├── fig_track2_7_AAPL_MSFT_heatmap.png
    └── fig_track2_8_AAPL_MSFT_summary.png
```

---

## 8. Limitations & Next Steps

**Current limitations:**
- Only US-listed companies with SEC EDGAR us-gaap data are supported; non-US or very small companies may have incomplete records
- ROE can be misleadingly high when equity is reduced by share buybacks — should always be read alongside NPM and absolute figures
- Companies listed after 2019 will have fewer than five years of data, reducing stability score reliability
- The fixed 2019–2023 window does not dynamically adjust to the most recent available filing year

**Possible improvements:**
- Extend year range dynamically to the most recent available filing
- Add sector-level benchmark comparisons
- Support multi-company (more than two) analysis simultaneously
- Incorporate additional metrics such as cash flow ratios and debt-to-equity


---

*Data source: U.S. Securities and Exchange Commission (SEC) EDGAR public API (sec.gov). Accessed April 2026.*
*Module: ACC102, Xi'an Jiaotong-Liverpool University. Submission deadline: 27 April 2026.*