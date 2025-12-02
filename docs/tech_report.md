# Technical Report

## Data Source

UNESCO / UNICEF / UN Statistics Division — Global Education Dataset
Raw CSV:
https://raw.githubusercontent.com/dagonzalezcolme/Educational-Gap-in-US/main/data/Global_Education_raw.csv

#### Overview

- ~202 countries
- ~29 indicators including:
	- completion rates
	- out-of-school rates
	- literacy
	- proficiency
	- enrollment
	- demographics
- Data types: string, float
- Years covered: 2018–2020 (pre-COVID)

#### Why This Data

The dataset provides global educational indicators separated by gender and by education level, allowing broad comparisons of gaps and regional patterns.

#### Limitations

- ~38% missing data across all indicators
- Only a few countries contain full records
- Reporting standards vary by country
- More missing data for smaller or lower-income regions

## Data Preparation

- Loaded data with latin-1 due to encoding issues in source file
- Replaced 0s with NaNs for all education related numeric columns
- Standardized column names for readability
- Corrected corrupted country names caused by encoding errors

Cleaning function:
```python
def clean_global_education_data():
    df = pd.read_csv(
        'https://raw.githubusercontent.com/dagonzalezcolme/Educational-Gap-in-US/main/data/Global_Education_raw.csv',
        encoding='latin-1'
    )

    education_cols = df.columns[3:]
    df[education_cols] = df[education_cols].replace(0, np.nan)

    column_mapping = {
        'Countries and areas': 'Country',
        'Latitude ': 'Latitude',
        'Longitude': 'Longitude',
        'OOSR_Pre0Primary_Age_Male': 'OOSR_PrePrimary_Male_Pct',
        'OOSR_Pre0Primary_Age_Female': 'OOSR_PrePrimary_Female_Pct',
        'OOSR_Primary_Age_Male': 'OOSR_Primary_Male_Pct',
        'OOSR_Primary_Age_Female': 'OOSR_Primary_Female_Pct',
        'OOSR_Lower_Secondary_Age_Male': 'OOSR_Lower_Secondary_Male_Pct',
        'OOSR_Lower_Secondary_Age_Female': 'OOSR_Lower_Secondary_Female_Pct',
        'OOSR_Upper_Secondary_Age_Male': 'OOSR_Upper_Secondary_Male_Pct',
        'OOSR_Upper_Secondary_Age_Female': 'OOSR_Upper_Secondary_Female_Pct',
        'Completion_Rate_Primary_Male': 'Completion_Primary_Male_Pct',
        'Completion_Rate_Primary_Female': 'Completion_Primary_Female_Pct',
        'Completion_Rate_Lower_Secondary_Male': 'Completion_Lower_Secondary_Male_Pct',
        'Completion_Rate_Lower_Secondary_Female': 'Completion_Lower_Secondary_Female_Pct',
        'Completion_Rate_Upper_Secondary_Male': 'Completion_Upper_Secondary_Male_Pct',
        'Completion_Rate_Upper_Secondary_Female': 'Completion_Upper_Secondary_Female_Pct',
        'Grade_2_3_Proficiency_Reading': 'Proficiency_Reading_Grade2_3_Pct',
        'Grade_2_3_Proficiency_Math': 'Proficiency_Math_Grade2_3_Pct',
        'Primary_End_Proficiency_Reading': 'Proficiency_Reading_Primary_End_Pct',
        'Primary_End_Proficiency_Math': 'Proficiency_Math_Primary_End_Pct',
        'Lower_Secondary_End_Proficiency_Reading': 'Proficiency_Reading_Lower_Secondary_End_Pct',
        'Lower_Secondary_End_Proficiency_Math': 'Proficiency_Math_Lower_Secondary_End_Pct',
        'Youth_15_24_Literacy_Rate_Male': 'Literacy_Youth_15_24_Male_Pct',
        'Youth_15_24_Literacy_Rate_Female': 'Literacy_Youth_15_24_Female_Pct',
        'Birth_Rate': 'Birth_Rate_Per_1000',
        'Gross_Primary_Education_Enrollment': 'Enrollment_Primary_Gross_Pct',
        'Gross_Tertiary_Education_Enrollment': 'Enrollment_Tertiary_Gross_Pct',
        'Unemployment_Rate': 'Unemployment_Rate_Pct'
    }

    df.rename(columns=column_mapping, inplace=True)

    corrupted_mask = df['Country'].str.contains('ï¿½', na=False)
    if corrupted_mask.any():
        df.loc[corrupted_mask, 'Country'] = 'Sao Tome and Principe'

    return df
```

---
## Question 1 Analysis

#### Tools

- pandas
- matplotlib
- geopandas

#### Key Variables

- Country (ADMIN)
- Completion_Primary_Male_Pct
- Completion_Primary_Female_Pct
- Completion_Lower_Secondary_Male_Pct
- Completion_Lower_Secondary_Female_Pct
- Completion_Upper_Secondary_Male_Pct
- Completion_Upper_Secondary_Female_Pct

#### Data Preparation 

Countries were filtered to keep only those with complete data for all six completion-rate columns:

```python
completion_cols = [
    'Completion_Primary_Male_Pct',
    'Completion_Primary_Female_Pct',
    'Completion_Lower_Secondary_Male_Pct',
    'Completion_Lower_Secondary_Female_Pct',
    'Completion_Upper_Secondary_Male_Pct',
    'Completion_Upper_Secondary_Female_Pct'
]

df = df.dropna(subset=completion_cols)
```

#### Derived Variables

Average Male Completion:

```python
male_cols = [
    'Completion_Primary_Male_Pct',
    'Completion_Lower_Secondary_Male_Pct',
    'Completion_Upper_Secondary_Male_Pct'
]
df['Avg_Male'] = df[male_cols].mean(axis=1)
```

Average Female Completion:

```python
female_cols = [
    'Completion_Primary_Female_Pct',
    'Completion_Lower_Secondary_Female_Pct',
    'Completion_Upper_Secondary_Female_Pct'
]
df['Avg_Female'] = df[female_cols].mean(axis=1)
```

Average Gender Gap:

```python
df['Avg_Gap'] = ((df['Completion_Primary_Female_Pct'] - df['Completion_Primary_Male_Pct']) +
                 (df['Completion_Lower_Secondary_Female_Pct'] - df['Completion_Lower_Secondary_Male_Pct']) +
                 (df['Completion_Upper_Secondary_Female_Pct'] - df['Completion_Upper_Secondary_Male_Pct'])) / 3


Average Total Completion:

df['Avg_Total'] = (df['Avg_Male'] + df['Avg_Female']) / 2
```

#### Data Issues

Tanzania and Burundi were removed from the below 50% visualization due to plotting problems despite complete data in the source.

#### Validation

- Verified that completion rates fall within 0 to 100 range
- Confirmed country counts match between data processing steps
- Checked that derived averages fall between expected minimum and maximum values

#### Alternative Analyses Explored

Initially only upper secondary completion was analyzed. This was expanded to average all three levels after recognizing that a single level may not represent the full picture of education gaps. A combined single chart showing all countries was attempted but became too crowded. Splitting into above and below 50% groups improved readability and revealed the pattern more clearly.

#### Visual Mapping Choices

Choropleth Map
- Orange = male advantage
- Green = female advantage
- Grey = missing data

Dumbbell Charts
- Green = male rates
- Yellow = female rates
- Grey connecting line = gender gap

#### Ethics and Transparency

- AI was used for visual conceptualization and drafting written content, followed by review and editing to ensure accuracy.
- National averages obscure within-country disparities
- Completion does not measure learning quality

--- 
## Question 2 Analysis



--- 
## Question 3 Analysis


