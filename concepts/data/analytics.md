# Data Analytics Techniques

**Scope**: Analytical methods, statistical techniques, and data analysis patterns for extracting insights from data.

**Purpose**: Use this when performing data analysis, building analytical models, or designing analytical workflows. For analytics engineering (transformation layer, semantic layer), see [Analytics Engineering](analytics.md). For pipeline patterns, see [Data Engineering](engineering.md).

## Table of Contents

- [1. Exploratory Data Analysis (EDA)](#1-exploratory-data-analysis-eda)
- [2. Statistical Analysis](#2-statistical-analysis)
- [3. Time Series Analysis](#3-time-series-analysis)
- [4. Segmentation & Clustering](#4-segmentation--clustering)
- [5. Correlation & Causation](#5-correlation--causation)
- [6. Hypothesis Testing](#6-hypothesis-testing)
- [7. A/B Testing & Experimentation](#7-ab-testing--experimentation)
- [8. Forecasting & Predictive Analytics](#8-forecasting--predictive-analytics)
- [9. Anomaly Detection](#9-anomaly-detection)
- [10. Root Cause Analysis](#10-root-cause-analysis)

---

## 1. Exploratory Data Analysis (EDA)

### Purpose

- Understand data structure, distributions, relationships
- Identify patterns, outliers, missing data
- Form hypotheses for further analysis

### Key Steps

- **Data profiling**: Shape, types, nulls, unique values
- **Summary statistics**: Mean, median, mode, std dev, quartiles
- **Distribution analysis**: Histograms, box plots, density plots
- **Relationship exploration**: Scatter plots, correlation matrices
- **Outlier detection**: Z-scores, IQR method, visual inspection

### Common Patterns

- **Univariate analysis**: Single variable distributions
- **Bivariate analysis**: Relationships between two variables
- **Multivariate analysis**: Relationships among multiple variables

---

## 2. Statistical Analysis

### Descriptive Statistics

- **Central tendency**: Mean, median, mode
- **Dispersion**: Range, variance, standard deviation, IQR
- **Shape**: Skewness, kurtosis
- **Percentiles**: Quartiles, deciles, percentiles

### Inferential Statistics

- **Sampling**: Random sampling, stratified sampling, cluster sampling
- **Confidence intervals**: Estimate population parameters from samples
- **Statistical significance**: p-values, significance levels (α)
- **Effect size**: Practical significance beyond statistical significance

### Common Distributions

- **Normal distribution**: Bell curve, many natural phenomena
- **Binomial distribution**: Success/failure outcomes
- **Poisson distribution**: Count of events in fixed interval
- **Exponential distribution**: Time between events

---

## 3. Time Series Analysis

### Components

- **Trend**: Long-term direction (increasing, decreasing, stable)
- **Seasonality**: Regular patterns (daily, weekly, monthly, yearly)
- **Cyclical**: Irregular cycles (business cycles, economic cycles)
- **Noise**: Random variation

### Analysis Techniques

- **Decomposition**: Separate trend, seasonality, and residuals
- **Moving averages**: Smooth out short-term fluctuations
- **Autocorrelation**: Relationship between values at different time lags
- **Stationarity**: Constant mean and variance over time

### Common Patterns

- **Trend analysis**: Identify long-term patterns
- **Seasonal adjustment**: Remove seasonal effects
- **Growth rates**: YoY, MoM, WoW comparisons
- **Cumulative metrics**: Running totals, year-to-date

---

## 4. Segmentation & Clustering

### Segmentation

- **Demographic**: Age, gender, location, income
- **Behavioral**: Purchase patterns, engagement, usage
- **Psychographic**: Attitudes, values, lifestyle
- **RFM analysis**: Recency, Frequency, Monetary value

### Clustering Techniques

- **K-means**: Partition data into k clusters
- **Hierarchical**: Build tree of clusters
- **DBSCAN**: Density-based clustering
- **Silhouette score**: Measure cluster quality

### Use Cases

- **Customer segmentation**: Group similar customers
- **Product categorization**: Group similar products
- **Anomaly detection**: Identify outliers from clusters

---

## 5. Correlation & Causation

### Correlation

- **Pearson correlation**: Linear relationships (-1 to +1)
- **Spearman correlation**: Monotonic relationships (rank-based)
- **Correlation matrix**: Visualize relationships between variables
- **Cautions**: Correlation ≠ causation, spurious correlations

### Causation

- **Causal inference**: Establish cause-and-effect relationships
- **Confounding variables**: Hidden factors affecting both variables
- **Randomized experiments**: Gold standard for establishing causation
- **Observational studies**: Use techniques to infer causation

### Common Pitfalls

- **Spurious correlations**: Coincidental relationships
- **Reverse causation**: Cause and effect reversed
- **Omitted variable bias**: Missing confounding factors

---

## 6. Hypothesis Testing

### Framework

- **Null hypothesis (H₀)**: Default assumption (no effect, no difference)
- **Alternative hypothesis (H₁)**: What we're testing for
- **Test statistic**: Calculated from sample data
- **p-value**: Probability of observing results if H₀ is true
- **Significance level (α)**: Threshold for rejecting H₀ (typically 0.05)

### Common Tests

- **t-test**: Compare means (one-sample, two-sample, paired)
- **Chi-square test**: Test independence, goodness of fit
- **ANOVA**: Compare means across multiple groups
- **Mann-Whitney U**: Non-parametric alternative to t-test

### Decision Making

- **p < α**: Reject H₀ (statistically significant)
- **p ≥ α**: Fail to reject H₀ (not statistically significant)
- **Type I error**: False positive (reject H₀ when it's true)
- **Type II error**: False negative (fail to reject H₀ when it's false)

---

## 7. A/B Testing & Experimentation

### Experimental Design

- **Control group**: Baseline (no change)
- **Treatment group**: New variant (change being tested)
- **Randomization**: Random assignment to groups
- **Sample size**: Calculate based on effect size, power, significance level

### Key Metrics

- **Conversion rate**: Percentage completing desired action
- **Average order value**: Average transaction amount
- **Engagement metrics**: Time on site, clicks, views
- **Retention rate**: Percentage returning users

### Analysis

- **Statistical significance**: p-value < 0.05
- **Practical significance**: Effect size matters for business
- **Confidence intervals**: Range of likely true effect
- **Multiple comparisons**: Adjust for testing multiple variants

### Best Practices

- **One variable at a time**: Isolate the effect being tested
- **Sufficient sample size**: Ensure statistical power
- **Run duration**: Long enough to capture full effect
- **Guardrail metrics**: Monitor for unintended negative effects

---

## 8. Forecasting & Predictive Analytics

### Forecasting Methods

- **Time series forecasting**: ARIMA, exponential smoothing, Prophet
- **Regression**: Linear, polynomial, logistic regression
- **Machine learning**: Random forest, gradient boosting, neural networks
- **Ensemble methods**: Combine multiple models

### Evaluation Metrics

- **MAE (Mean Absolute Error)**: Average absolute difference
- **RMSE (Root Mean Squared Error)**: Penalizes large errors more
- **MAPE (Mean Absolute Percentage Error)**: Percentage-based error
- **R² (R-squared)**: Proportion of variance explained

### Best Practices

- **Train/validation/test split**: Separate data for training and evaluation
- **Cross-validation**: K-fold cross-validation for robust evaluation
- **Feature engineering**: Create relevant features for prediction
- **Model selection**: Compare multiple models, choose best performer

---

## 9. Anomaly Detection

### Types of Anomalies

- **Point anomalies**: Individual data points that are outliers
- **Contextual anomalies**: Normal in one context, abnormal in another
- **Collective anomalies**: Collection of points that are anomalous together

### Detection Methods

- **Statistical methods**: Z-scores, IQR method, Grubbs' test
- **Distance-based**: k-nearest neighbors, local outlier factor
- **Density-based**: DBSCAN, isolation forest
- **Time series**: Moving average, exponential smoothing with thresholds

### Use Cases

- **Fraud detection**: Unusual transaction patterns
- **System monitoring**: Performance anomalies, errors
- **Quality control**: Defective products, data quality issues
- **Security**: Intrusion detection, suspicious activity

---

## 10. Root Cause Analysis

### Framework

- **Problem definition**: Clearly define the issue
- **Data collection**: Gather relevant data
- **Hypothesis generation**: Brainstorm possible causes
- **Hypothesis testing**: Test each hypothesis with data
- **Root cause identification**: Identify underlying cause
- **Solution implementation**: Address root cause

### Techniques

- **5 Whys**: Ask "why" repeatedly to drill down
- **Fishbone diagram**: Categorize potential causes
- **Pareto analysis**: Focus on most significant causes (80/20 rule)
- **Correlation analysis**: Identify factors correlated with problem

### Best Practices

- **Data-driven**: Base conclusions on evidence, not assumptions
- **Multiple perspectives**: Include different viewpoints
- **Systematic approach**: Follow structured methodology
- **Actionable insights**: Identify solutions, not just problems

---

> **Note**: For analytics engineering (transformation layer, semantic layer, BI modeling), see [Analytics Engineering](analytics.md). For data pipeline patterns, see [Data Engineering](engineering.md). For data management practices, see [Data Management](management.md).

