# Global Education Data

#### Basic Info

- File: Global_Education_Cleaned.csv
- Countries: 202
- Columns: 29
- Time period: Likely 2018-2020 data, published March-June 2021, pre-covid
- Sources: UNESCO, UNICEF, UN Statistics Division

#### Missing Data

- Missing values are blank (NaN), not zeros
- A lot of data is missing (38% overall)
- Proficiency data has the most gaps (55-78% missing)
- Only 4 countries have complete 100% of data: Colombia, Costa Rica, Honduras, Panama
- Small countries have the most missing data

#### Sources

- Out-of-school rates: UNESCO (March 2021)
- Completion rates: UNICEF (April 2021)
- Proficiency scores: UN Statistics Division (June 2021)
- Youth literacy: UNESCO (March 2021)
- Birth rate, enrollment, unemployment: Unknown

#### Units

- Latitude: decimal degrees
- Longitude: decimal degrees
- *_Pct: percentage
- *_Per_1000: rate per 1000 people

#### Categories

- OOSR: out-of-school rates by education level and gender
  - PrePrimary: ages 3-5
  - Primary: ages 6-11
  - Lower Secondary: ages 12-14
  - Upper Secondary: ages 15-17
- Completion: completion rates by education level and gender
  - Primary: ages 6-11
  - Lower Secondary: ages 12-14
  - Upper Secondary: ages 15-17
- Proficiency: reading and math proficiency by grade level
  - Grade2_3: ages 7-8 (early primary)
  - Primary_End: ages 10-12 (end of primary)
  - Lower_Secondary_End: ages 13-15 (end of lower secondary)
- Literacy: youth literacy (ages 15-24) by gender
- Birth_Rate_Per_1000: births per 1000 population
- Enrollment: gross enrollment ratios
- Unemployment_Rate_Pct: unemployment as percentage of labor force

**Note: Enrollment can be over 100%**
- Counts all enrolled students regardless of age
- Divided by age population
- So >100% happens if
  - Students started late or repeated grades 
  - Students started early
- Example: Madagascar has 142.5% primary enrollment due to many over age students

