# Bike Demand Forecasting 🚲

Predict hourly bike rentals using calendar features and the observed rental count from the previous hour.

## Dataset

UCI Bike Sharing Dataset, hourly observations from 2011–2012. The included `hour.csv` is used directly.
Source: https://archive.ics.uci.edu/dataset/275/bike+sharing+dataset
Citation: Fanaee-T, H. (2013). Bike Sharing [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5W894

Dataset license: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). The included `hour.csv` is unchanged.

## Run locally

Validated with Python 3.13.16. All 59 nonempty code cells completed in order in a fresh Python process; the VS Code kernel workflow and a clean dependency installation were not tested.

Run these commands from the project folder:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

In VS Code, open `analysis.ipynb`, select the `.venv` Python kernel, then choose **Restart Kernel and Run All**. The notebook reads `hour.csv`, trains models, evaluates them and saves `bike_demand_catboost.cbm` before loading it again. The VS Code Python and Jupyter extensions are needed for notebook execution.

## Method

Exploratory analysis, chronological 80/20 train/test splits, mean baseline, linear regression and CatBoost. CatBoost uses 500 iterations, depth 6, learning rate 0.05 and random seed 0.

The lag is retained only when consecutive timestamps are exactly one hour apart. Rows without a valid lag are removed. The final CatBoost comparison uses identical training and test rows.

## Results

| Model | MAE | RMSE |
|---|---:|---:|
| Mean baseline | 174.98 | 232.61 |
| Linear regression | 150.72 | 206.88 |
| CatBoost without lag (matched rows) | 96.39 | 139.57 |
| CatBoost with lag (matched rows) | 32.13 | 51.63 |

The baseline and linear regression use the original split; their test rows differ from the final CatBoost comparison. Metrics are rental counts and may vary slightly between environments.

## Prediction

After running the notebook:

```python
predict_demand(hr=17, weekday=2, season=3, cnt_lag1=400)
```

Features: `hr` (0–23), `weekday` (0 = Sunday), `season` (1 = winter, 2 = spring, 3 = summer, 4 = autumn), `cnt_lag1` (observed rentals in the preceding hour).

## Limitations

Evaluation uses the actual previous-hour demand for each test prediction. This represents rolling one-hour-ahead prediction; it does not measure a multi-hour forecast without new observations. The lag must be available at prediction time. Errors can increase during peak hours. The saved model is trained on the training portion, not the entire dataset.

## Files

- `analysis.ipynb`: analysis, training and evaluation.
- `hour.csv`: input data.
- `bike_demand_catboost.cbm`: saved CatBoost model.
- `requirements.txt`: package versions used for validation.
- `.gitignore`: excludes training logs, caches and virtual environments.
