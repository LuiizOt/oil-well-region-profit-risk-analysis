# Oil Well Region Selection: Profit and Risk Analysis

## Project Overview

An oil company must choose one of three regions in which to develop 200 new wells with a fixed budget of USD 100 million. This project trains a Linear Regression model per region to predict the reserves of each well, converts the predictions into expected profit, and uses **bootstrapping** to estimate the profit distribution, its 95% confidence interval and the probability of a loss.

**Key outcome:** region **geo_data_1** is recommended. It has the highest bootstrap mean profit (≈ USD 4.56 million), the only fully positive 95% confidence interval, and a risk of loss of 1.5%, the only value below the 2.5% risk limit.

## Business Problem

Developing wells is an expensive, irreversible investment. Choosing a region by the highest point estimate alone ignores uncertainty: a region can look profitable on average and still have a high probability of losing money. The company therefore needs a decision rule that combines expected profit with a risk constraint, considering that only a limited number of points (500) is explored in each region before the best 200 wells are selected.

## Objectives

1. Predict the reserve volume of each well in the three regions.
2. Estimate the profit from developing the 200 wells with the highest predicted reserves.
3. Quantify uncertainty with bootstrapping: mean profit, 95% confidence interval and risk of loss.
4. Recommend a region, keeping only regions whose risk of loss is **below 2.5%** and choosing the one with the highest mean profit.

## Dataset Description

Three files with geological exploration data, provided for educational purposes as part of the TripleTen Data Science program. Each file has 100,000 rows, 5 columns and no missing values.

| Column | Description |
|---|---|
| `id` | Well identifier |
| `f0`, `f1`, `f2` | Anonymized numeric features of the exploration point |
| `product` | **Target:** volume of reserves in the well |

Files: `geo_data_0.csv`, `geo_data_1.csv` and `geo_data_2.csv` (one per region).

**Business parameters used in the notebook**

| Parameter | Value |
|---|---|
| Total budget | USD 100,000,000 |
| Wells selected for development | 200 |
| Points explored per bootstrap sample | 500 |
| Revenue per unit of product | USD 4,500 |
| Bootstrap iterations | 1,000 |
| Random seed | 12345 |

## Technologies and Libraries

- Python
- pandas, NumPy
- scikit-learn (`LinearRegression`, `train_test_split`, `root_mean_squared_error`)
- Matplotlib
- Jupyter Notebook

## Methodology

1. **Modeling:** for each region, the data is split into training (75%) and validation (25%) sets, and a `LinearRegression` model predicts `product` from `f0`, `f1` and `f2`.
2. **Profit function:** the wells are sorted by predicted reserves, the top 200 are selected, and profit = (sum of their **actual** reserves × USD 4,500) − USD 100 million. Using the actual values for the selected wells measures the real outcome of a decision made with the model's predictions.
3. **Bootstrapping:** 1,000 times, 500 wells are drawn with replacement from the validation set (simulating an exploration campaign), the top 200 by prediction are selected, and the profit is calculated. This yields a distribution of profit for each region.
4. **Risk metrics:** mean profit, 95% confidence interval (2.5th and 97.5th percentiles) and risk of loss (share of samples with profit ≤ 0).
5. **Decision rule:** discard the regions with a risk of loss ≥ 2.5%, then choose the region with the highest mean profit.

## Results and Model Performance

**Direct profit:** top 200 wells selected from the full validation set (25,000 wells) of each region.

| Region | Profit (USD) |
|---|---|
| geo_data_0 | 33,208,260 |
| geo_data_1 | 24,150,867 |
| geo_data_2 | 27,103,500 |

**Bootstrap:** 1,000 samples of 500 wells; top 200 selected in each sample.

| Region | Mean profit (USD) | 95% confidence interval (USD) | Risk of loss |
|---|---|---|---|
| geo_data_0 | 3,961,650 | [−1,112,155, 9,097,669] | 6.9% |
| **geo_data_1** | **4,560,451** | **[338,205, 8,522,895]** | **1.5%** |
| geo_data_2 | 4,044,039 | [−1,633,504, 9,503,596] | 7.6% |

The validation RMSE of each regression model is calculated inside `train()` but is not displayed in the notebook's outputs.

## Key Findings

- **The point estimate and the risk-aware analysis disagree.** Selecting from the full validation set, geo_data_0 looks the most profitable. When the realistic exploration scenario (500 points) is simulated, geo_data_1 has the highest mean profit.
- geo_data_1 is the only region whose 95% confidence interval stays entirely positive.
- Only geo_data_1 satisfies the risk constraint (1.5% < 2.5%); geo_data_0 (6.9%) and geo_data_2 (7.6%) exceed it by a wide margin.

## Conclusions

**Achievement of objectives:** all objectives were met, and the analysis recommends developing wells in **geo_data_1**, the region with the best balance between profitability and safety.

**Limitations**

- The RMSE and the average predicted reserves per region are computed but not shown, so the quality of the regression models cannot be compared from the saved outputs.
- Only Linear Regression is evaluated.
- The break-even reserve volume per well (budget ÷ (200 × revenue per unit)) is not calculated, and the mean predicted and actual reserves are not compared with it.
- The unit of `product` is not specified in the notebook; profit values assume USD 4,500 per unit.
- The original run's final cell (the profit histogram) ended with a kernel-crash message from the IDE, which was removed from the saved output; the chart itself is preserved.

**Potential improvements:** display the RMSE and the mean predictions per region, add the break-even analysis, and test the sensitivity of the recommendation to the budget, the revenue per unit and the number of explored points.

## Repository Structure

```text
oil-well-region-profit-risk-analysis/
├── README.md
├── main.ipynb
└── datasets/
    ├── geo_data_0.csv
    ├── geo_data_1.csv
    └── geo_data_2.csv
```

## How to Run

1. Create and activate a Python 3 environment (the notebook was last run with Python 3.13):

   ```bash
   python -m venv .venv
   # Windows
   .venv\Scripts\activate
   # macOS / Linux
   source .venv/bin/activate
   ```

2. Install the dependencies. `root_mean_squared_error` requires **scikit-learn 1.4 or later**:

   ```bash
   pip install pandas numpy "scikit-learn>=1.4" matplotlib jupyter
   ```

3. Start Jupyter **from the project root**, so that the relative paths `./datasets/<file>.csv` resolve correctly, and open `main.ipynb`:

   ```bash
   jupyter notebook main.ipynb
   ```

Variable and function names (for example `calcular_lucro`, `orcamento_total`) and printed labels are in Portuguese, as in the original code.

## Author

**Luiz Otávio Carneiro Filho**
Geospatial Engineer | GIS & Data Science
[LinkedIn](https://www.linkedin.com/in/luizot/)
