# RQ2: prescription volume by ZIP

`rq2_data.csv`: 1,432 rows, one per California ZIP code.

Generated Nov 23, 2026.

## Data

The number of Medicare Part D prescription claims written in 2024 by prescribers whose practice address is in each ZIP code.

It is **not** the number of prescriptions filled in that ZIP. The ZIP is where the prescriber practices, not where the patient lives or where the prescription was dispensed. It also covers Medicare Part D only, so Medicaid, commercial insurance and cash payers are absent.

## Columns

| Column | Type | Meaning |
| --- | --- | --- |
| `zip` | **text** | Five-digit ZIP of the prescriber's practice address |
| `prescribers` | integer | Distinct prescribers with Part D claims in this ZIP |
| `annual_claims` | integer | Total 2024 Part D claims, refills included |
| `annual_fills` | decimal | Total standardized 30-day fills |
| `monthly_claims` | decimal | `annual_claims / 12` |
| `claims_per_prescriber` | decimal | `annual_claims / prescribers` |
| `monthly_fills` | decimal | `annual_fills / 12` |
| `fills_per_prescriber` | decimal | `annual_fills / prescribers` |

## Notes

- **All non-integer columns are rounded to 2 decimal places.** Don't round again at a different precision, or the dashboard and the report will disagree.

## Claims vs 30-day fills

A **claim** is one prescription filled, refills counted separately. A **30-day fill** restates that as a 30-day supply, so a 90-day prescription is one claim but three fills. They are different measures of the same activity. Claims is the headline figure for RQ2; fills is reported alongside it.

`monthly_claims` is simply the annual figure divided by twelve. The source data is annual, so it says nothing about seasonality.

## Source

[CMS Medicare Part D Prescribers — by Provider, 2024](https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider). One row per prescriber, 1,416,883 rows and 84 columns nationwide.

## How it was built

| Stage | Rows | Removed |
| --- | --- | --- |
| Raw file, nationwide | 1,416,883 | — |
| Columns reduced (84 → 12) | 1,416,883 | 0 |
| California only | 143,204 | 1,273,679 |
| Out-of-state ZIPs removed | 143,180 | 24 |
| Grouped to ZIP | **1,432** | — |

The 24 removed rows had a state of CA but a ZIP outside California. Both fields come from NPPES and the file cannot say which is stale, so the ZIP was treated as authoritative.

Verified during the build: one row per prescriber with no duplicate NPIs, every remaining row falling in exactly one ZIP group, and ZIP-level totals reconciling to the California totals for both claims and fills.

## What is missing from the data

**Low Prescribers per ZIPs.** Many ZIPs have a single prescriber.

**Low-volume prescribers dropped.** Any prescriber with fewer than 11 total claims, are removed from the dataset. A ZIP showing one prescriber may have several who fall below the threshold.

**Some values are blanked.** CMS suppresses individual values between 1 and 10. This affects `Tot_Benes` (12% blank) and the brand and generic cost columns (43% and 3%), none of which are used in this output. `Tot_Clms` is never blank, because those rows are dropped rather than blanked.
