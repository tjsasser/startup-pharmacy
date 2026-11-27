# Start-up Pharmacy Location Guide — Project Roadmap

| Created | Last update |
| :---: | :---: |
| Nov 3, 2026 | Nov 7, 2026 |

**Point of contact:** [Mary Kim](mailto:marykimsfo@gmail.com) — USF MS DSAI · (510) 421-9673 · Mkim106@usfca.edu

---

## Team Members

**USF MS DSAI Cohort 15**

- Mary Kim
- Narayan Poudel
- Jonah Robinson
- Hersh Rudrawal
- Thomas Sasser

---

## Introduction

### Purpose

A Start-up Pharmacy Location Guide: identifying independent pharmacy business opportunities in California using PySpark and distributed computing.

### Importance

We, USF MS DSAI Cohort 15 students, are developing a data-driven location guide to help prospective independent pharmacy owners make informed decisions about opening a new pharmacy or expanding an existing business in California.

Over the years, the role of community pharmacies has shifted tremendously, extending well beyond traditional prescription dispensing. As frontline healthcare providers, pharmacies now offer essential services such as immunizations, medication therapy management, diabetes care, and medication adherence consultations. Independent pharmacies in particular play an important role in their communities by offering personalized care and meeting their patients' specific needs.

Unfortunately, compared to established national chains, independent pharmacy owners often have limited access to resources and comprehensive market information. This makes it harder to evaluate a potential location, understand prescription demand in the area, identify prescriber opportunities, and assess competition.

To address these challenges, our project combines public healthcare datasets to provide meaningful insight into local pharmacy market opportunities. Using PySpark, we analyze across California ZIP codes:

- Prescriber distribution and specialties
- Medicare prescription volume
- Brand vs. generic drug spending
- Demographic information to estimate the private/commercial insurance market
- Medicaid prescription utilization for drug demand and spending
- Pharmacy competition

We aim to identify the factors that influence pharmacy business growth and reduce uncertainty in location selection by making large-scale healthcare market information accessible to independent pharmacy owners. This project is also intended as a foundation for future modeling to support pharmacy location and expansion decisions.

### Scope

California only, 2024 datasets, brand-name and generic drugs only.

- Brand (Bx) vs. generic (Gx) spending comparison
- Prescription volume
- Prescriber counts and specialties
- Medicare Part D
- Medicaid estimates
- Commercial estimates
- Pharmacy counts
- ZIP-level analysis

---

## Objectives

### Analysis goals

| RQ | Analysis question | Business purpose | Output |
| :---: | :--- | :--- | :--- |
| 1 | What does the brand-name (Bx) vs. generic (Gx) spending distribution look like by California ZIP code? | Understand medication mix; a high generic share attracts better distributor contracts | Bx/Gx dollars and ratio |
| 2 | What do average annual and monthly prescription volumes look like by ZIP code? | Estimate prescription demand | Rx scripts, monthly average, annual average |
| 3 | How many prescribers are in each ZIP code, and what are their specialties? | Identify potential prescriber relationship opportunities | Prescriber counts with specialty distribution |
| 4 | What is the estimated annual prescription spending for Medicare, Medicaid, and commercial insurance per ZIP code? | Estimate payer mix in the local market | Spending comparison (Med D, Medi-Cal, Commercial) |
| 5 | How many actively licensed pharmacies are in each ZIP code? | Assess local competition | Current active licensed pharmacy counts |
| 6 | What is the ratio of prescribers to pharmacies per California ZIP code? | Understand market opportunity | Prescriber-to-pharmacy ratio |

---

## Data and Pipeline Strategy

### Datasets

**Requirement:** at least one dataset must be ≥ 2 GB and/or ≥ 1M records.

| # | Dataset | Link | Format | Meets size req. | RQ |
| :---: | :--- | :--- | :--- | :---: | :---: |
| D1 | CMS NPPES Data Dissemination V.2 (Nov 2, 2026) — use `npidata_pfile_*.csv` | [Download (ZIP)](https://download.cms.gov/nppes/NPPES_Data_Dissemination_November_2026_V2.zip) | CSV/ZIP (PySpark) | Yes | 3, 6 |
| D2 | NUCC Healthcare Provider Taxonomy, Version 26.1 (7/1/26) | [CSV](https://nucc.org/images/stories/CSV/nucc_taxonomy_261.csv) | CSV | No | 2, 3 |
| D3 | Medicare Part D Prescribers — by Provider, 2024 | [API](https://data.cms.gov/data-api/v1/dataset/14d8e8a9-7e9b-4370-a044-bf97c46b4b44/data) · [Dataset page](https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider) | CSV | Yes | 1, 2, 4 |
| D4 | California DCA Pharmacy License | [DCA public info](https://www.dca.ca.gov/consumers/public_info/index.html) | CSV or XLSX | No | 5, 6 |
| D5 | CMS State Drug Utilization Data (SDUD) 2024 — API: `/api/1/metastore/schemas/dataset/items/61729e5a-7aa8-448c-8903-ba3e0cd0ea3c` | [Dataset page](https://data.medicaid.gov/dataset/61729e5a-7aa8-448c-8903-ba3e0cd0ea3c) | CSV | Yes | 4 |
| D6 | Medicaid Drug Rebate Program 2024 — API: `/api/1/metastore/schemas/dataset/items/0ad65fe5-3ad3-5d79-a3f9-7893ded7963a` | [Dataset page](https://data.medicaid.gov/dataset/0ad65fe5-3ad3-5d79-a3f9-7893ded7963a) | CSV | Yes | 1, 4 |
| D7 | DHCS Medi-Cal Certified Eligible Counts by Month, ZIP Code, and Sex | [Dataset page](https://publish.data.ca.gov/dataset/medi-cal-certified-eligible-counts-by-month-of-eligibility-zip-code-and-sex/resource/791a9aeb-3a9e-4b90-b249-e7e2ad3768f6) | CSV | Verify | 4 |
| D8 | U.S. Census ACS 5-Year 2024 | [Census API](https://www.census.gov/data/developers/data-sets/acs-5year.html) | CSV | Verify | 4 |
| D9 | CA DMHC Prescription Drug Cost Transparency Report 2024 | [PDF](https://www.dmhc.ca.gov/Portals/0/Docs/DO/SB172024Report.pdf) | PDF | Verify | 4 |

### Columns to extract and cleaning

| # | Dataset | Columns | Cleaning | Output table |
| :---: | :--- | :--- | :--- | :--- |
| D1 | CMS NPPES | NPI, Entity_type, practice-location state/ZIP, Taxonomy code 1–15, Primary taxonomy switch 1–15, Enumeration date, Deactivation/reactivation dates | Filter CA; unique individual NPIs (no duplicates); extract ZIP5; select primary taxonomy | `dim_prescriber` |
| D2 | NUCC Taxonomy | Code, Grouping, Classification, Specialization, Display name | Use 2024 version (otherwise most current); extract unique taxonomy codes | `dim_taxonomy` |
| D3 | Medicare Part D by Provider | `Prscrbr_NPI`, `Prscrbr_State_Abrvtn`, `Prscrbr_Zip5`, `Prscrbr_Type`, `Prscrbr_Ent_Cd`, `Tot_Clms`, `Tot_30day_Fills`, `Tot_Drug_Cst`, `Brnd_Tot_Drug_Cst`, `Gnrc_Tot_Drug_Cst` | 2024; filter CA; normalize NPI and ZIP; cast cost and claims columns | `fact_partd_provider` |
| D4 | CA DCA Pharmacy License | License_type, License_number, Pharmacy_name, Organization_type, Physical_address, City, State, ZIP, License_status, Original_issue_date, Expiration_date | Filter CA; keep active license status; remove expired licenses; remove individual pharmacists/techs (check PHY type); remove duplicates | `dim_pharmacy` |
| D5 | CMS SDUD 2024 | State, Year, Quarter, NDC, Utilization_type, Number_of_prescriptions, Total_amount_reimbursed, Medicaid_amount_reimbursed, Non-Medicaid_amount_reimbursed, Suppression_used | Filter CA, 2024; separate FFS (fee-for-service, paid directly by state Medicaid) from MCO (managed care organizations administering Medicaid); keep suppressed records | `fact_sdud_ca_2024` |
| D6 | Medicaid Drug Rebate Program | Year, Quarter, NDC, Drug_Category, Drug_Type_Indicator, FDA_Product_Name | 2024 Rx products; normalize to NDC11; keep quarterly drug category | `dim_medicaid_drug` |
| D7 | DHCS Medi-Cal Eligible Counts | ZIP, Eligibility_year, Eligibility_month, Sex, Eligible_count | Filter 2024; aggregate across sex by ZIP and month | `agg_zip_medical_eligibility` |
| D8 | U.S. Census ACS 2024 | ZCTA5, Total_population, Population_aged_18_64, Privately_insured_population_by_age | Use 5-year ZCTA estimates; count privately insured population | `dim_zip_demographics` |
| D9 | CA DMHC Report 2024 | 2024 reported drug spending, covered plan/member definitions, per-member spending | Extract reported drug spending and covered population | `commercial_benchmark` |

### Join plan and PySpark operations

| RQ | Join plan | Calculation | PySpark operations |
| :---: | :--- | :--- | :--- |
| `rq1` — Bx vs. Gx spending per ZIP | D3 only (no join); filter CA; group by ZIP | Brand % = Σ brand cost / Σ total drug cost × 100<br>Generic % = Σ generic cost / Σ total drug cost × 100 | `filter()`, `map()`, `reduceByKey()`, `mapValues()` |
| `rq2` — Annual & monthly Rx volume | D1 ⟕ D2 on `primary_taxonomy_code = taxonomy_code` (M:1); D3 ⟕ on NPI (1:1) | Annual Rx = Σ total claims<br>Monthly Rx = annual Rx / 12<br>Avg = annual Rx / unique Part D NPIs | `map()`, `join()`, `reduceByKey()`, `mapValues()` |
| `rq3` — Prescriber counts and specialties | D1 ⟕ D2 on `primary_taxonomy_code = taxonomy_code` (M:1) | Prescribers = count(distinct NPI)<br>Prescriber share = prescribers in ZIP / total CA prescribers × 100 | `filter()`, `map()`, `distinct()`, `leftOuterJoin()`, `reduceByKey()` |
| `rq4` — Medicare, Medicaid, and commercial Rx spending | D3: Medicare spending by prescriber ZIP<br>D5 + D7: estimated Medicaid spending by ZIP (1:M)<br>D8 + D9: estimated commercial spending by ZIP (1:M) | Statewide totals allocated to ZIPs by eligibility / insured-population weights | `filter()`, `map()`, `reduceByKey()`, `mapValues()`, `join()`, `leftOuterJoin()` |
| `rq5` — Active pharmacies | D4: CA DCA licensing | Count of distinct active licenses by ZIP | `filter()`, `map()`, `reduceByKey()`, `distinct()` |
| `rq6` — Prescriber-to-pharmacy ratio | Aggregated D1 + aggregated D4, joined on ZIP5 (1:1) | Ratio = prescribers / pharmacies | `map()`, `fullOuterJoin()`, `mapValues()` |

### Data Schema

Schema and example rows for each raw source file. Example rows show only the first few columns.

#### File summary

| # | Tab | Source file | Description | Rows | Columns |
| :---: | :--- | :--- | :--- | ---: | ---: |
| 1 | MDRP (D6) | `Medicaid-Drug-Rebate-Program_2024-drugproducts-q2-2026.csv` | CMS Medicaid Drug Rebate Program product list. One row per NDC per quarter, 2014 Q3 – 2026 Q2. | 2,002,508 | 24 |
| 2 | Pharmacy_Counts (D4) | `Pharmacy_Counts.csv` | Board of Pharmacy license counts by license type (California license categories). The last row is a spreadsheet formula (`=SUM(C2:C38)`). | 38 | 3 |
| 3 | NUCC_Taxonomy (D2) | `nucc_taxonomy_261.csv` | NUCC Health Care Provider Taxonomy code set, version 26.1. One row per taxonomy code. | 883 | 8 |
| 4 | MediCal_Eligibles (D7) | `oct2026_productionsecondsheet_2020_recent.csv` | Medi-Cal certified eligibles by month, ZIP code and sex (California DHCS), 2020-01 onward. ZIP `.` or `STATEWIDE` = statewide total. | 646,584 | 8 |
| 5 | SDUD_2024 (D5) | `sdud2024_updatedJuly2026.csv` | CMS State Drug Utilization Data, 2024 (updated July 2026). One row per state × NDC × quarter × utilization type. | 5,308,407 | 15 |
| 6 | PartD_Prescribers (D3) | `Medicare Part D Prescribers - by Provider.zip > 2024/mup_dpr_ry26_p04_v20_dy24_npi.csv` | CMS Medicare Part D Prescribers – by Provider, data year 2024. One row per prescriber NPI. | 1,416,883 | 84 |
| 7 | NPPES_npidata (D1) | `NPPES_Data_Dissemination_November_2026_V2.zip > npidata_pfile_20050523-20261102.csv` | NPPES NPI Registry main file (November 2026 full release). One row per NPI. | 9,891,116 | 330 |
| 8 | NPPES_pl (D1) | `NPPES_Data_Dissemination_November_2026_V2.zip > pl_pfile_20050523-20261102.csv` | NPPES secondary practice locations. One row per NPI × additional location. | 1,284,848 | 10 |
| 9 | NPPES_endpoint (D1) | `NPPES_Data_Dissemination_November_2026_V2.zip > endpoint_pfile_20050523-20261102.csv` | NPPES electronic endpoints (Direct messaging, FHIR, etc.). One row per NPI × endpoint. | 609,854 | 19 |
| 10 | NPPES_othername (D1) | `NPPES_Data_Dissemination_November_2026_V2.zip > othername_pfile_20050523-20261102.csv` | NPPES other organization names (DBA, former names). One row per NPI × name. | 866,962 | 4 |

#### MDRP — Medicaid Drug Rebate Program (D6)

`Medicaid-Drug-Rebate-Program_2024-drugproducts-q2-2026.csv` · 2,002,508 rows · 24 columns

| # | Column name | Type |
| ---: | :--- | :--- |
| 1 | Year | Integer |
| 2 | Quarter | Integer |
| 3 | Labeler Name | Text |
| 4 | NDC | Text (numeric code) |
| 5 | Labeler Code | Text (numeric code) |
| 6 | Product Code | Text (numeric code) |
| 7 | Package Size Code | Text (numeric code) |
| 8 | Drug Category | Text (code/category) |
| 9 | Drug Type Indicator | Text (numeric code) |
| 10 | Termination Date | Date (MM/DD/YYYY) |
| 11 | Unit Type | Text (code/category) |
| 12 | Units Per Pkg Size | Decimal (zero-padded) |
| 13 | FDA Approval Date | Date (MM/DD/YYYY) |
| 14 | Market Date | Date (MM/DD/YYYY) |
| 15 | FDA Therapeutic Equivalence Code | Text (code/category) |
| 16 | FDA Product Name | Text |
| 17 | Clotting Factor Indicator | Flag (Y/N) |
| 18 | Pediatric Indicator | Flag (Y/N) |
| 19 | Package Size Intro Date | Date (MM/DD/YYYY) |
| 20 | Purchased Product Date | Date (MM/DD/YYYY) |
| 21 | COD Status | Text (numeric code) |
| 22 | FDA Application Number | Numeric with markers (PART346, PART349) |
| 23 | Reactivation Date | Date (MM/DD/YYYY) |
| 24 | Line Extension Drug Indicator | Flag (Y/N) |

Example rows:

| Year | Quarter | Labeler Name | NDC | Labeler Code | Product Code | Package Size Code | Drug Category | Drug Type Indicator | Termination Date | Unit Type | Units Per Pkg Size |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 2023 | 2 | BAUSCH & LOMB INC. | 24208081905 | 24208 | 0819 | 05 | I | 1 | 11/30/2021 | ML | 0000005.000 |
| 2023 | 3 | MYLAN INSTITUTIONAL, INC | 51079073620 | 51079 | 0736 | 20 | N | 1 | 06/30/1994 | TAB | 0000001.000 |
| 2023 | 3 | CIPLA USA, INC. | 69097083207 | 69097 | 0832 | 07 | N | 1 | 04/30/2019 | TAB | 0000100.000 |
| 2023 | 1 | WINTHROP U.S. | 00955104090 | 00955 | 1040 | 90 | I | 1 | 12/31/2014 | TAB | 0000001.000 |
| 2023 | 1 | CARDINAL HEALTH 110, LLC. DBA LEADER | 70000031201 | 70000 | 0312 | 01 | N | 2 | 08/13/2019 | CAP | 0000050.000 |

#### Pharmacy Counts (D4)

`Pharmacy_Counts.csv` · 38 rows · 3 columns

| # | Column name | Type |
| ---: | :--- | :--- |
| 1 | Agency Name | Text |
| 2 | License Type Name | Text |
| 3 | License Count | Numeric with markers (`=SUM(C2:C38)` in last row) |

Example rows:

| Agency Name | License Type Name | License Count |
| :--- | :--- | ---: |
| Board Of Pharmacy | Temp-Pharmacy Technician | 14 |
| Board Of Pharmacy | Centralized Hospital Packaging | 8 |
| Board Of Pharmacy | Outsourcing Facility | 6 |
| Board Of Pharmacy | Centralized Hospital Packaging (government Owned) | 1 |
| Board Of Pharmacy | Sterile Compounding (government Owned) | 117 |

#### NUCC Health Care Provider Taxonomy (D2)

`nucc_taxonomy_261.csv` · 883 rows · 8 columns

| # | Column name | Type |
| ---: | :--- | :--- |
| 1 | Code | Text |
| 2 | Grouping | Text |
| 3 | Classification | Text |
| 4 | Specialization | Text |
| 5 | Definition | Text |
| 6 | Notes | Text |
| 7 | Display Name | Text |
| 8 | Section | Text |

Example rows:

| Code | Grouping | Classification |
| :--- | :--- | :--- |
| 2080P0201X | Allopathic & Osteopathic Physicians | Pediatrics |
| 207RH0005X | Allopathic & Osteopathic Physicians | Internal Medicine |
| 207XS0114X | Allopathic & Osteopathic Physicians | Orthopaedic Surgery |
| 2086P0122X | Allopathic & Osteopathic Physicians | Surgery |
| 207VE0102X | Allopathic & Osteopathic Physicians | Obstetrics & Gynecology |

#### Medi-Cal Certified Eligibles (D7)

`oct2026_productionsecondsheet_2020_recent.csv` · 646,584 rows · 8 columns

| # | Column name | Type |
| ---: | :--- | :--- |
| 1 | Month of Eligibility | Year-month (YYYY-MM) |
| 2 | Zip Code | Numeric with markers (`.`) |
| 3 | Sex | Text (code/category) |
| 4 | Total Eligibles | Integer (comma-formatted) |
| 5 | Annotation Code | Text (numeric code) |
| 6 | Annotation Description | Text |
| 7 | Status | Text (code/category) |
| 8 | Date of Data | Date (DDMONYYYY) |

Example rows:

| Month of Eligibility | Zip Code | Sex | Total Eligibles |
| :--- | :--- | :--- | ---: |
| 2020-09 | STATEWIDE | Total | 13,205,495 |
| 2020-11 | 95633 | Female | 434 |
| 2020-05 | 91202 | Total | 7,471 |
| 2020-12 | 90222 | Male | 8,729 |
| 2020-02 | 95002 | Male | 208 |

#### CMS State Drug Utilization Data (D5)

`sdud2024_updatedJuly2026.csv` · 5,308,407 rows · 15 columns

| # | Column name | Type |
| ---: | :--- | :--- |
| 1 | Utilization Type | Text (code/category) |
| 2 | State | Text (code/category) |
| 3 | NDC | Text (numeric code) |
| 4 | Labeler Code | Text (numeric code) |
| 5 | Product Code | Text (numeric code) |
| 6 | Package Size | Text (numeric code) |
| 7 | Year | Integer |
| 8 | Quarter | Integer |
| 9 | Suppression Used | Boolean (true/false) |
| 10 | Product Name | Text |
| 11 | Units Reimbursed | Decimal (zero-padded) |
| 12 | Number of Prescriptions | Integer (zero-padded) |
| 13 | Total Amount Reimbursed | Decimal (zero-padded) |
| 14 | Medicaid Amount Reimbursed | Decimal (zero-padded) |
| 15 | Non Medicaid Amount Reimbursed | Decimal (zero-padded) |

Example rows:

| Utilization Type | State | NDC | Labeler Code | Product Code |
| :--- | :--- | :--- | :--- | :--- |
| FFSU | AK | 00074433902 | 00074 | 4339 |
| FFSU | AR | 65162019011 | 65162 | 0190 |
| FFSU | AL | 72786010101 | 72786 | 0101 |
| FFSU | AR | 70377000813 | 70377 | 0008 |
| MCOU | AR | 68645019059 | 68645 | 0190 |

#### Medicare Part D Prescribers — by Provider (D3)

`Medicare Part D Prescribers - by Provider.zip > 2024/mup_dpr_ry26_p04_v20_dy24_npi.csv` · 1,416,883 rows · 84 columns (first 46 listed)

| # | Column name | Type |
| ---: | :--- | :--- |
| 1 | `Prscrbr_NPI` | Text (numeric code) |
| 2 | `Prscrbr_Last_Org_Name` | Text |
| 3 | `Prscrbr_First_Name` | Text |
| 4 | `Prscrbr_MI` | Text |
| 5 | `Prscrbr_Crdntls` | Text |
| 6 | `Prscrbr_Ent_Cd` | Text (code/category) |
| 7 | `Prscrbr_St1` | Text |
| 8 | `Prscrbr_St2` | Text |
| 9 | `Prscrbr_City` | Text |
| 10 | `Prscrbr_State_Abrvtn` | Text |
| 11 | `Prscrbr_State_FIPS` | Text (numeric code) |
| 12 | `Prscrbr_Zip5` | Text (numeric code) |
| 13 | `Prscrbr_RUCA` | Decimal |
| 14 | `Prscrbr_RUCA_Desc` | Text |
| 15 | `Prscrbr_Cntry` | Text (code/category) |
| 16 | `Prscrbr_Type` | Text |
| 17 | `Prscrbr_Type_Src` | Text |
| 18 | `Tot_Clms` | Decimal |
| 19 | `Tot_30day_Fills` | Decimal |
| 20 | `Tot_Drug_Cst` | Decimal |
| 21 | `Tot_Day_Suply` | Decimal |
| 22 | `Tot_Benes` | Decimal |
| 23 | `GE65_Sprsn_Flag` | Text (suppression flag: `#` or `*`) |
| 24 | `GE65_Tot_Clms` | Decimal |
| 25 | `GE65_Tot_30day_Fills` | Decimal |
| 26 | `GE65_Tot_Drug_Cst` | Decimal |
| 27 | `GE65_Tot_Day_Suply` | Decimal |
| 28 | `GE65_Bene_Sprsn_Flag` | Text (suppression flag: `#` or `*`) |
| 29 | `GE65_Tot_Benes` | Decimal |
| 30 | `Brnd_Sprsn_Flag` | Text (suppression flag: `#` or `*`) |
| 31 | `Brnd_Tot_Clms` | Decimal |
| 32 | `Brnd_Tot_Drug_Cst` | Decimal |
| 33 | `Gnrc_Sprsn_Flag` | Text (suppression flag: `#` or `*`) |
| 34 | `Gnrc_Tot_Clms` | Decimal |
| 35 | `Gnrc_Tot_Drug_Cst` | Decimal |
| 36 | `Othr_Sprsn_Flag` | Text (suppression flag: `#` or `*`) |
| 37 | `Othr_Tot_Clms` | Decimal |
| 38 | `Othr_Tot_Drug_Cst` | Decimal |
| 39 | `MAPD_Sprsn_Flag` | Text (suppression flag: `#` or `*`) |
| 40 | `MAPD_Tot_Clms` | Decimal |
| 41 | `MAPD_Tot_Drug_Cst` | Decimal |
| 42 | `PDP_Sprsn_Flag` | Text (suppression flag: `#` or `*`) |
| 43 | `PDP_Tot_Clms` | Decimal |
| 44 | `PDP_Tot_Drug_Cst` | Decimal |
| 45 | `LIS_Sprsn_Flag` | Text (suppression flag: `#` or `*`) |
| 46 | `LIS_Tot_Clms` | Decimal |

#### NPPES — NPI Registry main file (D1)

`NPPES_Data_Dissemination_November_2026_V2.zip > npidata_pfile_20050523-20261102.csv` · 9,891,116 rows · 330 columns (first 47 listed)

| # | Column name | Type |
| ---: | :--- | :--- |
| 1 | NPI | Text (numeric code) |
| 2 | Entity Type Code | Text (numeric code) |
| 3 | Replacement NPI | Text (numeric code) |
| 4 | Employer Identification Number (EIN) | Text (masked as `<UNAVAIL>`) |
| 5 | Provider Organization Name (Legal Business Name) | Text |
| 6 | Provider Last Name (Legal Name) | Text |
| 7 | Provider First Name | Text |
| 8 | Provider Middle Name | Text |
| 9 | Provider Name Prefix Text | Text (code/category) |
| 10 | Provider Name Suffix Text | Text |
| 11 | Provider Credential Text | Text |
| 12 | Provider Other Organization Name | Text (masked as `<UNAVAIL>`) |
| 13 | Provider Other Organization Name Type Code | Text (numeric code) |
| 14 | Provider Other Last Name | Text |
| 15 | Provider Other First Name | Text |
| 16 | Provider Other Middle Name | Text |
| 17 | Provider Other Name Prefix Text | Text |
| 18 | Provider Other Name Suffix Text | Text (code/category) |
| 19 | Provider Other Credential Text | Text |
| 20 | Provider Other Last Name Type Code | Text (numeric code) |
| 21 | Provider First Line Business Mailing Address | Text |
| 22 | Provider Second Line Business Mailing Address | Text |
| 23 | Provider Business Mailing Address City Name | Text |
| 24 | Provider Business Mailing Address State Name | Text |
| 25 | Provider Business Mailing Address Postal Code | Text (numeric code) |
| 26 | Provider Business Mailing Address Country Code (If outside U.S.) | Text (code/category) |
| 27 | Provider Business Mailing Address Telephone Number | Text (numeric code) |
| 28 | Provider Business Mailing Address Fax Number | Text (numeric code) |
| 29 | Provider First Line Business Practice Location Address | Text |
| 30 | Provider Second Line Business Practice Location Address | Text |
| 31 | Provider Business Practice Location Address City Name | Text |
| 32 | Provider Business Practice Location Address State Name | Text |
| 33 | Provider Business Practice Location Address Postal Code | Text (numeric code) |
| 34 | Provider Business Practice Location Address Country Code (If outside U.S.) | Text (code/category) |
| 35 | Provider Business Practice Location Address Telephone Number | Text (numeric code) |
| 36 | Provider Business Practice Location Address Fax Number | Text (numeric code) |
| 37 | Provider Enumeration Date | Date (MM/DD/YYYY) |
| 38 | Last Update Date | Date (MM/DD/YYYY) |
| 39 | NPI Deactivation Reason Code | Text – no values in public file |
| 40 | NPI Deactivation Date | Date (MM/DD/YYYY) |
| 41 | NPI Reactivation Date | Date (MM/DD/YYYY) |
| 42 | Provider Sex Code | Text (code/category) |
| 43 | Authorized Official Last Name | Text |
| 44 | Authorized Official First Name | Text |
| 45 | Authorized Official Middle Name | Text |
| 46 | Authorized Official Title or Position | Text |
| 47 | Authorized Official Telephone Number | Text (numeric code) |

#### NPPES — Secondary Practice Locations (D1)

`NPPES_Data_Dissemination_November_2026_V2.zip > pl_pfile_20050523-20261102.csv` · 1,284,848 rows · 10 columns

| # | Column name | Type |
| ---: | :--- | :--- |
| 1 | NPI | Text (numeric code) |
| 2 | Provider Secondary Practice Location Address- Address Line 1 | Text |
| 3 | Provider Secondary Practice Location Address- Address Line 2 | Text |
| 4 | Provider Secondary Practice Location Address - City Name | Text |
| 5 | Provider Secondary Practice Location Address - State Name | Text |
| 6 | Provider Secondary Practice Location Address - Postal Code | Text (numeric code) |
| 7 | Provider Secondary Practice Location Address - Country Code (If outside U.S.) | Text (code/category) |
| 8 | Provider Secondary Practice Location Address - Telephone Number | Text (numeric code) |
| 9 | Provider Secondary Practice Location Address - Telephone Extension | Text (numeric code) |
| 10 | Provider Practice Location Address - Fax Number | Text (numeric code) |

Example rows:

| NPI | Address Line 1 | Address Line 2 | City | State |
| :--- | :--- | :--- | :--- | :--- |
| 1265571152 | 1233 Locust Street | 500 | Philadelphia | PA |
| 1013067354 | 965 S Main St | Suite 1 | Waterbury | CT |
| 1003879073 | 9035 Sunset Dr | Suite 202 | Miami | FL |
| 1760525133 | 3674 Route 27, Department B | Princeton Radiology Associates, P.A. | Kendall Park | NJ |
| 1295766871 | 801 N Lincoln Avenue | C/O Cox Monett Hospital Inc | Monett | MO |

#### NPPES — Electronic Endpoints (D1)

`NPPES_Data_Dissemination_November_2026_V2.zip > endpoint_pfile_20050523-20261102.csv` · 609,854 rows · 19 columns

| # | Column name | Type |
| ---: | :--- | :--- |
| 1 | NPI | Text (numeric code) |
| 2 | Endpoint Type | Text (code/category) |
| 3 | Endpoint Type Description | Text |
| 4 | Endpoint | Text |
| 5 | Affiliation | Flag (Y/N) |
| 6 | Endpoint Description | Text |
| 7 | Affiliation Legal Business Name | Text |
| 8 | Use Code | Text (code/category) |
| 9 | Use Description | Text |
| 10 | Other Use Description | Text |
| 11 | Content Type | Text (code/category) |
| 12 | Content Description | Text (code/category) |
| 13 | Other Content Description | Text |
| 14 | Affiliation Address Line One | Text |
| 15 | Affiliation Address Line Two | Text |
| 16 | Affiliation Address City | Text |
| 17 | Affiliation Address State | Text |
| 18 | Affiliation Address Country | Text (code/category) |
| 19 | Affiliation Address Postal Code | Text (numeric code) |

Example rows:

| NPI | Endpoint Type | Endpoint Type Description | Affiliation |
| :--- | :--- | :--- | :--- |
| 1063624690 | OTHERS | Other URL | Y |
| 1790769438 | OTHERS | Other URL | Y |
| 1477549822 | CONNECT | CONNECT URL | Y |
| 1952374654 | CONNECT | CONNECT URL | Y |
| 1558360024 | CONNECT | CONNECT URL | Y |

#### NPPES — Other Organization Names (D1)

`NPPES_Data_Dissemination_November_2026_V2.zip > othername_pfile_20050523-20261102.csv` · 866,962 rows · 4 columns

| # | Column name | Type |
| ---: | :--- | :--- |
| 1 | NPI | Text (numeric code) |
| 2 | Provider Other Organization Name | Text |
| 3 | Provider Other Organization Name Type Code | Text (numeric code) |
| 4 | Created Date | Date (MM/DD/YYYY) |

Example rows:

| NPI | Provider Other Organization Name | Type Code |
| :--- | :--- | :--- |
| 1396819728 | SEARS OPTICAL #C0168 | 3 |
| 1215009097 | Case Management-North | 5 |
| 1912936865 | Rehab Medical of Salt Lake, Inc. | 4 |
| 1528149655 | Bashas' United Drugs; Food City United Drugs; AJ's United Dr… | 3 |
| 1275529257 | PHARMACY EXPRESS | 3 |

---

## Team Plan

**Meetings** (in person at 101 Howard):

- Monday & Thursday, 12–1 pm
- Friday, 2–3 pm

| Member | Research questions | Initials and dates |
| :--- | :--- | :--- |
| Mary Kim | Q4 (Medicaid, Medicare) | |
| Jonah Robinson | Q1, Q5 | |
| Thomas Sasser | Q4 (Medicare, Commercial) | |
| Hersh Rudrawal | Q2, Q6 | |
| Narayan Poudel | Q3, Q6 | |

---

## Team Schedule

### Mary Kim

| Week | Tasks | Deliverables |
| :--- | :--- | :--- |
| 11/6 – 11/9 | Locate and retrieve CMS SDUD 2024 and DHCS Medi-Cal eligible counts; D6 drug products | Raw Medicaid files, counts, log, schema |
| 11/10 – 11/16 | Load SDUD/DHCS with RDDs; filter CA, 2024; normalize ZIP; extract reimbursement and eligibility values; suppression | Medicaid and eligibility RDD |
| 11/17 – 11/23 | One statewide Medicaid spending and ZIP eligibility weights; join SDUD + D6 on NDC/year/quarter | Medicaid spending, eligibility weights, NDC join |
| 11/24 – 11/30 | Medicaid 2024 spending calculation; allocation by ZIP + Medicaid shares | Medicaid spending estimates by ZIP |
| 12/1 – 12/7 | Validate Medicaid spending estimates and geo distribution; notes on limitations and methodology | Medicaid charts and findings |
| 12/8 – 12/14 | Write methodology on calculation, suppression strategy, and eligibility allocation | Report write-up |
| 12/15 – 12/16 | Rerun results; finalize methodology and limitations | Final Medicaid RQ4 |

### Thomas Sasser

| Week | Tasks | Deliverables |
| :--- | :--- | :--- |
| 11/6 – 11/9 | Locate 2024 DMHC prescription drug report; locate Census ACS 2024 for private insurance data; identify eligible commercial spending measures | Commercial notes, ACS fields |
| 11/10 – 11/16 | DMHC commercial spending; privately insured population by ZCTA with ACS data | Commercial and cleaned population RDD |
| 11/17 – 11/23 | Commercial spending amount + ZIP/ZCTA insurance weights; coverage and geo match verification | Commercial allocation calculation validation and QA report |
| 11/24 – 11/30 | Calculate commercial ZIP spending estimates per the formula; combine Med D, Medicaid, and commercial results with RDD joins | RQ4 three-payer spending table |
| 12/1 – 12/7 | Combine all ZIP-level RDD outputs; generate ZIP selector, spending chart, and comparison tables | Interactive dashboard |
| 12/8 – 12/14 | Validate commercial methodology with Mary; compile report | Report draft |
| 12/15 – 12/16 | Repo check and report submission | Final report and QA |

### Jonah Robinson

| Week | Tasks | Deliverables |
| :--- | :--- | :--- |
| 11/6 – 11/9 | Locate Medicare Part D by Provider (2024); locate CA DCA pharmacy license data; verify brand and generic spending columns; verify license category | Raw Medicare Part D, raw DCA file, column and size check |
| 11/10 – 11/16 | Clean Part D Bx/Gx spending; clean DCA file; filter CA, active licensed pharmacies, ZIP code | Cleaned Part D spending and pharmacies |
| 11/17 – 11/23 | Aggregate Med D brand/generic spending; count unique pharmacies by ZIP | RQ1 spending amount RDD; RQ5 pharmacy counts RDD |
| 11/24 – 11/30 | Calculate Bx/Gx spending %, annual total drug costs, and unique active pharmacy counts by ZIP | Completed RQ1, RQ5 metrics; Medicare spending for RQ4 (hand off to Tom) |
| 12/1 – 12/7 | Bx/Gx charts; pharmacy counts; Medicare spending results for later dashboard use | RQ1, RQ5 final usage-ready files |
| 12/8 – 12/14 | Compare Bx/Gx drug costs against Part D totals; double-check active pharmacy counts and license filters | RQ1, RQ5 report findings |
| 12/15 – 12/16 | Finalize Bx/Gx spending, Medicare spending, and active pharmacies | RQ1, RQ5, and Med D for RQ4 files |

### Hersh Rudrawal

| Week | Tasks | Deliverables |
| :--- | :--- | :--- |
| 11/6 – 11/9 | Explore Med D `Tot_Clms`, `Tot_30day_Fills`, NPI, and ZIP | Rx volume check, RDD plan |
| 11/10 – 11/16 | Clean prescription claims, 30-day fills, NPI, and ZIP codes from Med D | Cleaned prescription volume RDD |
| 11/17 – 11/23 | Join Part D Rx records with provider specialty RDD using NPI (from Narayan); handle unmatched NPIs | NPI join RDD |
| 11/24 – 11/30 | Calculate annual Rx claims, monthly claims, 30-day fills, and average Rx-to-provider ratio; combine prescriber and pharmacy ZIP counts for RQ6 | RQ2 prescription volume; RQ6 ratio |
| 12/1 – 12/7 | Prepare annual/monthly volume for visualization; double-check prescriber-to-pharmacy ratio | RQ2, RQ6 dashboard-ready output |
| 12/8 – 12/14 | Reconcile total annual claims + fills; check RQ6 zero-pharmacy ZIPs and joins | RQ2, RQ6 validation and report |
| 12/15 – 12/16 | Calculator for Rx volumes and prescriber-to-pharmacy ratio | Final RQ2, RQ6 files |

### Narayan Poudel

| Week | Tasks | Deliverables |
| :--- | :--- | :--- |
| 11/6 – 11/9 | Locate NPPES and NUCC 2024 taxonomy; understand primary taxonomy and CA ZIP for practices | Raw NPPES/NUCC fields check, row counts |
| 11/10 – 11/16 | Filter NPPES for CA and individual providers; primary taxonomy; normalize ZIP and NPI; clean NUCC specialty codes | Clean provider and taxonomy RDD |
| 11/17 – 11/23 | Join NPPES with NUCC on primary taxonomy code; double-check one row per NPI; get provider counts to hand off to Hersh | Provider and specialty RDD; join validation check |
| 11/24 – 11/30 | Calculate unique prescribers per ZIP, per specialty, and for CA; check the RQ6 denominator calculation | Completed RQ3, provider counts check |
| 12/1 – 12/7 | Prepare provider distribution charts; cross-check with RQ6 provider counts | RQ3 visualization-ready provider table |
| 12/8 – 12/14 | Check unique NPI counts, primary specialty, prescriber definition, and NPPES date | QA findings for report |
| 12/15 – 12/16 | Finalize provider and specialty counts; check consistency with RQ6 | Final RQ3 and provider side of RQ6 file |


## Repo Structure

```text
.
├── notebooks
│   ├── rq1
│   │   └── notes.txt
│   ├── rq2
│   │   └── notes.txt
│   ├── rq3
│   │   └── notes.txt
│   ├── rq4
│   │   ├── mary_d5_sdud.ipynb
│   │   ├── mary_d6_mdrp.ipynb
│   │   ├── mary_r7_dhcs.ipynb
│   │   └── mary_rq4_analysis.ipynb
│   ├── rq5
│   │   └── notes.txt
│   └── rq6
│       └── notes.txt
├── README.md
└── requirements.txt
```

## Notes on the notebooks

Notebooks run independently.  Run them in any order.

### RQ4 : Estimation on medicaid prescription spending by CA Zip code.
Methodology = CA statewide medicaid reimbursement form SDUD

```text
Get Medi-cal eligible patients by CA zip code from DHCS

Calculate share of zip eligible population / total CA population
share * statewide medicaid spending.

Keeping the suppressed data since they are not $0.
```
