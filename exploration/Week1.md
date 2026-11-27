# Week 1 Deliverables

## Question

Average annual and monthly prescription volume by ZIP.

Output: Rx scripts, monthly averages, annual average.

## Datasets

Medicare Part D Prescribers by Provider, 2024

1,416,883 rows, 84 columns nationwide

### Columns we need

- Prscrbr_NPI : int
- Prscrbr_State_Abrvtn : Str | Categorical
- Prscrbr_Zip5 : Str
- Prscrbr_Type : Str | Categorical
- Prscrbr_Ent_Cd:  Str | Categorical
- Tot_Clms : int
- Tot_30day_Fills : float
- Tot_Drug_Cst : float
- Prscrbr_Type_Src: Str
- Tot_Benes: int

### Note

Prescribers with less than 11 claims are dropped from the data set. So prescribers in rural areas may be underrepresented.

### California Specific

- Rows: 143,204 CA rows
- Rows: 143,180 valid entries (wrong ZIP range)
- Unique CA Zips: 1432
- Missing data:
  - Brnd_Tot_Drug_Cst   : 43%
  - Gnrc_Tot_Drug_Cst   :  3%
  - Tot_Benes           : 12%

Compiled Data in: rq2_data.csv

### RDD Plan

Entire Medicare Dataset

- Load the file
- Each line into a row, and each row split by delimiter "," | shape: (1416883, 84)
- Filter out extra columns | shape: (1416883, 12)
- Convert existing columns to proper datatype, handle ZIP properly
- Filter rows by CA only | shape: (143204, 12)
- Remove invalid entries- wrong ZIP range | shape: (143180, 12)
- Convert to Pair RDD on Zip code
- Group data by ZIP then aggregate the values | shape: (1432, 8)
  - prescribers: number of unique prescribers
  - annual_claims: total claims
  - annual_fills: total fills
  - monthly_claims: annual claims / 12
  - claims_per_prescriber: total claims / prescriber count
  - monthly_fills: total fills / 12
  - fills_per_prescriber: total fills / prescriber count
- Output RDD to csv file or pass on to next process
