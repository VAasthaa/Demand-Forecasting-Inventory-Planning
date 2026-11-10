# Demand Forecasting & Inventory Planning

Forecasting daily product demand for a retail store and turning the forecast into inventory decisions (safety stock and reorder points).


## Business problem

A retail chain with 10 stores sells the same 50 items in every store. Each store has to decide how much stock to hold:

- **Too little stock** → empty shelves, lost sales, unhappy customers
- **Too much stock** → cash tied up in inventory, higher storage costs

**Goal:** predict how many units of each item will sell each day, and use that forecast to decide **how much buffer stock to keep** and **when to reorder**.

## Dataset

- **Source:** [Store Item Demand Forecasting Challenge (Kaggle)](https://www.kaggle.com/competitions/demand-forecasting-kernels-only)
- **Size:** 913,000 rows: 10 stores × 50 items × 1,826 days (1 Jan 2013 – 31 Dec 2017)
- **Columns:** `date`, `store`, `item`, `sales` (units sold)
- **Quality:** no missing values, no duplicates, no missing days for any store–item pair

## Approach

1. **Data quality checks:** shape, data types, nulls, duplicates, date coverage per store–item pair
2. **Exploratory analysis:**
   - Yearly trend: sales grew every year (+15%, +4.4%, +8.6%, +3.6%)
   - Yearly seasonality: lowest in January, peak in July
   - Weekly pattern: Monday is 21% below average and Sunday is 19% above
   - Store and item ranking: Store 2 is the busiest (12.8% of sales)
3. **Series selected:** Store 2 (highest sales) with a fast (item 15), medium (item 9) and slow (item 5) seller
4. **Train/test split by time:** train on Jan 2013 – Sep 2017, test on Oct – Dec 2017 (92 days)
5. **Models compared, using MAPE (mean absolute percentage error):**
   - **Baseline:** average of the last 30 days
   - **Holt-Winters:** trend + weekly seasonality
   - **Final model:** Holt-Winters on data adjusted by a monthly seasonality index, so it captures both the weekly and the yearly pattern
6. **Inventory planning:** safety stock at a 95% service level with a 7-day lead time, and the reorder point per month
7. **2018 forecast:** retrained on all 5 years and forecast 1 Jan – 31 Mar 2018

## Results

**Forecast accuracy (MAPE, lower is better)**

| Item | Baseline | Holt-Winters (weekly) | Final model |
|---|---|---|---|
| Fast seller (item 15) | 22.7% | 18.9% | **6.5%** |
| Medium seller (item 9) | 27.0% | 22.9% | **10.1%** |
| Slow seller (item 5) | 26.6% | 24.0% | **14.7%** |

**Inventory impact (95% service level, 7-day lead time)**

| Item | Safety stock (baseline) | Safety stock (final model) | Reduction |
|---|---|---|---|
| Fast seller (item 15) | 99 units | 42 units | **57.6%** |
| Medium seller (item 9) | 71 units | 36 units | **49.7%** |
| Slow seller (item 5) | 29 units | 21 units | **28.3%** |

**Reorder point, 2018 (units: place an order when stock falls to this level)**

| Item | Jan | Feb | Mar |
|---|---|---|---|
| Fast seller (item 15) | 648 | 720 | 862 |
| Medium seller (item 9) | 372 | 423 | 505 |
| Slow seller (item 5) | 150 | 160 | 192 |

## Key insights

- Weekly seasonality alone barely beat the baseline. **Adding the yearly pattern cut forecast error by about 2.5×.**
- A more accurate forecast means a smaller safety buffer. On average, **45% less safety stock** is needed for the same service level.
- Slow sellers have a higher percentage error because small daily numbers are noisier.

## Tools

Python (pandas, NumPy, Matplotlib, Seaborn, statsmodels) · Jupyter Notebook · Power BI

## Repository structure

```
├── demand_forecasting.ipynb        # full analysis and models
├── outputs/
│   ├── forecast_accuracy.csv
│   ├── safety_stock_comparison.csv
│   ├── reorder_plan_2018.csv
│   └── daily_forecast_2018.csv
├── images/
│   └── dashboard.png
├── requirements.txt
└── README.md
```

## How to run

1. Download `train.csv` from the Kaggle link above and place it in the same folder as the notebook.
2. Install the libraries: `pip install -r requirements.txt`
3. Open `demand_forecasting.ipynb` and run all cells.


---
**Author:** Aastha Verma · [LinkedIn](https://www.linkedin.com/in/verma-aastha)
