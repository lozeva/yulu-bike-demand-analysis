# Yulu Bike Demand Analysis

## 1. Overview
This project analyzes the factors that influence **demand for Yulu electric bike rentals** using exploratory data analysis (EDA) and statistical hypothesis testing.

Yulu is India's leading micro-mobility service provider, offering shared electric bikes for daily urban commutes. Their mission is to reduce traffic congestion by providing a safe, affordable, and eco-friendly transportation alternative. The company observed fluctuations in rental demand and wants to understand which factors significantly impact usage.

The goal of this project is to identify the **key variables affecting bike rental demand** so that the company can make **data-driven operational decisions**.

---

## 2. Business Problem

Yulu has observed a dip in revenue and wants to understand:

* What factors significantly influence the number of rented bikes in the Indian market?

Understanding these relationships helps optimize:
* Bike supply distribution
* Operational planning
* Pricing and demand forecasting

---

## 3. Objectives

The main objectives of this analysis are:
1. Perform **Exploratory Data Analysis (EDA)** on the dataset.
2. Identify patterns and variables that significantly affect bike rental demand.
3. Conduct **statistical hypothesis tests** to confirm or reject assumptions about demand drivers.
4. Provide **actionable business recommendations**.

---

## 4. Dataset

**Source:** [Kaggle - Yulu Bike Sharing Dataset](https://www.kaggle.com/datasets/ranitsarkar01/yulu-bike-sharing-data)

**Size:**
* 10,886 rows
* 12 columns

**File included in repository:**

```
yulu_bike_sharing_dataset.csv
```

### Dataset Features

| Column       | Type                | Description                                                                            |
| ------------ | ------------------- | -------------------------------------------------------------------------------------- |
| `datetime`   | datetime            | Hourly timestamp                                                                       |
| `season`     | categorical (1-4)   | 1: Spring, 2: Summer, 3: Fall, 4: Winter                                               |
| `holiday`    | binary              | 1: public holiday, 0: not a holiday                                                    |
| `workingday` | binary              | 1: working day, 0: weekend/holiday                                                     |
| `weather`    | categorical (1-4)   | 1: Clear/Partly cloudy, 2: Mist/Cloudy, 3: Light Snow/Rain, 4: Heavy Rain/Thunderstorm |
| `temp`       | continuous          | Temperature in Celsius                                                                 |
| `atemp`      | continuous          | Feels-like temperature in Celsius                                                      |
| `humidity`   | continuous          | Relative humidity (%)                                                                  |
| `windspeed`  | continuous          | Wind speed                                                                             |
| `casual`     | count               | Count of casual (non-registered) users                                                 |
| `registered` | count               | Count of registered users                                                              |
| `count`      | **target**          | Total rentals - target variable (`casual` + `registered`)                              |

---

## 5. Research Questions

This analysis investigates the following hypotheses:
1. Does working day vs. non-working day status affect rental demand?
2. Does weather condition affect rental demand?
3. Does season affect rental demand?
4. Does the relationship between weather and season indicate statistical dependence that could affect demand modeling?

---

## 6. Statistical Framework

Significance level pre-registered before running any test (to avoid p-hacking):
```
α = 0.05
```

> **Note on multiple comparisons:** Running 4 hypothesis tests under $\alpha$ = 0.05 gives a family-wise error rate of approximately 18.5%. This is addressed in each post-hoc analysis using **Bonferroni correction**.

**Standard testing protocol applied to all hypotheses:**
1. **Normality check** - Shapiro-Wilk test on each group;
2. **Variance homogeneity** - Levene's test;
3. **Main test** - Kruskal-Wallis (Tests 1-3) or Chi-Square (Test 4);
4. **Effect size** - $\eta ^ 2$ for Kruskal-Wallis, Cramer's V for Chi-Square;
5. **Post-hoc pairwise comparisons** - Dunn's test with Bonferroni correction (implemented manually, as SciPy has no native implementation).

---

## 7. Project Structure

```
yulu_bike_demand_analysis.ipynb   # Main analysis notebook
yulu_bike_sharing_dataset.csv     # Dataset
README.md                         # Project documentation
```

---

## 8. Exploratory Data Analysis

EDA is structured to inform test selection before hypothesis testing, not after. Key sections include:

**Distribution analysis**
* The target variable `count` is right-skewed, confirmed visually (histogram + Q-Q plot) and formally by Shapiro-Wilk (p < 0.001), ruling out parametric tests.

**Outlier detection and treatment**
* IQR method applied to `windspeed`, `casual`, `registered`, and `count`;
* **Winsorization (capping)** used instead of row removal to preserve rare high-demand observations (for example: holidays) while limiting distributional distortion.

**Categorical variable comparisons**
* `season` and `weather` show clear differences in average rentals; `workingday` and `holiday` status show minimal visual difference (formally tested in Section 8 of the notebook).

**Temporal patterns**
* Monthly -> rentals peak between May-October, aligning with warmer temperatures;
* Hourly -> two commuter spikes at 8am and 5-6pm, suggesting registered users primarily use Yulu for work commutes.

**Casual vs. registered user segmentation**
* Registered users average 2-4 times more rentals than casual users across all conditions;
* On working days, registered usage rises while casual usage drops - and vice versa on non-working days - revealing two distinct use cases: **commuting** (registered) and **leisure** (casual).

**Correlation analysis**
* Both Pearson and Spearman correlations computed. `temp`/`atemp` correlate strongly and positively with `count`, `humidity` moderately negatively, `windspeed` weakly negatively;
* `temp` and `atemp` are nearly redundant (r > 0.98) - using both in any future regression model would introduce multicollinearity.

---

## 9. Key Insights

* **Season strongly affects demand** - Fall and Summer drive the highest rental volumes, Spring the lowest;
* **Weather significantly impacts usage** - clear conditions produce the highest demand, heavy rain/snow suppresses it sharply;
* **Working day status does not produce a net effect on total demand** - `casual` and `registered` users offset each other, masking the underlying behavioral split between the two segments;
* **Weather and season are statistically associated** - a dependence that should be accounted for in demand modeling;
* **Temperature and humidity are the strongest continuous predictors** of rental demand.

---

## 10. Technologies Used
* Python
* Pandas
* NumPy
* Seaborn
* Matplotlib
* SciPy
* Statsmodels
* Itertools
* Jupyter Notebook

---

## 11. How to Run the Project

1. Clone the repository

```bash
git clone https://github.com/yourusername/yulu-bike-demand-analysis.git
```

2. Install dependencies

```bash
pip install pandas numpy matplotlib seaborn scipy statsmodels jupyter
```

3. Run the notebook

```bash
jupyter notebook
```

Open:

```
yulu_bike_demand_analysis.ipynb
```

---

## 12. Future Improvements

* Build **predictive machine learning models** for demand forecasting, leveraging the identified demand drivers as features.
