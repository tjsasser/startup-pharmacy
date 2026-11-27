# Start-up Pharmacy Location Guide — Project Roadmap

| Created | Last update |
| :---: | :---: |
| Nov 3, 2026 | Nov 7, 2026 |

**Point of contact:** [Mary Kim](mailto:marykimsfo@gmail.com) — USF MS DSAI · (510) 421-9673 · Mkim106@usfca.edu

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

### Contributors

**USF MS DSAI Cohort 15**

- Mary Kim
- Narayan Poudel
- Jonah Robinson
- Hersh Rudrawal
- Thomas Sasser

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

The data schema with examples for each data source is in the accompanying spreadsheet; row and column counts are listed at the top left of each tab.

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
