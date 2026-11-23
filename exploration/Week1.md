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
