# Iowa Biofuel Distribution: Dashboard and Machine Learning Analysis

An analysis of Iowa motor fuel sales by county, 2012-2024, in two parts:

1. **An interactive Streamlit dashboard** for exploring fuel sales, county patterns and biofuel adoption.
2. **A machine learning notebook** that predicts county-level biofuel distribution and forecasts the statewide trend to 2030.

Made by **Fortune Analytics** at FEU Institute of Technology (Analytics Tools and Techniques, IT0095).

Team: Aldave, Adrian | Cortes, Karmelo | Mangubat, John Erick | Villa, Daniel

**My role:** Co-Researcher and Lead Data Analyst (Karmelo Cortes).

## Data

- **Source:** [Iowa Motor Fuel Sales by County and Year](https://data.iowa.gov/Sales-Distribution/Iowa-Motor-Fuel-Sales-by-County-and-Year/hbwp-wys3/about_data), reported to the Iowa Department of Revenue and published on data.iowa.gov.
- **Coverage:** 2012-2024, 99 Iowa counties plus aggregated groups, 9 attributes.
- **Target:** Biofuel Distribution Percentage, the share of motor fuel sales made up of pure ethanol and pure biodiesel.
- **`Cleaned_Iowa_Motor_Fuel_Sales.csv`** (in this repo, 1,273 records) is the cleaned file the dashboard reads.
- The notebook starts from the raw download. See [`data/README.md`](data/README.md).

## Part 1: Interactive dashboard

A Streamlit app built with Plotly. Use the sidebar to switch between descriptive and predictive views and to filter by all years, a year range or a single year.

**Descriptive analytics**
- **Trend analysis:** fuel sales by type and biofuel distribution over time.
- **County insights:** top counties by fuel sales and by biofuel distribution.
- **Fuel comparison:** how the fuel mix changed, with stacked area and pie charts.
- **Retail analysis:** correlations between retail locations, sales and biofuel share, plus sales per location.

**Predictive analytics:** Random Forest and Linear Regression models compared on MAE and R², feature importance, and biofuel distribution forecasts for 2025-2027.

### Run the dashboard

```bash
git clone https://github.com/Melow04/iowa_fuel.git
cd iowa_fuel
pip install -r requirements.txt
streamlit run dashboard.py
```

## Part 2: Machine learning analysis

The full analysis is in [`notebooks/Fortune_Analytics_IOWA.ipynb`](notebooks/Fortune_Analytics_IOWA.ipynb).

**Method**
1. **Cleaning:** fixed a column name, treated missing sales values and retail counts as zero, handled the aggregated "Other" county group, and removed outliers.
2. **Feature engineering:** total fuel sales, biofuel, ethanol-to-gasoline and biodiesel-to-diesel ratios, sales per location, encoded county, a year ordinal and a previous-year lag feature.
3. **Exploration:** temporal trends, growth rates, county rankings and correlation analysis.
4. **Modeling:** Random Forest Regression for county-level prediction and feature importance, and Linear Regression for the yearly trend. Numeric predictors were standardized.
5. **Evaluation:** R², RMSE, MAE and MAPE on an 80/20 split, plus cross-validation.

**Results**

| Model | Split | R² | RMSE | MAE | MAPE |
| --- | --- | --- | --- | --- | --- |
| Random Forest | Train (80%) | 0.9024 | 0.0173 | 0.0090 | 6.37% |
| Random Forest | Test (20%) | 0.7471 | 0.0284 | 0.0163 | 11.76% |
| Linear Regression (yearly trend) | Test | 0.9516 | 0.0039 | 0.0030 | 2.25% |

**Key findings**
- Average biofuel distribution rose steadily from 2013 to 2024, at roughly 0.5 percentage points a year on the linear trend. The forecast reaches about **19% by 2030**, up from about 16% in 2025.
- The previous year's value is by far the strongest predictor in the Random Forest (about **88% of feature importance**), so the model behaves like an autoregressive one.
- The biodiesel-to-diesel ratio (r = 0.521) and sales per location (r = 0.484) are most closely tied to adoption. The raw number of retail locations (r = 0.021) is not.
- The model predicts the fastest growth in Johnson, Montgomery and Monroe counties for 2025.

**Limitations**
- Because the model leans on last year's value, it may miss early growth in counties with low adoption history.
- The train/test split is random across county-year rows, not time-based.
- The trend model is fit on 12 yearly averages, so its forecasts are a baseline and not a precise prediction.
- Pricing, demographic and station-level data were not included and could explain the largest errors (for example in Fremont and Poweshiek counties).

### Run the notebook

```bash
pip install -r requirements-notebook.txt
```

Download the raw CSV into `data/` (see `data/README.md`), then open `notebooks/Fortune_Analytics_IOWA.ipynb` in Jupyter or Google Colab and run all cells.

## Repository structure

```
iowa_fuel/
├── README.md
├── dashboard.py                        # Streamlit dashboard
├── Cleaned_Iowa_Motor_Fuel_Sales.csv   # cleaned data used by the dashboard
├── requirements.txt                    # dashboard dependencies
├── requirements-notebook.txt           # notebook dependencies
├── data/
│   └── README.md                       # how to get the raw dataset
└── notebooks/
    └── Fortune_Analytics_IOWA.ipynb    # full ML analysis
```

## Tools

Python, Streamlit, Plotly, pandas, NumPy, scikit-learn, SciPy, matplotlib, seaborn, Google Colab.
