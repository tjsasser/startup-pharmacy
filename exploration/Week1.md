# Week 1 Deliverables

## Question
Average annual and monthly prescription volume by ZIP.

Output: Rx scripts, monthly averages, annual average.

## Datasets
Medicare Part D Prescribers by Providers, 2024

1,416,883 rows, 84 columns nationwide

### Columns we need
- Prscrbr_NPI : int
- Prscrbr_State_Abrvtn : Str | Categorical
- Prscrbr_Zip5 : int
- Prscrbr_Type : Str | Categorical
- Prscrbr_Ent_Cd:  Str | Categorical
- Tot_Clms : int
- Tot_30day_Fills : float
- Tot_Drug_Cst : float
- Brnd_Tot_Drug_Cst : float
- Gnrc_Tot_Drug_Cst : float

### California Specific
- Rows: 1,43,204 entries 
- Missing data appears as Nan in pandas: 
    - Prscrbr_Type: 1 
    - Brnd_Tot_Drug_Cst: 61170
    - Gnrc_Tot_Drug_Cst: 4379
- Brnd_Tot_Drug_Cst: $229506
- Gnrc_Tot_Drug_Cst: $24717

## RDD plan - data
- Load file into RDD from text, csv "," seperated
- Fetch column index and remove extra columns, map
- Filter all rows to CA only
- Update missing values with team decided value
- Output a CSV or upload to Psql db

## RDD plan - transformations
- Group data by zip, key value: (zip, [rem columns])
- Then Calculate the following:

$BX_z=\sum \text{Brnd\_Tot\_Drug\_Cst}$ 

$GX_z=\sum \text{Gnrc\_Tot\_Drug\_Cst}$

$BX\%_z=\frac{BX_z}{\text{Total Part D Drug Cost}_z}\times100$ 

$GX\%_z=\frac{GX_z}{\text{Total Part D Drug Cost}_z}\times100$

