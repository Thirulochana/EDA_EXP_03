**EXP 3 - Delhi Air Quality Analysis**

**Aim**


To compare air quality parameters in Delhi across different stations and analyze the relationship between pollutants (e.g., PM2.5 and NO₂) using scatter plots and correlation analysis.


**Procedure / Algorithm**

1)Load the dataset using pandas.

2)Preprocess the data:

3)Convert the date column (period.datetimeFrom.utc) to datetime format.

4)Drop missing or invalid values.

5)Pivot the dataset so each pollutant (parameter) becomes a separate column.

6)Plot scatter plot between PM2.5 and NO₂ to study their relationship.

7)Plot correlation heatmap between all pollutants to identify relationships.

8)Interpret the results — identify which pollutants are correlated and which stations are most polluted.


**Program**

**Name :** Thirulochana S 

**Reg No:** 212225040472 

```
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.preprocessing import StandardScaler, MinMaxScaler

# Load the dataset
df = pd.read_csv("C:/Users/acer/Downloads/del-sirifort-cpcb-2024-25.csv")

# Display basic information
print("Dataset Shape:", df.shape)

print("\nFirst 5 Rows:")
display(df.head())

print("\nDataset Information:")
df.info()

# --------------------------------------------------
# 1. Select required air-quality parameters
# --------------------------------------------------

pollutants = [
    "PM2.5 (µg/m³)",
    "PM10 (µg/m³)",
    "NO2 (µg/m³)",
    "Ozone (µg/m³)"
]

# Convert pollutant columns to numeric
for col in pollutants:
    df[col] = pd.to_numeric(df[col], errors="coerce")

# Remove missing values
df_clean = df[pollutants].dropna().copy()

print("\nRows after preprocessing:", len(df_clean))

# --------------------------------------------------
# 2. Univariate Distribution Analysis
# --------------------------------------------------

for col in pollutants:
    plt.figure(figsize=(8, 5))
    sns.histplot(df_clean[col], kde=True)
    plt.title(f"Distribution of {col}")
    plt.xlabel(col)
    plt.ylabel("Frequency")
    plt.tight_layout()
    plt.show()

# --------------------------------------------------
# 3. Measures of Central Tendency
# --------------------------------------------------

central_tendency = pd.DataFrame({
    "Mean": df_clean[pollutants].mean(),
    "Median": df_clean[pollutants].median(),
    "Mode": df_clean[pollutants].mode().iloc[0]
})

print("\nMeasures of Central Tendency:")
display(central_tendency.round(2))

# --------------------------------------------------
# 4. Measures of Spread
# --------------------------------------------------

spread = pd.DataFrame({
    "Minimum": df_clean[pollutants].min(),
    "Maximum": df_clean[pollutants].max(),
    "Range": df_clean[pollutants].max() - df_clean[pollutants].min(),
    "Variance": df_clean[pollutants].var(),
    "Standard Deviation": df_clean[pollutants].std(),
    "IQR": (
        df_clean[pollutants].quantile(0.75)
        - df_clean[pollutants].quantile(0.25)
    )
})

print("\nMeasures of Spread:")
display(spread.round(2))

# --------------------------------------------------
# 5. Boxplot for Outlier Detection
# --------------------------------------------------

for col in pollutants:
    plt.figure(figsize=(8, 4))
    sns.boxplot(x=df_clean[col])
    plt.title(f"Boxplot of {col}")
    plt.xlabel(col)
    plt.tight_layout()
    plt.show()

# --------------------------------------------------
# 6. Outlier Detection using IQR Method
# --------------------------------------------------

outlier_results = []

for col in pollutants:

    Q1 = df_clean[col].quantile(0.25)
    Q3 = df_clean[col].quantile(0.75)
    IQR = Q3 - Q1

    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR

    outliers = df_clean[
        (df_clean[col] < lower_bound) |
        (df_clean[col] > upper_bound)
    ]

    outlier_results.append({
        "Pollutant": col,
        "Q1": Q1,
        "Q3": Q3,
        "IQR": IQR,
        "Lower Bound": lower_bound,
        "Upper Bound": upper_bound,
        "Number of Outliers": len(outliers)
    })

outlier_df = pd.DataFrame(outlier_results)

print("\nOutlier Detection using IQR:")
display(outlier_df.round(2))

# --------------------------------------------------
# 7. Min-Max Scaling
# --------------------------------------------------

minmax_scaler = MinMaxScaler()

minmax_scaled = pd.DataFrame(
    minmax_scaler.fit_transform(df_clean[pollutants]),
    columns=pollutants
)

print("\nMin-Max Scaled Data:")
display(minmax_scaled.head().round(3))

# --------------------------------------------------
# 8. Standardization using Z-Score
# --------------------------------------------------

standard_scaler = StandardScaler()

standardized = pd.DataFrame(
    standard_scaler.fit_transform(df_clean[pollutants]),
    columns=pollutants
)

print("\nStandardized Data:")
display(standardized.head().round(3))

# --------------------------------------------------
# 9. Inequality Analysis using Gini Coefficient
# --------------------------------------------------

def gini_coefficient(values):

    values = values.sort_values().values

    n = len(values)

    if values.sum() == 0:
        return 0

    index = range(1, n + 1)

    gini = (
        (2 * sum(i * x for i, x in zip(index, values)))
        / (n * values.sum())
    ) - ((n + 1) / n)

    return gini


gini_values = {}

for col in pollutants:
    gini_values[col] = gini_coefficient(df_clean[col])

gini_df = pd.DataFrame(
    gini_values,
    index=["Gini Coefficient"]
).T

print("\nInequality Analysis:")
display(gini_df.round(3))

# --------------------------------------------------
# 10. Complete Statistical Summary
# --------------------------------------------------

print("\nComplete Statistical Summary:")
display(df_clean[pollutants].describe().round(2))
```


**Output**
<img width="1065" height="667" alt="Screenshot 2026-09-08 204936" src="https://github.com/user-attachments/assets/0e255a43-32ab-4414-9c70-41e69aaff62d" />
<img width="1021" height="327" alt="Screenshot 2026-09-08 204947" src="https://github.com/user-attachments/assets/238b5ea0-e02d-4d4c-afb0-077636d352a6" />
<img width="1047" height="577" alt="Screenshot 2026-09-08 204956" src="https://github.com/user-attachments/assets/128b8ba2-98ea-4cac-b73b-5eb503a6da39" />
<img width="1027" height="317" alt="Screenshot 2026-09-08 205007" src="https://github.com/user-attachments/assets/fd5d42c7-5a15-483f-8a4e-c07bef694656" />
<img width="1035" height="363" alt="Screenshot 2026-09-08 205022" src="https://github.com/user-attachments/assets/a3d3571a-3f0c-4863-984a-d8325ed5d55b" />
<img width="982" height="387" alt="Screenshot 2026-09-08 205030" src="https://github.com/user-attachments/assets/64803be0-76be-4d0c-bc42-5b4d4ec4da5d" />
<img width="1038" height="390" alt="Screenshot 2026-09-08 205037" src="https://github.com/user-attachments/assets/12b8b8e3-00ca-4342-8f1f-aabbbd6bc13f" />


**Result**

The dataset was successfully loaded and processed to extract pollutant-wise and station-wise air quality data for Delhi.


