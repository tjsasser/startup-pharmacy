

| Created  →  Nov 3, 2026  | Last update  →  Nov 7, 2026 |
| :---: | :---: |
|  PROJECT ROADMAP   |  |
| Point of contact  →  [Mary Kim](mailto:marykimsfo@gmail.com)  USF MS DSAI / (510) 421-9673 Email : Mkim106@usfca.edu |  |

|  Start-up Pharmacy Location Guide |  |
| :---- | ----- |

| 01 |
| :---- |

### **Introduction**

#### Purpose

A Start-up Pharmacy Location Guide : Identifying Independent Pharmacy Business Opportunity in California using Pyspark, distributed computing.

#### Importance

We, USF MS DSAI Cohort 15 students, decided to develop a data-driven location guide that helps potential independent pharmacy owners make informed business decisions on opening a new business or expansion of their business in California. 

Over the course of years, the role of community pharmacies have shifted tremendously. Its scope is beyond traditional prescription dispensing. Pharmacies now provide essential services such as immunization, medication therapy management, diabetes care and medication adherence consultation as a frontline healthcare provider. Independent pharmacies in particular, play a very important role in their communities by offering personalized care and providing specific needs to their patients. 

Unfortunately, compared to established national chain pharmacies, independent pharmacy owners often have limited access to resources and comprehensive market information. It challenges their business decision making when they evaluate their potential business location, understand prescription demand in the area, identify provider opportunity and assess competition. 

To address these challenges, our project focuses on incorporating public healthcare datasets to provide meaningful insights into local pharmacy market opportunities. Using Pyspark, we decided to analyze prescriber distribution and their specialties, Medicare prescription volume, Brands versus generics drug spending, demographic information to estimate private / commercial insurance market, Medicaid prescription utilization for drug demand and spending across California ZIP codes as well as pharmacy competition information. 

We aim to provide the factors that influence pharmacy business growth, to reduce uncertainty in the location selection process by making large-scale healthcare market information accessible to independent pharmacy owners. This project is planned to establish a foundation for future modeling to further support pharmacy location and business expansion decisions. 

#### Scope

California specific, 2024 datasets, Brand name and generics only. 

* Brand (Bx) vs Generics (Gx) spending comparison   
* Prescription Volume   
* Prescriber counts and specialties   
* Medicare Part D   
* Medicaid Estimates   
* Commercial Estimates   
* Pharmacy counts   
* ZIP level 

#### Contributors 

**USF MS DSAI Cohort 15**  
Mary Kim   
Narayan Poudel  
Jonah Robinson  
Hersh Rudrawal  
Thomas Sasser 

|  02 |  |  |
| :---- | ----- | ----- |

### **Objectives**

#### Analysis goals 

1) Analysis question : What does the brand-name (Bx) versus generic (Gx) spending distribution look like by California ZIP code?   
   Business purpose : To understand medication mix. High generics attracts a better contract from distributors.    
   Output : Bx/Gx dollars and ratio  
2) Analysis question : What does the average annual and monthly prescription volumes look like by ZIP code?  
   Business purpose : Estimate prescription demand.  
   Output : Rx scripts, monthly averages, annual average.  
3) Analysis question : How many prescribers are in each ZIP code? What are their specialties?   
   Business purpose : Identify potential prescriber relationship opportunities.  
   Output : Prescriber counts with specialty distribution.   
4) Analysis question : What is the estimated annual prescription spending for Medicare, Medicaid, and commercial insurance per ZIP code?  
   Business purpose : Estimate payer mix in the local market.   
   Output : Spending comparison (MedD, Medi-Cal, Commercial)  
5) Analysis question : How many licensed with active status pharmacies are in each ZIP code?   
   Business purpose : Assess local competition.  
   Output : Current active licensed pharmacy counts.   
6) What is the ratio of prescribers to pharmacies per CA Zip code?  
   Business purpose : Understand market opportunity  
   Output : Rxers to pharmacy ratio. 

|  Start-up Pharmacy Location Guide |  |
| :---- | ----- |

| 03 |
| :---- |

### **Data** 

### **Pipeline Strategy**

#### 

#### **DataSets** 

Requirements : At least one dataset must be \>= 2GB and/or \>= 1M records. 

| Dataset  | Name and link | Method | Requirements | Analysis question |
| :---- | :---- | :---- | :---- | :---- |
| 1 | CMS NPPES [NPPES Data Dissemination V.2 (November 02, 2026\)](https://download.cms.gov/nppes/NPPES_Data_Dissemination_November_2026_V2.zip) Use npidata\_pfile\_\*.csv | CSV/ZIP (pyspark) | Yes | 3, 6 |
| 2 | NUCC Healthcare Provider Taxonomy [Version 26.1 7/1/26](https://nucc.org/images/stories/CSV/nucc_taxonomy_261.csv) | CSV Download | No | 2, 3 |
| 3 | Medicare Part D Prescriber \- by provider  2024 API  [https\://data.cms.gov/data-api/v1/dataset/14d8e8a9-7e9b-4370-a044-bf97c46b4b44/data](https://data.cms.gov/data-api/v1/dataset/14d8e8a9-7e9b-4370-a044-bf97c46b4b44/data) Link [https\://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider](https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider) | CSV | Yes | 1, 2, 4 |
| 4 | California DCA Pharmacy License [https\://www\.dca.ca.gov/consumers/public\_info/index.html](https://www.dca.ca.gov/consumers/public_info/index.html) | CSV or XLSX | No | 5, 6 |
| 5 | CMS SDUD 2024 API ​/api​/1​/metastore​/schemas​/dataset​/items​/61729e5a-7aa8-448c-8903-ba3e0cd0ea3c link [https\://data.medicaid.gov/dataset/61729e5a-7aa8-448c-8903-ba3e0cd0ea3c?](https://data.medicaid.gov/dataset/61729e5a-7aa8-448c-8903-ba3e0cd0ea3c?)  | CSV | Yes | 4 |
| 6 | Medicaid Drug Rebate Program 2024 API ​/api​/1​/metastore​/schemas​/dataset​/items​/0ad65fe5-3ad3-5d79-a3f9-7893ded7963a link  [https\://data.medicaid.gov/dataset/0ad65fe5-3ad3-5d79-a3f9-7893ded7963a?](https://data.medicaid.gov/dataset/0ad65fe5-3ad3-5d79-a3f9-7893ded7963a?)  | CSV | Yes | 1, 4 |
| 7 | DHCS Medi-Cal Eligible Counts Link [https\://publish.data.ca.gov/dataset/medi-cal-certified-eligible-counts-by-month-of-eligibility-zip-code-and-sex/resource/791a9aeb-3a9e-4b90-b249-e7e2ad3768f6](https://publish.data.ca.gov/dataset/medi-cal-certified-eligible-counts-by-month-of-eligibility-zip-code-and-sex/resource/791a9aeb-3a9e-4b90-b249-e7e2ad3768f6) Link to CSV [https\://s3.amazonaws.com/og-production-open-data-chelseama-892364687672/resources/55391885-76d4-4fa1-9af8-74844386fc48/oct2026\_productionsecondsheet\_2020\_recent.csv?X-Amz-Algorithm=AWS4-HMAC-SHA256\&X-Amz-Credential=AKIAJJIENTAPKHZMIPXQ%2F20261104%2Fus-east-1%2Fs3%2Faws4\_request\&X-Amz-Date=20261104T063256Z\&X-Amz-Expires=86400\&X-Amz-SignedHeaders=host\&X-Amz-Signature=7af9d22f2c2cab30054ba1bb92137c50ff74c317092e40529ef7129001beba08](https://s3.amazonaws.com/og-production-open-data-chelseama-892364687672/resources/55391885-76d4-4fa1-9af8-74844386fc48/oct2026_productionsecondsheet_2020_recent.csv?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAJJIENTAPKHZMIPXQ%2F20261104%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20261104T063256Z&X-Amz-Expires=86400&X-Amz-SignedHeaders=host&X-Amz-Signature=7af9d22f2c2cab30054ba1bb92137c50ff74c317092e40529ef7129001beba08)  | CSV | Verify | 4 |
| 8 | U.S Census ACS 2024 link  [https\://www\.census.gov/data/developers/data-sets/acs-5year.html?](https://www.census.gov/data/developers/data-sets/acs-5year.html?) | CSV | Verify | 4 |
| 9 | CA DMHC Prescription Drug Transparency Report 2024 Link  [https\://www\.dmhc.ca.gov/Portals/0/Docs/DO/SB172024Report.pdf?](https://www.dmhc.ca.gov/Portals/0/Docs/DO/SB172024Report.pdf?utm_source=chatgpt.com) | PDF  | Verify | 4 |

#### ![][image1]**![][image2]**

#### 

#### **Columns to extract and cleaning** 

| Dataset  | Name and link | Columns  | Expected\_output |
| :---- | :---- | :---- | :---- |
| 1 | CMS NPPES [NPPES Data Dissemination V.2 (November 02, 2026\)](https://download.cms.gov/nppes/NPPES_Data_Dissemination_November_2026_V2.zip) Use npidata\_pfile\_\*.csv | NPI,  Entity\_type,  practice-location state/ZIP, Taxonomy code 1-15, Primary Taxonomy switched 1-15,  Enumeration, Deactivation/reactivation dates  | dim\_prescriber  |
| Filter CA for unique individual NPI \- no duplicate  Extract ZIP5 Select primary taxonomy |  |  |  |
| 2 | NUCC Healthcare Provider Taxonomy [Version 26.1 7/1/26](https://nucc.org/images/stories/CSV/nucc_taxonomy_261.csv) | Code,  Grouping,  Classification,  Specialization,  Display name  | dim\_taxonomy |
| Select 2024 version \- if not, use the most current  Extract unique taxonomy codes.  |  |  |  |
| 3 | Medicare Part D Prescriber \- by provider  2024 API  [https\://data.cms.gov/data-api/v1/dataset/14d8e8a9-7e9b-4370-a044-bf97c46b4b44/data](https://data.cms.gov/data-api/v1/dataset/14d8e8a9-7e9b-4370-a044-bf97c46b4b44/data) Link [https\://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider](https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider) | Prscrb\_NPI, Prscrbr\_State\_Abrvtn, Prscrbr\_Zip5,  Prscrbr\_Type, Prscrbr\_Ent\_Cd, Tot\_Clms, Tot\_30day\_Fills, Tot\_Drug\_Cst, Brnd\_Tot\_Drug\_Cst, Gnrc\_Tot\_Drug\_Cst | fact\_partd\_provider |
| 2024 Filter CA  Normalize NPI and ZIP  Cast cost  Cast claims  |  |  |  |
| 4 | California DCA Pharmacy License [https\://www\.dca.ca.gov/consumers/public\_info/index.html](https://www.dca.ca.gov/consumers/public_info/index.html) | License\_type,  License\_number, Pharmacy\_name,  Organization\_type,  Physical\_address,  City, State, ZIP, License\_status, Original\_issue, Expiration\_dates,  | dim\_pharmacy |
| Filter CA Filter active \- license status  Remove pharmacies who have expired license.  Remove individual pharmacists/techs. Make sure to look at PHY  Remove duplicates |  |  |  |
| 5 | CMS SDUD 2024 API ​/api​/1​/metastore​/schemas​/dataset​/items​/61729e5a-7aa8-448c-8903-ba3e0cd0ea3c link [https\://data.medicaid.gov/dataset/61729e5a-7aa8-448c-8903-ba3e0cd0ea3c?](https://data.medicaid.gov/dataset/61729e5a-7aa8-448c-8903-ba3e0cd0ea3c?)  | State, Year, Quarter, Ndc, Utilization\_type,  Number\_of\_prescriptions, Total\_amount\_reimbursed,  Medicaid\_amount\_reimbursed,  Non-medicaid\_amount\_reimbursed, suppression\_used  | fact\_sdud\_ca\_2024 |
| Filter CA  2024 Separate FFS (fee-for-service meaning straight state medicaid)/MCO (managed care organization meaning private insurance handling state medicaid) Keep suppressed records |  |  |  |
| 6 | Medicaid Drug Rebate Program 2024 API ​/api​/1​/metastore​/schemas​/dataset​/items​/0ad65fe5-3ad3-5d79-a3f9-7893ded7963a link  [https\://data.medicaid.gov/dataset/0ad65fe5-3ad3-5d79-a3f9-7893ded7963a?](https://data.medicaid.gov/dataset/0ad65fe5-3ad3-5d79-a3f9-7893ded7963a?)  | Year, Quarter, NDC,  Drug\_Category,  Drug\_Type\_Indicator,  FDA\_Product\_Name | dim\_medicaid\_drug |
| 2024 Rx product Normalize NDC11 Keep quarterly drug category  |  |  |  |
| 7 | DHCS Medi-Cal Eligible Counts Link [https\://publish.data.ca.gov/dataset/medi-cal-certified-eligible-counts-by-month-of-eligibility-zip-code-and-sex/resource/791a9aeb-3a9e-4b90-b249-e7e2ad3768f6](https://publish.data.ca.gov/dataset/medi-cal-certified-eligible-counts-by-month-of-eligibility-zip-code-and-sex/resource/791a9aeb-3a9e-4b90-b249-e7e2ad3768f6) Link to CSV [https\://s3.amazonaws.com/og-production-open-data-chelseama-892364687672/resources/55391885-76d4-4fa1-9af8-74844386fc48/oct2026\_productionsecondsheet\_2020\_recent.csv?X-Amz-Algorithm=AWS4-HMAC-SHA256\&X-Amz-Credential=AKIAJJIENTAPKHZMIPXQ%2F20261104%2Fus-east-1%2Fs3%2Faws4\_request\&X-Amz-Date=20261104T063256Z\&X-Amz-Expires=86400\&X-Amz-SignedHeaders=host\&X-Amz-Signature=7af9d22f2c2cab30054ba1bb92137c50ff74c317092e40529ef7129001beba08](https://s3.amazonaws.com/og-production-open-data-chelseama-892364687672/resources/55391885-76d4-4fa1-9af8-74844386fc48/oct2026_productionsecondsheet_2020_recent.csv?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAJJIENTAPKHZMIPXQ%2F20261104%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20261104T063256Z&X-Amz-Expires=86400&X-Amz-SignedHeaders=host&X-Amz-Signature=7af9d22f2c2cab30054ba1bb92137c50ff74c317092e40529ef7129001beba08)  | ZIP, eligibility\_year,  Eligibility\_month,  Sex,  eligible\_count  | agg\_zip\_medical\_eligibility  |
| Filter 2024 Aggregate sex categories and by ZIP, month |  |  |  |
| 8 | U.S Census ACS 2024 link  [https\://www\.census.gov/data/developers/data-sets/acs-5year.html?](https://www.census.gov/data/developers/data-sets/acs-5year.html?) | ZCTA5,  Total\_population, Population\_aged\_18\_64, privately\_insured\_population\_by\_age | dim\_zip\_demographics |
| Get 5 years ZCTA estimates Counts private insurance. |  |  |  |
| 9 | CA DMHC Prescription Drug Transparency Report 2024 Link  [https\://www\.dmhc.ca.gov/Portals/0/Docs/DO/SB172024Report.pdf?](https://www.dmhc.ca.gov/Portals/0/Docs/DO/SB172024Report.pdf?utm_source=chatgpt.com) | 2024 reported drug\_spending,  covered-plan/member definitions, permember spending  | Commercial\_benchmark |
| Get reported drug spending  Get covered population |  |  |  |

#### 

#### 

#### 

#### **Join plan and PySpark operations**

| Research question  (Please use variable name such as rq1, rq2 etc. )  | Join plan  | PySpark operation |
| :---- | :---- | :---- |
| 1 : Brand vs Generics Drug Spending per ZIP Filter CA  Group by ZIP | D3 (No join needed) Calculation Brand % \= summation of brand cost / summation of total drug cost \* 100  Generics % \= summation of generics cost / summation of total drug cost \* 100 | filter() map() reduceByKey() mapValues() |
| 2 : Annual & monthly rx volume.  | D1 left join D2  primary\_taxonomy\_code \= taxonomy\_code  M : 1  D3 left join  on NPI  1:1 Calculation  Annual Rx \= summation of total claims  Monthly Rx \= annual rx / 12 Avg \= annual Rx / unique part D NPIs.  | map() join() reduceByKey() mapValues()  |
| 3 : Prescriber counts and specialties  | D1 left join D2 Primary\_taxonomy\_code \= taxonomy\_code M:1 Calculation  Prescribers \= count(unique NPI) Prescriber Share \= Prescribers in ZIP / Total CA Prescriber counts \* 100  | filter() map() distinct() leftOuterJoin() reduceByKey()  |
| 4 : Medicare, Medicaid and commercial prescription spending  | D3 : Medicare spending by prescriber ZIP  D5 \+ D7 : Estimated Medicaid Spending by ZIP 1:M D8 \+ D9 : Estimated Commercial Spending by ZIP 1:M  | filter() map() reduceByKey() mapValues() join() leftOuterJoin()  |
| 5 : Number of active pharmacies  | D4 : CA DCA licensing   | filter() map() reduceByKey() distinct() |
| 6 : Prescriber to pharmacy ratio | Aggregated D1 \+ aggregated D4  Join on ZIP5 1:1  Ratio \= prescribers / pharmacies    | map() fullOuterJoin() mapValues() |

|  Start-up Pharmacy Location Guide |  |
| :---- | ----- |

Here is the data schema with examples for each of the eight data sources we are including in this project.  Note that rows and columns are listed on the top left of each spreadsheet tab.

**File Summary:**  
![][image3]  
**MDRP:**  
![][image4]

**Pharmacy Counts:**  
![][image5]  
**NUCC Health Care Provider Codes:**  
![][image6]

**Medi-Cal Certified Eligibles:**  
![][image7]

**CMS State Drug Utilization:**  
![][image8]

**Medicare Part D Prescribers:**  
![][image9]

**NPPES Data Dissemination:**  
![][image10]

**NPPES Data Dissemination, Secondary Practice Locations:**  
![][image11]

**NPPES Electronic Endpoints:**  
![][image12]

**NPPES Other Organization Names:**  
![][image13]

|  Start-up Pharmacy Location Guide |  |
| :---- | ----- |

| 05 |
| :---- |

### **Team Plan** 

Meetings :  Monday & Thursday : 12-1 in person at 101 Howard.  
Friday : 2-3pm in person at 101 Howard. 

|  | Member |  | Research Question |  | Initials and Dates |
| ----- | ----- | :---- | :---- | :---- | :---- |
|  |  |  |  |  |  |
|  | Mary Kim  |  | Q4 (Medicaid, Medicare) |  |  |
|  | Jonah Robinson |   | Q1 , Q5 |  |  |
|  | Thomas Sasser |  | Q4 (Medicare, Commercial) |  |  |
|  | Hersh Rudrawal |  | Q2 , Q6 |  |  |
|  | Narayan Poudel |  | Q3 , Q6 |  |   |

|  Start-up Pharmacy Location Guide |  |
| :---- | ----- |

| 06 |
| :---- |

# 

| Mary Kim |  |  |
| :---- | :---- | ----- |
|  |  |  |
| **Week ( 11 / 6 \- 11 / 9 )** | Locate and retrieve CMS SDUD 2024 DHCS Medi-Cal eligible counts. D6 Drug products.   Raw Medicaid files, Counts, log, Schema   |  |
|  |  |  |
|   |  |  |
| **Week (11 / 10 \- 11 / 16\)** |  SDUD / DHCS load with RDDs.  Filter CA, 2024 Normalize ZIP Extract reimbursement, eligibility values Suppression Medicaid and eligibility RDD  |  |
|   |  |  |
| **Week (11 / 17 \- 11 / 23\)** |  One statewide medicaid spending and ZIP eligibility weights.  Join SDUD \+ D6 NDC/year/quarter.  Medicaid spending, eligibility weights, NDC join.   |  |
|  |  |  |
| **Week (11 / 24 \- 11 / 30\)** | Medicaid 2024 spending calculation Allocation by ZIP \+ medicaid shares. Medicaid spending estimates calculation by ZIP   |  |
|  |  |  |
| **Week (12 / 1 \- 12 / 7\)** |  Validation on medicaid spending estimates and geo distribution.  Notes on limitation and methodology.  Medicaid charts and findings.   |  |
|  |  |  |
| **Week (12 / 8 \- 12 / 14\)** |  Write methodology on calculation, suppression strategy, eligibility allocation.  Write report.   |  |
|  |  |  |
| **Week (12 / 15 \- 12 / 16\)** |  Rerun the results and finalize methodology and limitations.  Final Medicaid RQ4.   |  |
| Thomas Sasser |  |  |
|  |  |  |
|  |  |  |
| **Week ( 11 / 6 \- 11 / 9 )** | Locate 2024 DMHC prescription drug report Locate Census ACS 2024 report for private insurance data. Eligible commerical spending measures.   Commercial notes, ACS fields.   |  |
|  |  |  |
|   |  |  |
| **Week (11 / 10 \- 11 / 16\)** |  DHMC commercial spending and  Privately insured population by ZCTA with ACS data.   Commercial and cleaned population RDD  |  |
|   |  |  |
| **Week (11 / 17 \- 11 / 23\)** |  Commercial spending amount \+ ZIP/ZCTA insurance weights Coverage and geo match verification Commercial allocation calculation validation and QA report.   |  |
|  |  |  |
| **Week (11 / 24 \- 11 / 30\)** | Calculate commercial ZIP spending estimates per the formula.  Combine Med D, Medicaid, and commercial results with RDD joins.  RQ4 three \- payer table for spending.   |  |
|  |  |  |
| **Week (12 / 1 \- 12 / 7\)** |  Combine all ZIP level RDD outputs and generate ZIP selector, spending chart and comparison tables.  Interactive dashboard  |  |
|  |  |  |
| **Week (12 / 8 \- 12 / 14\)** |  Validate commercial methodology with Mary. Compile report. Report draft.   |  |
|  |  |  |
| **Week (12 / 15 \- 12 / 16\)** |  Repo check and report submission Final report and QA.   |  |
|  |  |  |
|  |  |  |

| Jonah Robinson |  |  |
| :---- | ----- | ----- |

|  |  |  |
| :---- | :---- | ----- |
| **Week ( 11 / 6 \- 11 / 9 )** | Locate Medicare Part D by Provider(2024) Locate CA DCA pharmacy license data Verify brand spending, generics spending columns Verify license category  Raw Medicare Part D Raw DCA file  Column and size   |  |
|  |  |  |
|   |  |  |
| **Week (11 / 10 \- 11 / 16\)** |  Clean Part D Bx/Gx spending Clean DCA file Filter CA, active licensed pharmacy, Zip code  Cleaned Part D spending and pharmacies  |  |
|   |  |  |
| **Week (11 / 17 \- 11 / 23\)** |  Aggregate Med D brand/generic spending.  Count pharmacy by ZIP. Unique pharmacies  RQ1 spending amount RDD  RQ5 pharmacy counts RDD   |  |
|  |  |  |
| **Week (11 / 24 \- 11 / 30\)** | Calculate Bx/Gx spending % Annual total drug costs Unique active pharmacy counts by ZIP  Completed RQ1, RQ5 metrics. Medicare Spending for RQ 4\. Hand out Medicare Spending to Tom.   |  |
|  |  |  |
| **Week (12 / 1 \- 12 / 7\)** |  Bx/Gx charts Pharmacy counts,  Medicare spending results for dashboard usage later.  RQ1, RQ5 final usage ready files.   |  |
|  |  |  |
| **Week (12 / 8 \- 12 / 14\)** |  Bx/Gx drug costs against Part D totals Double check active pharmacy counts and license filters.  RQ1, RQ5 report findings.   |  |
|  |  |  |
| **Week (12 / 15 \- 12 / 16\)** |  Finalize Bx/Gx spending.  Medicare spending Active pharmacies.  RQ1, RQ5 and Med D for RQ4 files.    |  |
|  |  |  |

Hersh Rudrawal

|  |  |  |
| :---- | :---- | ----- |
| **Week ( 11 / 6 \- 11 / 9 )** | Explore Med D tot\_clms, Tot\_30day\_Fills, NPI, and ZIP.  Rx volume check, RDD plan.    |  |
|  |  |  |
|   |  |  |
| **Week (11 / 10 \- 11 / 16\)** |  Clean prescription claims, 30 days fills,  NPI and ZIP codes From Med D. Cleaned prescription volume RDD  |  |
|   |  |  |
| **Week (11 / 17 \- 11 / 23\)** |  Join Part D rx records with provider specialty RDD using NPI (from Narayan) NPI join RDD, handle unmatched NPI   |  |
|  |  |  |
| **Week (11 / 24 \- 11 / 30\)** | Calculate annual Rxs claims, Monthly, 30 days fills, and Average rx ratio to provider.  Combine prescribers and pharmacy ZIP counts for RQ6.  RQ2 prescription volume. RQ6 ratio   |  |
|  |  |  |
| **Week (12 / 1 \- 12 / 7\)** |  Prepare annual/monthly volume for visualization.  Double check prescribers to pharmacy ratio.  RQ2, RQ6 dashboard ready output.  |  |
|  |  |  |
| **Week (12 / 8 \- 12 / 14\)** |  Reconcile total annual claims \+ fills.  Check RQ 6 zero- pharmacy and joins.  RQ2, RQ6 validation and report.  |  |
|  |  |  |
| **Week (12 / 15 \- 12 / 16\)** |  Calculator for rx volumes and prescribers to pharmacy ratio.  Final RQ2, RQ6 files.   |  |
|   |  |  |

Narayan Poudel

|  |  |  |
| :---- | :---- | ----- |
| **Week ( 11 / 6 \- 11 / 9 )** | Locate NPPES and NUCC 2024 taxonomy.  Understand primary taxonomy and CA ZIP for practices.  Raw NPPES/NUCC fields check. Row counts.   |  |
|  |  |  |
|   |  |  |
| **Week (11 / 10 \- 11 / 16\)** |  NPPES filter for CA and individual providers.  Primary taxonomy  Normalize ZIP and NPI Clean NUCC specialty codes.  Clean provider and taxonomy RDD   |  |
|   |  |  |
| **Week (11 / 17 \- 11 / 23\)** |  Join NPPES with NUCC on primary taxonomy code.  Double check one row per NPI and get provider counts for hand out to Hersh.  Provider and specialty RDD Join validation check   |  |
|  |  |  |
| **Week (11 / 24 \- 11 / 30\)** | Calculate unique/distinct prescribers per Zip per specialty and CA.  Check the calculation for the denominator RQ6.  Completed RQ3, provider counts check.  |  |
|  |  |  |
| **Week (12 / 1 \- 12 / 7\)** |  Prepare provider distribution charts, cross check with RQ 6 provider counts.  RQ3 visualization ready provider table.   |  |
|  |  |  |
| **Week (12 / 8 \- 12 / 14\)** |  Check unique NPI counts, primary specialty, prescriber definition and NPPES date.  Write QA findings for report.   |  |
|  |  |  |
| **Week (12 / 15 \- 12 / 16\)** |  Finalize provider and specialty counts. Check for consistency with RQ6.  Final RQ3 and provider side of RQ6 file.   |  |
|  |  |  |
