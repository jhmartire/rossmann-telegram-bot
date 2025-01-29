# Rossmann Revenue Forecast
# 0.0 Guidelines

This project utilizes data extracted and processed from [Kaggle: Rossmann Store Sales](https://www.kaggle.com/c/rossmann-store-sales/overview). 

The prediction API is hosted on [Render](https://render.com/) at https://rossmann-telegram-bot.onrender.com . A Telegram bot is also available under the username @project_rossman_bot (RossmannBot).

---

# 1.0 Business Problem

Rossmann operates over 3,000 pharmacies across 7 European countries. As part of a modernization plan, the CFO aims to renovate stores to enhance infrastructure and customer service. To determine the budget allocation for each store, regional managers were asked to provide 6-week revenue forecasts. However, manual forecasting has proven error-prone. 

**Objective**: Automate revenue predictions for the next 6 weeks, enabling the CFO to make data-driven decisions and assess store eligibility for renovations.

---

# 2.0 Business Assumptions

1. The CFO must access forecasts from anywhere.
2. The CFO requires flexibility to analyze individual cases.
3. Days when stores were closed are excluded from predictions.
4. Only stores with sales > 0 in historical data are considered.
5. Financial thresholds for renovation eligibility:
   - **Renovation Approval**: Stores with forecasted average revenue ≥ historical average.
   - **Budget Allocation**:
     - <2.5% forecast deviation → 7.5% of total revenue.
     - 2.5%–5% deviation → 10% of total revenue.
     - >5% deviation → 12.5% of total revenue.

## 2.1 Data Description
| Attribute | Description |
| -- | -- |
| Store | Unique store identifier |
| Date | Sales date |
| DayOfWeek | Numeric day of the week (1-7) |
| Sales | Daily revenue |
| Customers | Daily customer count |
| Open | Store status: 1 = Open, 0 = Closed |
| StateHoliday | Public holiday indicator: a = Public, b = Easter, c = Christmas, 0 = None |
| SchoolHoliday | 1 = Closed for school holiday, 0 = Open |
| StoreType | Store model (a, b, c, d) |
| Assortment | Product variety: a = Basic, b = Extra, c = Extended |
| CompetitionDistance | Distance (meters) to nearest competitor |
| CompetitionOpenSince | [Month/Year] Opening date of nearest competitor |
| Promo | 1 = Active promotion, 0 = None |
| Promo2 | 1 = Ongoing extended promotion, 0 = Inactive |
| Promo2Since | [Year/Week] Start date of extended promotion |
| PromoInterval | Months when extended promotions are renewed (e.g., "Feb,May,Aug,Nov") |

---

# 3.0 Solution Strategy

Adopted the **CRISP-DM methodology** with iterative cycles across 9 phases:

## 3.1 CRISP-DM Phases
1. **Business Problem**: Define stakeholders and objectives.
2. **Business Understanding**: Align with stakeholders to prototype solutions.
3. **Data Collection**: Aggregate data from Kaggle and internal sources.
4. **Data Cleaning**: Handle missing values, outliers, and inconsistencies.
5. **Exploratory Analysis**: Investigate relationships, generate hypotheses, and engineer features.
6. **Data Modeling**: Apply statistical transformations for model readiness.
7. **ML Algorithms**: Train and validate models (Validation Holdout, Cross-Validation, Fine-Tuning).
8. **Performance Evaluation**: Select best-performing model using MAPE (Mean Absolute Percentage Error).
9. **Deployment**: Publish API and web application for CFO access.

## 3.2 Final Product
A web application with three tabs:
1. **Home**: Overview and support contact.
2. **Bulk Search**: Multi-store forecast analysis.
3. **Single Search**: Individual store analysis with multi-select capability.

**Output**: Table showing Store ID, Forecasted Revenue, Renovation Eligibility, and Max Budget. Data exportable to CSV.

## 3.3 Tools Used
- **Python 3.10.4** (Pandas, Scikit-learn, XGBoost)
- **Git/GitHub** (Version Control)
- **Jupyter Notebook/VSCode** (Development)
- **Render** (API Hosting)
- **Streamlit** (Frontend Deployment)
- **Telegram API** (Bot Integration)

---

# 4.0 Data Analysis

Post-cleaning, statistical analysis and hypothesis testing revealed key insights:

## 4.1 Business Insights
12 hypotheses tested, two most impactful:

### H10: Stores sell more after the 10th of each month.
**False**: Sales gradually decline post-10th.  
![H10](src/image/h10.png)

### H11: Stores sell less on weekends.
**True**: Significant drop on Sundays.  
![H11](src/image/h11.png)

---

# 5.0 Machine Learning Models

Tested Algorithms:
1. Average Model (Baseline)
2. Linear Regression
3. Lasso Regression
4. Random Forest Regressor
5. XGBoost Regressor

---

# 6.0 Model Selection

**Primary Metric**: MAPE (Interpretability for stakeholders).

## 6.1 Model Performance (Cross-Validation)
| Model | MAE | MAPE | RMSE |
| ----- | --- | ---- | ---- |
| Linear Regression | 2083.17 ± 294.98 | 0.30 ± 0.02 | 2958.87 ± 466.72 |
| Lasso Regression | 2117.66 ± 340.94 | 0.29 ± 0.01 | 3061.89 ± 503.23 |
| Random Forest | 836.61 ± 217.10 | 0.12 ± 0.02 | 1254.30 ± 316.17 |
| XGBoost | 7049.20 ± 588.65 | 0.95 ± 0.00 | 7715.20 ± 689.51 |

## 6.2 Final Model: XGBoost Regressor
**Reason**: Resource constraints on Render favored lighter models. Post-tuning metrics:
- **MAE**: 767.87
- **MAPE**: 11.53%
- **RMSE**: 1105.00

---

# 7.0 Business Results

## 7.1 Model Performance
- Captured cyclical trends with minimal error divergence.  
![Model Performance](src/image/Performance%20modelo.png)

## 7.2 Store-Level Accuracy
- Most stores clustered near 0% MAPE.  
![Store Performance](src/image/performance_loja.png)

## 7.3 Financial Impact
| Scenario | Revenue (R$) |
| -------- | ------------ |
| Forecast | 285,982,336 |
| Worst Case | 285,122,909 |
| Best Case | 286,841,800 |

---

# 8.0 Production Deployment
- **API**: [Viewer in  Render](https://api-rossmann-8d96.onrender.com/)
- **Web App**: [Streamlit Dashboard](https://regression-2022-1-rossmann.streamlit.app/)
- **Telegram Bot**: @project_rossman_bot 

---

# 9.0 Conclusion
This MVP enables the CFO to plan renovations effectively. While stable, iterative improvements are planned.

---

# 10.0 Next Steps
1. Investigate outlier stores for custom modeling.
2. Test additional algorithms (e.g., LightGBM, Prophet).
3. Engineer time-series features (lag, rolling windows).
4. Collaborate with stakeholders on new hypotheses.

---
