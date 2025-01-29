# Hypothesis Testing in Healthcare: Drug Safety

## Table of Contents
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Data Preparation](#data-preparation)
- [Hypothesis Testing](#hypothesis-testing)
  - [Z-Test for Proportions](#z-test-for-proportions)
  - [Chi-Square Test](#chi-square-test)
  - [Age Analysis](#age-analysis)
  - [Mann-Whitney U Test](#mann-whitney-u-test)
- [Findings](#findings)
- [Limitations](#limitations)

### Project Overview
---
In this project, I performed hypothesis testing on a dataset from a randomized controlled drug trial conducted by GlobalXYZ to assess the safety of a drug by analyzing adverse effects experienced by individuals. The goal was to explore whether the drug treatment caused significant adverse reactions compared to the placebo group.

### Data Source
- **Dataset**: drug_safety.csv
- **Data Provided By**: Hbiostat, Vanderbilt University Department of Biostatistics
- **Columns**:
  - **sex**: Gender of the individual
  - **age**: Age of the individual
  - **week**: Week of the drug testing
  - **trx**: Treatment (Drug) and control (Placebo) groups
  - **wbc**: White blood cell count
  - **rbc**: Red blood cell count
  - **adverse_effects**: Presence of at least one adverse effect
  - **num_effects**: Number of adverse effects experienced by an individual

### Tools & Libraries

- **Python**: Data Analysis and Hypothesis Testing
- **Libraries**:
  - pandas for data handling
  - seaborn, matplotlib for visualization
  - statsmodels for statistical testing
  - pingouin for advanced statistical analysis

### Data Preparation
---
```python
import numpy as np
import pandas as pd
from statsmodels.stats.proportion import proportions_ztest
import pingouin
import seaborn as sns
import matplotlib.pyplot as plt

# Load the dataset
drug_safety = pd.read_csv("drug_safety.csv")
```

### Hypothesis Testing
#### Z-Test for Proportions
We tested if there was a significant difference in the proportion of individuals experiencing adverse effects between the drug and placebo groups.

```python
p_hat = drug_safety.groupby('trx')['adverse_effects'].value_counts()

n_yes = np.array([1024, 512])
n_rows = np.array([9703 + 1024 , 4864 + 512])

z_score, p_value = proportions_ztest(n_yes, n_rows)
two_sample_p_value =  p_value
print(two_sample_p_value)  # Output: 0.9639333330262475
```
#### Chi-Square Test
We explored whether the number of adverse effects (num_effects) was independent of the treatment group (Drug vs Placebo).
```python
expected, observed, stats = pingouin.chi2_independence(data=drug_safety, x="num_effects", y="trx")
num_effects_p_value = stats[stats['test'] == 'pearson']
num_effects_p_value = num_effects_p_value['pval'].values
print(num_effects_p_value)  # Output: [0.61501233]
```
#### Age Analysis
We analyzed the age distribution of individuals in the drug and placebo groups.
```python
xbar = drug_safety.groupby('trx')['age'].mean()
s = drug_safety.groupby('trx')['age'].std()
n = drug_safety.groupby('trx')['age'].count()

sns.histplot(data=drug_safety, x='age', hue='trx')
plt.show()
```
![drug_placebo (2)](https://github.com/user-attachments/assets/0ee7e118-e921-4406-8e40-efce49f53364)


#### Mann-Whitney U Test
We tested if the age distribution was significantly different between the drug and placebo groups.
```python
age_vs_trx = drug_safety[['age', 'trx']]
age_vs_trx_wide = age_vs_trx.pivot(columns='trx', values='age')

age_group_effects_p_value = pingouin.mwu(x=age_vs_trx_wide['Drug'], y=age_vs_trx_wide['Placebo'], alternative='two-sided')
age_group_effects_p_value = age_group_effects_p_value['p-val'].values
print(age_group_effects_p_value)  # Output: [0.25696267]
```
### Findings
- The Z-Test for Proportions revealed that there was no significant difference in the proportion of adverse effects between the drug and placebo groups (p-value = 0.96).
- The Chi-Square Test showed no significant relationship between the number of adverse effects and treatment group (p-value = 0.62).
- The Age Distribution Analysis indicated that age did not significantly differ between the drug and placebo groups.
- The Mann-Whitney U Test suggested no significant difference in age between the groups (p-value = 0.26).
### Limitations
- The dataset did not provide species or other contextual information, making it difficult to contextualize findings.
- The results may be affected by the data quality, sample size, and any missing data points in the dataset.
- The analysis assumes that the groups are randomly assigned and that external factors do not influence the results.




