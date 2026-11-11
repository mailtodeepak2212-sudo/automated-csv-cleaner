# Data Policy

This document defines the data-quality rules for the Automated CSV Cleaner project. All team members must follow these rules so that cleaning, validation, reporting, and tests behave consistently.

## 1. Input data

- Keep the original input CSV unchanged in `data/raw/`.
- Never overwrite the raw input file during cleaning.
- The pipeline should read the raw file and produce separate output files.

## 2. Text standardization

- Remove leading and trailing whitespace from text values.
- Standardize text capitalization consistently (for example, city names use title case).
- Apply text standardization before checking for duplicate rows.

## 3. Duplicate records

- Remove exact duplicate rows after text standardization.
- Record the number of duplicate rows removed in the cleaning statistics.
- Do not remove records merely because one field matches; duplicate detection should use the agreed row-level rule.

## 4. Age validation and missing values

- A genuinely empty age is missing data, not automatically invalid data.
- Convert age values to numeric values while tracking conversion failures separately from values that were originally empty.
- Reject non-empty values that cannot be converted to a number (for example, `twenty`).
- Reject ages below 0 or above 120.
- For records that pass validation, replace genuinely missing ages with the median of valid ages.
- Never impute a conversion failure such as `twenty`. It must remain a rejected record.

## 5. Salary validation

- Reject records with a negative salary.
- Do not silently change a negative salary to zero or another value.

## 6. Rejected records

- Preserve rejected records separately in `data/rejected/`.
- Include enough information to identify why each record was rejected (for example, an explanatory reason or validation error).
- Do not silently discard invalid records.

## 7. Output and reporting

- Save accepted, cleaned data separately under `data/cleaned/`.
- Save rejected records under `data/rejected/`.
- Save a processing report under `reports/`.
- Report useful counts, including input rows, duplicates removed, accepted rows, rejected rows, and imputed values.
- Ensure the reported counts are consistent with the actual output files.

## 8. Expected processing order

1. Read the raw CSV.
2. Standardize text values.
3. Identify and track numeric conversion failures separately from genuinely missing values.
4. Remove duplicate rows after text standardization.
5. Validate records and separate rejected records from valid records.
6. Impute genuinely missing ages in the valid records using the median of valid ages.
7. Save accepted and rejected outputs.
8. Generate the processing report.

## 9. Testing expectations

Tests should cover at least:
- Leading/trailing whitespace and inconsistent capitalization.
- Duplicate rows that become identical after standardization.
- A genuinely missing age.
- A non-numeric age such as `twenty`.
- An age below 0 and an age above 120.
- A negative salary.
- Correct accepted/rejected outputs and report statistics.
