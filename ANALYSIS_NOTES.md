# Descriptive analysis notes

Original observations: 48,204  
Full-row duplicates: 17  
Unique hourly timestamps after full-row deduplication: 40,575  
Observation window: 2012-10-02 to 2018-09-30  
Target: traffic_volume (hourly vehicle count)  
Hourly traffic mean: 3,290.7  
Peak average hour: 16:00 (5,709 vehicles/hour)  
Lowest average hour: 03:00 (373 vehicles/hour)  
Most prevalent primary weather: Clouds (15,123 hourly records)  

These summary plots were independently generated from the supplied UCI CSV after full-row deduplication and one-row-per-timestamp selection (first recorded row per hour), mirroring the notebook's broad cleaning approach. They are descriptive summaries, not model predictions. No predictive model was retrained to produce the charts.

## Important limitations

- Timestamp consolidation retains the first weather observation per hour; alternate observations for the same hour are lost.
- `weather_main` averages do not establish causation; hours, seasons and holidays may confound them.
- The notebook's ten invalid 0 K temperature readings remain unresolved: its `.replace()` calls do not assign results back to `X`.
- The notebook saved no Random Forest evaluation output. SGD regression reported an extremely negative R², indicating a serious model fitting/scaling/outlier issue to investigate; it is excluded from the main comparison charts for readability.
- The notebook's actual-vs-predicted plotting cell uses outdated car-price labels and has no saved figure; it needs correction and execution before publishing.
