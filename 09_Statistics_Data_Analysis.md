# Statistics & Data Analysis Concepts

## Table of Contents
1. [Descriptive Statistics](#descriptive-statistics)
2. [Probability & Distributions](#probability--distributions)
3. [Hypothesis Testing](#hypothesis-testing)
4. [Correlation & Regression](#correlation--regression)
5. [Sampling & Experimental Design](#sampling--experimental-design)
6. [Data Quality & Preprocessing](#data-quality--preprocessing)
7. [Business Metrics & KPIs](#business-metrics--kpis)
8. [A/B Testing](#ab-testing)
9. [Time Series Concepts](#time-series-concepts)
10. [Common Interview Questions](#common-interview-questions)

---

## Descriptive Statistics

### Q1: What are the measures of central tendency and when to use each?
**Answer:**

**Mean (Average):**
- Sum of all values divided by count
- Best for: Symmetric distributions without outliers
- Sensitive to outliers
- Formula: x̄ = Σx / n

**Median:**
- Middle value when sorted
- Best for: Skewed distributions, data with outliers
- Robust to outliers
- Formula: For odd n: value at (n+1)/2; For even n: average of n/2 and (n/2)+1

**Mode:**
- Most frequently occurring value
- Best for: Categorical data, multimodal distributions
- Can have multiple modes or no mode

**Comparison:**

| Distribution | Best Measure | Example |
|--------------|-------------|---------|
| Symmetric, no outliers | Mean | Heights of adults |
| Skewed right | Median | Income distribution |
| Skewed left | Median | Age at retirement |
| Categorical | Mode | Favorite color |
| Bimodal | Both modes | Test scores (two groups) |

**When to use which:**
- **Mean:** When data is normally distributed and you need to use in further calculations
- **Median:** When data has outliers or is skewed; better represents "typical" value
- **Mode:** For categorical data or when identifying most common value

---

### Q2: What are measures of dispersion and why are they important?
**Answer:**

**Measures of Dispersion** describe how spread out the data is.

**Range:**
- Max - Min
- Simple but sensitive to outliers

**Variance:**
- Average of squared deviations from mean
- Population: σ² = Σ(x - μ)² / N
- Sample: s² = Σ(x - x̄)² / (n - 1)
- Units are squared (hard to interpret)

**Standard Deviation:**
- Square root of variance
- Same units as original data
- Population: σ = √σ²
- Sample: s = √s²

**Interquartile Range (IQR):**
- Q3 - Q1 (75th percentile - 25th percentile)
- Robust to outliers
- Used to identify outliers: values < Q1 - 1.5×IQR or > Q3 + 1.5×IQR

**Coefficient of Variation (CV):**
- CV = (Standard Deviation / Mean) × 100%
- Relative measure of dispersion
- Allows comparison across different scales

**Why Important:**
- Two datasets can have same mean but very different spreads
- Risk assessment (higher dispersion = higher risk)
- Quality control (tighter dispersion = better process)
- Sample size determination

---

### Q3: Explain percentiles, quartiles, and box plots
**Answer:**

**Percentiles:**
- Pth percentile: Value below which P% of data falls
- P50 = Median
- P25 = First Quartile (Q1)
- P75 = Third Quartile (Q3)
- P90, P95, P99: Used for SLA metrics, outlier detection

**Quartiles:**
- Q1 (25th percentile): Lower quartile
- Q2 (50th percentile): Median
- Q3 (75th percentile): Upper quartile
- IQR = Q3 - Q1

**Box Plot Components:**
```
    │     ← Max (or Q3 + 1.5×IQR)
    ├─
    │     ← Q3 (75th percentile)
   ┌┴┐    ← Median line inside box
   │ │    ← Box = IQR (Q1 to Q3)
   └┬┘
    │     ← Q1 (25th percentile)
    ├─
    │     ← Min (or Q1 - 1.5×IQR)
    ○     ← Outliers
```

**Five-Number Summary:**
Min, Q1, Median, Q3, Max

---

### Q4: What is skewness and kurtosis?
**Answer:**

**Skewness:** Measures asymmetry of distribution

**Positive Skew (Right-skewed):**
- Tail extends to the right
- Mean > Median > Mode
- Example: Income, house prices

**Negative Skew (Left-skewed):**
- Tail extends to the left
- Mean < Median < Mode
- Example: Age at retirement, exam scores (easy test)

**Symmetric:**
- Mean = Median = Mode
- Example: Heights, IQ scores

**Kurtosis:** Measures "tailedness" of distribution

**Leptokurtic (Kurtosis > 3):**
- Sharper peak, heavier tails
- More extreme values
- Example: Financial returns

**Platykurtic (Kurtosis < 3):**
- Flatter peak, lighter tails
- Fewer extreme values
- Example: Uniform distribution

**Mesokurtic (Kurtosis = 3):**
- Normal distribution

---

## Probability & Distributions

### Q5: What is the difference between population and sample?
**Answer:**

| Aspect | Population | Sample |
|--------|-----------|--------|
| Definition | Entire group of interest | Subset of population |
| Parameters | μ (mean), σ (SD), N (size) | x̄ (mean), s (SD), n (size) |
| Greek letters | μ, σ, σ² | x̄, s, s² |
| Goal | Usually unknown | Estimate population parameters |
| Census | Complete enumeration | Statistical inference |

**Why Sample?**
- Cost: Cheaper than census
- Time: Faster results
- Practicality: Population may be inaccessible
- Destructive testing: Can't test all items

---

### Q6: Explain the Normal Distribution and its properties
**Answer:**

**Normal Distribution (Gaussian):**
- Bell-shaped, symmetric curve
- Defined by mean (μ) and standard deviation (σ)
- Notation: X ~ N(μ, σ²)

**Properties:**
1. Symmetric about the mean
2. Mean = Median = Mode
3. Total area under curve = 1 (100%)
4. Follows Empirical Rule (68-95-99.7):
   - 68% within μ ± σ
   - 95% within μ ± 2σ
   - 99.7% within μ ± 3σ

**Standard Normal Distribution:**
- Z ~ N(0, 1)
- Z-score: Z = (X - μ) / σ

**Z-Score Interpretation:**
- Z = 0: At the mean
- Z = 1: 1 SD above mean
- Z = -2: 2 SD below mean
- |Z| > 3: Potential outlier

**Central Limit Theorem:**
- Sample means approach normal distribution as sample size increases (n > 30)
- Regardless of population distribution
- Foundation for many statistical tests

---

### Q7: What are Type I and Type II errors?
**Answer:**

**Hypothesis Testing Framework:**
- H₀ (Null Hypothesis): Default assumption
- H₁ (Alternative Hypothesis): What we want to prove

**Type I Error (False Positive):**
- Reject H₀ when it's actually true
- Probability = α (significance level)
- Example: Concluding a drug works when it doesn't

**Type II Error (False Negative):**
- Fail to reject H₀ when it's actually false
- Probability = β
- Power = 1 - β (probability of correctly rejecting H₀)
- Example: Missing that a drug actually works

**Analogy (Criminal Trial):**
- H₀: Defendant is innocent
- Type I: Convict innocent person
- Type II: Acquit guilty person

**Trade-off:**
- Decreasing α increases β (and vice versa)
- Increasing sample size reduces both

| Decision | H₀ True | H₀ False |
|----------|---------|----------|
| Reject H₀ | Type I Error (α) | Correct (Power) |
| Fail to Reject | Correct | Type II Error (β) |

---

## Hypothesis Testing

### Q8: Explain the p-value and its interpretation
**Answer:**

**P-value:**
- Probability of obtaining results at least as extreme as observed, assuming H₀ is true
- NOT the probability that H₀ is true

**Interpretation:**
- p < 0.05 (typical threshold): Reject H₀ → Statistically significant
- p ≥ 0.05: Fail to reject H₀ → Not statistically significant

**Common Misconceptions:**
1. p-value is NOT P(H₀ is true | data)
2. p-value does NOT measure effect size
3. p-value does NOT measure practical significance
4. p = 0.049 vs p = 0.051 are practically the same

**Example:**
- Testing if new website increases conversion
- H₀: Conversion rate is same (p_new = p_old)
- H₁: Conversion rate is higher (p_new > p_old)
- p = 0.03 → Reject H₀ → New website significantly increases conversion

---

### Q9: What is the difference between one-tailed and two-tailed tests?
**Answer:**

**Two-Tailed Test:**
- H₁: μ ≠ μ₀ (difference in either direction)
- Splits α between both tails
- Used when direction is not predicted
- More conservative

**One-Tailed Test:**
- H₁: μ > μ₀ or μ < μ₀ (specific direction)
- Puts all α in one tail
- Used when direction is predicted
- More powerful for detecting effect in predicted direction

**Example:**
- Two-tailed: "Does the new drug change blood pressure?"
- One-tailed: "Does the new drug LOWER blood pressure?"

**Important:** Choose before seeing data! Choosing after is p-hacking.

---

### Q10: What are confidence intervals?
**Answer:**

**Confidence Interval (CI):**
- Range of values likely to contain the true population parameter
- 95% CI: If we repeated sampling 100 times, 95 intervals would contain true parameter

**Formula for Mean CI:**
```
CI = x̄ ± (t × s/√n)
```
Where t is critical value from t-distribution

**Interpretation:**
- "We are 95% confident the true mean is between [lower, upper]"
- NOT "There is 95% probability the mean is in this interval"

**Factors affecting width:**
- Confidence level: Higher = wider
- Sample size: Larger = narrower
- Variability: Higher = wider

**Example:**
- Average customer satisfaction: 4.2
- 95% CI: [3.8, 4.6]
- Interpretation: We're 95% confident true average satisfaction is between 3.8 and 4.6

---

## Correlation & Regression

### Q11: What is correlation and how do you interpret it?
**Answer:**

**Correlation Coefficient (r):**
- Measures linear relationship between two variables
- Range: -1 to +1

**Interpretation:**
- r = +1: Perfect positive linear relationship
- r = -1: Perfect negative linear relationship
- r = 0: No linear relationship
- |r| > 0.7: Strong
- |r| 0.3-0.7: Moderate
- |r| < 0.3: Weak

**Pearson's r:**
- Measures linear correlation
- Sensitive to outliers
- Formula: r = Cov(X,Y) / (σₓ × σᵧ)

**Spearman's ρ:**
- Rank-based correlation
- Monotonic relationships (not just linear)
- Robust to outliers

**Important Notes:**
1. Correlation ≠ Causation
2. Correlation only measures linear relationships
3. Outliers can dramatically affect r
4. r² = Coefficient of Determination (% variance explained)

**Example:**
- r = 0.85 between study hours and exam scores
- Interpretation: Strong positive linear relationship
- r² = 0.72 → 72% of exam score variance explained by study hours

---

### Q12: What is linear regression and how do you interpret it?
**Answer:**

**Simple Linear Regression:**
```
Y = β₀ + β₁X + ε
```
- Y: Dependent variable (outcome)
- X: Independent variable (predictor)
- β₀: Intercept (Y when X=0)
- β₁: Slope (change in Y per unit change in X)
- ε: Error term

**Interpretation:**
- β₁ = 2.5: For every 1-unit increase in X, Y increases by 2.5 units
- β₀ = 10: When X=0, Y is expected to be 10

**R-squared (R²):**
- Proportion of variance in Y explained by X
- Range: 0 to 1 (0% to 100%)
- Higher = better fit

**Adjusted R²:**
- Penalizes for adding unnecessary predictors
- Better for comparing models with different numbers of predictors

**Assumptions:**
1. Linearity: Relationship is linear
2. Independence: Observations are independent
3. Homoscedasticity: Constant variance of errors
4. Normality: Errors are normally distributed
5. No multicollinearity (for multiple regression)

---

### Q13: What is multicollinearity and how do you detect it?
**Answer:**

**Multicollinearity:**
- High correlation between independent variables in regression
- Makes it hard to determine individual variable effects

**Detection Methods:**
1. **Correlation Matrix:** Correlations > 0.8 between predictors
2. **Variance Inflation Factor (VIF):**
   - VIF = 1/(1 - R²)
   - VIF > 5: Moderate multicollinearity
   - VIF > 10: Severe multicollinearity
3. **Tolerance:** 1/VIF; Tolerance < 0.1 indicates problem

**Solutions:**
1. Remove one of correlated variables
2. Combine variables (create index)
3. Use Principal Component Analysis (PCA)
4. Ridge regression
5. Collect more data

---

## Sampling & Experimental Design

### Q14: What are the different sampling methods?
**Answer:**

**Probability Sampling (Random):**

**1. Simple Random Sampling:**
- Every member has equal chance
- Random number generator or lottery
- Best for: Homogeneous populations

**2. Stratified Sampling:**
- Divide population into strata (groups)
- Random sample from each stratum
- Best for: Heterogeneous populations
- Ensures representation of all groups

**3. Systematic Sampling:**
- Select every kth member
- k = N/n (population size / sample size)
- Best for: Ordered populations

**4. Cluster Sampling:**
- Divide into clusters (often geographic)
- Randomly select entire clusters
- Best for: Large, dispersed populations
- Less precise but more practical

**Non-Probability Sampling:**

**1. Convenience Sampling:**
- Use readily available subjects
- Quick but biased

**2. Quota Sampling:**
- Like stratified but non-random
- Fill quotas for each group

**3. Snowball Sampling:**
- Referrals from existing subjects
- Best for: Hard-to-reach populations

---

## Data Quality & Preprocessing

### Q15: How do you handle missing data?
**Answer:**

**Types of Missing Data:**

**1. MCAR (Missing Completely at Random):**
- Missingness unrelated to any data
- Example: Random survey non-response
- Least problematic

**2. MAR (Missing at Random):**
- Missingness related to observed data
- Example: Older people less likely to report income
- Can be addressed with proper methods

**3. MNAR (Missing Not at Random):**
- Missingness related to missing value itself
- Example: High-income people hide income
- Most problematic, requires domain knowledge

**Handling Methods:**

**Deletion:**
- Listwise: Remove entire row (if < 5% missing)
- Pairwise: Use available pairs for each calculation
- Risk: Reduces sample size, may introduce bias

**Imputation:**
- Mean/Median/Mode: Simple, reduces variance
- Forward/Backward fill: For time series
- KNN Imputation: Use similar rows
- Regression Imputation: Predict missing values
- Multiple Imputation: Create multiple datasets

**Advanced:**
- MICE (Multivariate Imputation by Chained Equations)
- Deep learning methods
- Domain-specific imputation

---

### Q16: How do you detect and handle outliers?
**Answer:**

**Detection Methods:**

**1. Statistical Methods:**
- Z-score: |Z| > 3
- IQR Method: < Q1 - 1.5×IQR or > Q3 + 1.5×IQR
- Grubbs' Test

**2. Visual Methods:**
- Box plots
- Scatter plots
- Histograms

**3. Distance-Based:**
- Mahalanobis distance
- DBSCAN clustering

**Handling Outliers:**

**1. Keep Them:**
- If they represent true extreme values
- If sample size is large
- If using robust methods

**2. Remove Them:**
- If data entry errors
- If they distort analysis significantly
- Document removal

**3. Transform:**
- Log transformation
- Square root transformation
- Winsorization (cap at percentiles)

**4. Use Robust Methods:**
- Median instead of mean
- IQR instead of standard deviation
- Robust regression

---

## Business Metrics & KPIs

### Q17: What are the most important business metrics?
**Answer:**

**Financial Metrics:**
- Revenue, Profit, Margin (Gross, Operating, Net)
- ROI, ROA, ROE
- EBITDA, EBIT
- Cash Flow, Burn Rate
- CAC (Customer Acquisition Cost)
- LTV (Lifetime Value)

**Sales Metrics:**
- Conversion Rate
- Average Order Value (AOV)
- Sales Growth
- Pipeline Value
- Win Rate
- Sales Cycle Length

**Marketing Metrics:**
- CTR (Click-Through Rate)
- CPC (Cost Per Click)
- CPA (Cost Per Acquisition)
- ROAS (Return on Ad Spend)
- Impressions, Reach
- Engagement Rate

**Customer Metrics:**
- NPS (Net Promoter Score)
- CSAT (Customer Satisfaction)
- Churn Rate
- Retention Rate
- CLV (Customer Lifetime Value)
- CAC Payback Period

**Product Metrics:**
- DAU/MAU (Daily/Monthly Active Users)
- Session Duration
- Feature Adoption Rate
- Time to Value
- Net Revenue Retention

**Operational Metrics:**
- Inventory Turnover
- Order Fulfillment Time
- Defect Rate
- Employee Satisfaction
- Capacity Utilization

---

### Q18: What is Cohort Analysis?
**Answer:**

**Cohort Analysis:** Groups users/customers by shared characteristic (usually signup date) and tracks their behavior over time.

**Types:**
1. **Time-Based Cohorts:** Group by signup/acquisition period
2. **Behavioral Cohorts:** Group by action taken
3. **Size-Based Cohorts:** Group by purchase amount, company size

**Key Metrics:**
- Retention Rate: % of cohort still active
- Churn Rate: % of cohort lost
- Revenue per Cohort: Total revenue from cohort
- Cohort LTV: Lifetime value of cohort

**Why Important:**
- Identifies trends in customer behavior
- Measures impact of product changes
- Compares performance across time periods
- Predicts future revenue

**Example:**
- Cohort: Customers who signed up in January 2024
- Month 1 retention: 80%
- Month 3 retention: 60%
- Month 6 retention: 45%
- Compare to December 2023 cohort to see if changes improved retention

---

## A/B Testing

### Q19: How do you design and analyze an A/B test?
**Answer:**

**Design Phase:**

**1. Define Hypothesis:**
- H₀: No difference between variants
- H₁: Variant B performs better than A

**2. Choose Metric:**
- Primary: Conversion rate, revenue per user
- Secondary: Engagement, retention
- Guardrail: Page load time, error rate

**3. Calculate Sample Size:**
- Power (1-β): Typically 80%
- Significance (α): Typically 5%
- Minimum Detectable Effect (MDE)
- Baseline conversion rate
- Formula or online calculator

**4. Randomization:**
- Random assignment to A or B
- Ensure balance in key demographics
- Avoid contamination (users seeing both)

**Analysis Phase:**

**1. Check for Issues:**
- Sample ratio mismatch
- Novelty effect
- Seasonality
- Instrumentation errors

**2. Statistical Test:**
- Two-proportion z-test (for conversion rates)
- t-test (for continuous metrics)
- Mann-Whitney U (for non-normal data)

**3. Calculate:**
- P-value
- Confidence interval
- Effect size
- Practical significance

**4. Make Decision:**
- p < 0.05 AND practically significant → Implement
- p < 0.05 but not practically significant → Consider cost
- p ≥ 0.05 → Don't implement (or run longer)

**Common Pitfalls:**
- Peeking (checking results before planned end)
- Multiple testing (testing many variants)
- Selection bias
- Insufficient sample size
- Running too short

---

## Time Series Concepts

### Q20: What are the components of a time series?
**Answer:**

**Time Series Decomposition:**
```
Y(t) = T(t) + S(t) + C(t) + I(t)
```

**1. Trend (T):**
- Long-term direction (up, down, flat)
- Example: Growing sales over 5 years

**2. Seasonality (S):**
- Regular, repeating patterns
- Fixed period (daily, weekly, monthly, yearly)
- Example: Higher retail sales in December

**3. Cyclical (C):**
- Long-term oscillations (not fixed period)
- Usually economic cycles
- Example: Business cycles (3-10 years)

**4. Irregular/Random (I):**
- Unpredictable fluctuations
- Noise
- Example: One-time events, random variation

**Additive vs Multiplicative:**
- **Additive:** Y = T + S + C + I (constant seasonality)
- **Multiplicative:** Y = T × S × C × I (seasonality grows with trend)

---

### Q21: What are common time series forecasting methods?
**Answer:**

**Simple Methods:**
- **Naive:** Next value = Last observed value
- **Simple Average:** Next value = Average of all history
- **Moving Average:** Next value = Average of last N periods
- **Exponential Smoothing:** Weighted average with decreasing weights

**Advanced Methods:**
- **ARIMA:** Autoregressive Integrated Moving Average
- **SARIMA:** Seasonal ARIMA
- **Prophet:** Facebook's forecasting tool (trend + seasonality + holidays)
- **LSTM/Neural Networks:** Deep learning approaches

**Evaluation Metrics:**
- MAE (Mean Absolute Error)
- RMSE (Root Mean Squared Error)
- MAPE (Mean Absolute Percentage Error)
- MASE (Mean Absolute Scaled Error)

**Cross-Validation:**
- Time series split (respect temporal order)
- Walk-forward validation

---

## Common Interview Questions

### Q22: What is the difference between correlation and causation?
**Answer:**

**Correlation:** Two variables move together
**Causation:** One variable directly affects another

**Examples of Correlation ≠ Causation:**
- Ice cream sales and drowning deaths (both correlated with temperature)
- Nicholas Cage movies and swimming pool drownings
- Pirate population and global warming

**Establishing Causation:**
1. **Temporal precedence:** Cause before effect
2. **Correlation:** Variables are related
3. **No confounding:** Rule out alternative explanations
4. **Mechanism:** Plausible causal pathway
5. **Experiment:** Randomized controlled trial (RCT)

**Methods to Infer Causation:**
- Randomized experiments (A/B tests)
- Natural experiments
- Instrumental variables
- Regression discontinuity
- Difference-in-differences
- Granger causality (time series)

---

### Q23: What is the difference between supervised and unsupervised learning?
**Answer:**

| Aspect | Supervised Learning | Unsupervised Learning |
|--------|--------------------|----------------------|
| Data | Labeled (input + output) | Unlabeled (input only) |
| Goal | Predict output | Find patterns/structure |
| Examples | Regression, Classification | Clustering, Dimensionality Reduction |
| Evaluation | Compare predictions to labels | Harder to evaluate |
| Algorithms | Linear Regression, Random Forest, SVM | K-Means, PCA, DBSCAN |

**Supervised Examples:**
- Predict house prices (regression)
- Classify emails as spam/not spam (classification)
- Predict customer churn (classification)

**Unsupervised Examples:**
- Customer segmentation (clustering)
- Anomaly detection
- Market basket analysis (association rules)
- Topic modeling

**Semi-Supervised:**
- Mix of labeled and unlabeled data
- Common when labeling is expensive

---

### Q24: What is overfitting and how do you prevent it?
**Answer:**

**Overfitting:**
- Model learns training data too well
- Performs well on training data, poorly on new data
- Captures noise as if it were signal

**Signs:**
- Large gap between training and validation performance
- Complex model with many parameters
- High variance

**Prevention Methods:**

**1. Cross-Validation:**
- K-fold cross-validation
- Stratified sampling

**2. Regularization:**
- L1 (Lasso): Adds penalty for absolute values
- L2 (Ridge): Adds penalty for squared values
- Elastic Net: Combination of L1 and L2

**3. Simpler Models:**
- Fewer parameters
- Feature selection
- Dimensionality reduction

**4. More Data:**
- Collect more training examples
- Data augmentation

**5. Early Stopping:**
- Stop training when validation error stops improving

**6. Ensemble Methods:**
- Bagging (Random Forest)
- Boosting (Gradient Boosting)
- Reduces variance

**7. Dropout (Neural Networks):**
- Randomly drop neurons during training

---

### Q25: What is feature engineering?
**Answer:**

**Feature Engineering:** Creating new features from raw data to improve model performance.

**Types:**

**1. Transformation:**
- Log transformation (skewed data)
- Scaling/Normalization
- Binning (continuous to categorical)

**2. Creation:**
- Polynomial features (x², x³)
- Interaction terms (x₁ × x₂)
- Ratios (x₁ / x₂)
- Date features (day of week, month, is_weekend)

**3. Encoding:**
- One-hot encoding (categorical)
- Label encoding (ordinal)
- Target encoding (high cardinality)

**4. Aggregation:**
- Rolling averages
- Cumulative sums
- Group statistics

**5. Text Features:**
- TF-IDF
- Word embeddings
- N-grams

**6. Domain-Specific:**
- Business logic features
- Industry-specific transformations

**Importance:**
- Often more impactful than algorithm choice
- Domain knowledge is crucial
- Iterative process

---

### Q26: What is the bias-variance tradeoff?
**Answer:**

**Total Error = Bias² + Variance + Irreducible Error**

**Bias:**
- Error from oversimplified assumptions
- High bias = underfitting
- Model misses relevant relations
- Example: Linear model for non-linear data

**Variance:**
- Error from sensitivity to small fluctuations
- High variance = overfitting
- Model learns noise
- Example: Complex decision tree

**Tradeoff:**
- Increasing model complexity → Decreases bias, increases variance
- Decreasing model complexity → Increases bias, decreases variance
- Goal: Find sweet spot minimizing total error

**Analogy:**
- High bias: Always predicting average (misses patterns)
- High variance: Memorizing training data (misses general patterns)
- Ideal: Learning true underlying pattern

---

### Q27: What are the different types of data?
**Answer:**

**1. Nominal (Categorical):**
- Categories with no order
- Example: Colors, gender, country
- Analysis: Mode, frequency, chi-square

**2. Ordinal:**
- Categories with order
- Example: Ratings (1-5 stars), education level
- Analysis: Median, percentiles, rank correlation

**3. Interval:**
- Numeric, equal intervals, no true zero
- Example: Temperature (Celsius), IQ scores
- Analysis: Mean, standard deviation, correlation

**4. Ratio:**
- Numeric, equal intervals, true zero
- Example: Height, weight, income, age
- Analysis: All statistical operations, ratios meaningful

**Additional Types:**
- **Binary:** Yes/No, True/False
- **Discrete:** Countable (number of children)
- **Continuous:** Measurable (height, weight)
- **Text:** Unstructured
- **Time Series:** Temporal
- **Geospatial:** Location-based

---

### Q28: What is dimensionality reduction?
**Answer:**

**Dimensionality Reduction:** Reducing number of features while preserving information.

**Why:**
- Curse of dimensionality
- Reduce overfitting
- Improve computation speed
- Visualization (2D/3D)
- Remove multicollinearity

**Methods:**

**1. Feature Selection:**
- Filter methods (correlation, chi-square)
- Wrapper methods (recursive feature elimination)
- Embedded methods (Lasso regularization)

**2. Feature Extraction:**
- **PCA (Principal Component Analysis):** Linear, unsupervised
- **LDA (Linear Discriminant Analysis):** Linear, supervised
- **t-SNE:** Non-linear, visualization
- **UMAP:** Non-linear, faster than t-SNE
- **Autoencoders:** Neural network approach

**PCA Process:**
1. Standardize data
2. Compute covariance matrix
3. Find eigenvectors and eigenvalues
4. Sort by eigenvalues (explained variance)
5. Select top k components
6. Transform data

---

### Q29: What is the difference between classification and regression?
**Answer:**

| Aspect | Classification | Regression |
|--------|---------------|------------|
| Output | Discrete categories | Continuous values |
| Examples | Spam/Not spam, Churn/Not churn | Price, Temperature, Sales |
| Algorithms | Logistic Regression, SVM, Decision Trees, Random Forest | Linear Regression, Polynomial Regression |
| Evaluation | Accuracy, Precision, Recall, F1, AUC-ROC | MAE, RMSE, R² |
| Output Layer | Softmax/Sigmoid | Linear |

**Classification Types:**
- Binary: Two classes
- Multi-class: More than two classes
- Multi-label: Multiple labels per instance

**Regression Types:**
- Simple: One predictor
- Multiple: Multiple predictors
- Polynomial: Non-linear relationships

---

### Q30: What is cross-validation and why is it important?
**Answer:**

**Cross-Validation:** Technique to assess model performance on unseen data.

**K-Fold Cross-Validation:**
1. Split data into K equal parts (folds)
2. Train on K-1 folds, test on remaining fold
3. Repeat K times (each fold used as test once)
4. Average performance across all folds

**Common K values:**
- K=5 or K=10 (standard)
- K=n (Leave-One-Out)

**Stratified K-Fold:**
- Preserves class distribution in each fold
- Important for imbalanced datasets

**Time Series Split:**
- Respects temporal order
- Training set always before test set

**Why Important:**
- More reliable than single train-test split
- Reduces variance in performance estimate
- Better use of data (each point used for training and testing)
- Helps detect overfitting

**Other Methods:**
- Holdout validation (simple split)
- Bootstrap sampling
- Nested cross-validation (for hyperparameter tuning)
