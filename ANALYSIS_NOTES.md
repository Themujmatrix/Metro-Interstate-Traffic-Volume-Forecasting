# Analysis Notes — Metro Interstate Traffic Volume Forecasting (Version 6)

**Author:** Mujtaba Ahmed | **Group:** Individual project  
**Dataset:** https://archive.ics.uci.edu/dataset/492/metro+interstate+traffic+volume  
**Notebook:** `Metro_Traffic_Forecast(6).ipynb` (preserved under `Metro_Traffic_Forecast.ipynb`)

## Dataset audit

- Raw: 48,204 observations, 9 fields, 17 exact duplicate rows.
- Missing `holiday` labels in raw file: 48,143; filled with `None` (non-holiday category).
- After full-row deduplication: 48,187 rows; after first-record-per-timestamp: 40,575 rows.
- Date range: 2012-10-02 09:00 to 2018-09-30 23:00.
- Mean hourly traffic: 3,290.7 vehicles.
- Highest average hour: 16:00 (5,708.6 vehicles/hour); lowest: 03:00 (373.2).
- Busiest day by mean traffic: Friday.
- Most frequent primary weather category: Clouds (15,123 hours).

## Saved model evaluations

| Model | Training R² | Test R² | Test RMSE (vehicles/hour) |
|---|---:|---:|---:|
| RandomForest Regression | 0.8736 | 0.7939 | 894.35 |
| GradientBoosting Regression | 0.7949 | 0.7922 | 898.09 |
| Polynomial Regression | 0.6595 | 0.6548 | 1,157.47 |
| Ridge Regression | 0.1515 | 0.1554 | 1,810.57 |
| Linear Regression | 0.1515 | 0.1553 | 1,810.69 |
| Lasso Regression | 0.1501 | 0.1550 | 1,810.91 |
| ElasticNet Regression | 0.1299 | 0.1364 | 1,830.79 |
| SGD Regression | -345128941396.6241 | -12504675.0143 | 6,966,532.02 |

All metrics above come from executed cells in Version 6, not newly trained models. They reflect the original notebook's data cleaning, feature selection, and chronological test period.

## Interpretation

- **RandomForest Regression** leads with test R² 0.7939, RMSE 894.35 vehicles/hour.
- Gradient Boosting is close, with test R² 0.7922. Model ranking differences are small; do not overclaim superiority without additional time-window validation.
- Random Forest training R² 0.8736 versus testing R² 0.7939 suggests a nonzero generalization gap.
- SGD yields severely negative test R² (-12,504,675.0143) and RMSE 6,966,532.02 vehicles/hour. The result is retained transparently but suppressed from readable comparison plots.
- Weather and time plots show *associations*, not evidence that a particular weather category causes more or less traffic.

## Observed notebook issues

1. **Temperature:** Notebook Cell 22 replaces -273.15 °C with -7.2 °C after Kelvin conversion. This changes the invalid zero-Kelvin readings but uses an unsupported guessed value. A median imputer fitted within the training pipeline is more defensible; recalculated model results would be required after changing this.
2. **Rainfall:** The original data contain `rain_1h = 9831.3`; source verification and outlier treatment should occur before retraining. Avoid arbitrarily capping legitimate storm observations.
3. **Duplicated hours:** The `drop_duplicates(subset="date_time")` operation picks the first record, discarding alternative weather readings. Hour-level aggregation is a possible future improvement.
4. **Future-weather information:** Observed weather for target hour is only valid at prediction time if known; a live forecasting deployment should use contemporaneously available weather forecasts.
5. **Ablation:** Both `weather_main` and `weather_description` represent the same weather event at different granularities. Test one versus both rather than claiming independent importance.
6. **Evaluation:** One chronological 80/20 holdout is appropriate as a first step; rolling-origin validation, a baseline and MAE should be added to support model selection.
7. **Reproducibility:** Default SGD parameters have no `random_state` here. Fix seed, inspect convergence, and compare models after preprocessing is corrected.

## Visual artifacts and provenance

- `traffic_by_hour.png`, `traffic_by_weekday.png`, `traffic_by_weather.png`, `traffic_distribution.png`: recalculated descriptive aggregates from supplied UCI CSV using the notebook's first-row-per-hour reduction.
- `model_test_r2.png`, `model_test_rmse.png`, `train_test_r2.png`: plots of the **saved Version 6 notebook metrics**. SGD omitted from these *charts only*.
- `notebook_correlation_heatmap.png`, `notebook_actual_vs_predicted_diagnostic.png`, `notebook_r2_comparison.png`: images extracted from the notebook's stored PNG output; diagnostic plot includes unstable SGD.

## Recommended next iteration

Replace unsupported temperature repairs with missing values and fit a `SimpleImputer` using training data only; investigate rainfall anomaly; aggregate repeated weather records more deliberately; add time-based features; run time-series cross-validation. Only then should the README and proposal be updated with new model metrics.
