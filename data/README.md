# Sample data

This folder contains a **representative subset of the synthetic source data** used in the AvocadoMind Power BI portfolio project.

The files are included so that reviewers can inspect the model structure, field definitions, and example records without downloading the full working dataset.

## Sampling approach

- 120 fact rows
- 5 observations sampled from each month from **2024-01 through 2025-12**
- deterministic random seed: `42`
- all 5 marketing channels, all 7 audience segments, and all 6 regions are represented
- only campaign and date dimension rows referenced by the sample fact table are included

> The sample is intentionally smaller than the full dataset used in the Power BI report, so it will **not reproduce the dashboard totals**.

## Files

- `fact_performance_sample.csv` — sampled daily campaign performance observations
- `dim_campaign_sample.csv` — campaign attributes for campaigns referenced by the sample
- `dim_date_sample.csv` — dates referenced by the sample
- `dim_channel.csv` — full channel dimension
- `dim_audience.csv` — full audience dimension
- `dim_region.csv` — full region dimension
- `data_dictionary.csv` — field definitions and model notes

## Data grain

`Fact_Performance` grain:

**1 row = 1 campaign × 1 calendar day**

The fact table connects to the dimensions through:
- `Campaign_ID`
- `Channel_ID`
- `Audience_ID`
- `Region_ID`
- `Date`

All data in this repository is synthetic and created for portfolio / analytical practice purposes.
