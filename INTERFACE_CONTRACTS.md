# Interface Contracts

This document defines the agreed function interfaces for the Automated CSV Cleaner. Implementations must follow these contracts so modules can be integrated without return-value or data-type mismatches.

## 1. Data flow

`main.py` orchestrates the pipeline. It should call module functions rather than contain cleaning, validation, or reporting logic itself.

Expected order:

1. Read the raw CSV into a Pandas DataFrame.
2. Call `clean_data(df)`.
3. Call `validate_data(cleaned_df)` to separate valid and rejected records.
4. Call `impute_missing_values(valid_df)` on valid records only.
5. Call `generate_report(...)` with the statistics from the preceding stages.
6. Save cleaned data, rejected data, and the report to their designated output folders.

## 2. Member 1 — `src/cleaning.py`

### `clean_data(df)`

**Input:** A Pandas DataFrame.

**Responsibilities:**
- Do not modify the caller's DataFrame in place; work on a copy.
- Strip leading and trailing whitespace from text columns.
- Standardize text capitalization consistently (city names should use title case).
- Remove duplicate rows after text standardization.

**Returns:** A two-item tuple:
1. `cleaned_df`: the standardized DataFrame with duplicates removed.
2. `cleaning_stats`: a dictionary containing at least `input_rows` and `duplicates_removed`.

### `impute_missing_values(valid_df)`

**Input:** A DataFrame containing records that passed validation.

**Responsibilities:**
- Impute genuinely missing Age values using the median of valid, numeric ages.
- Do not impute invalid text values such as `twenty`; those must already have been rejected by validation.
- Work on a copy rather than modifying the caller's DataFrame in place.

**Returns:** A two-item tuple:
1. `imputed_df`: the DataFrame with eligible missing Age values imputed.
2. `imputation_stats`: a dictionary containing at least `ages_imputed` and the median used (if one was available).

## 3. Member 2 — `src/validation.py`

### `validate_data(df)`

**Input:** A standardized Pandas DataFrame returned by `clean_data`.

**Responsibilities:**
- Separate valid records from rejected records.
- Reject non-numeric Age values such as `twenty`.
- Reject Age values below 0 or above 120.
- Reject negative Salary values.
- Preserve rejected rows and add a `rejection_reason` field explaining why each row was rejected.
- Distinguish an originally missing Age from a non-empty value that fails numeric conversion. Do not treat both as ordinary missing values.

**Returns:** A three-item tuple:
1. `valid_df`: records that pass validation; genuinely missing Age values may remain missing for the imputation stage.
2. `rejected_df`: rejected records, including their `rejection_reason`.
3. `validation_stats`: a dictionary containing at least `valid_rows` and `rejected_rows`, plus useful rejection counts.

## 4. Member 2 — `src/reporting.py`

### `generate_report(cleaning_stats, validation_stats, imputation_stats)`

**Inputs:** The three statistics dictionaries returned by the cleaning, validation, and imputation stages.

**Responsibilities:**
- Combine the stage statistics into one clear report structure.
- Include input, duplicate, valid, rejected, and imputed-row counts.
- Keep the report data consistent with the actual output DataFrames.

**Returns:** A dictionary representing the report. The pipeline is responsible for saving it under `reports/` unless the team agrees on a separate save function.

## 5. Pipeline owner — `src/pipeline.py` and `main.py`

The pipeline coordinates the functions in this order:

```python
cleaned_df, cleaning_stats = clean_data(df)
valid_df, rejected_df, validation_stats = validate_data(cleaned_df)
imputed_df, imputation_stats = impute_missing_values(valid_df)
report = generate_report(
    cleaning_stats,
    validation_stats,
    imputation_stats,
)
```

The pipeline should return or save the cleaned DataFrame, rejected DataFrame, and report. `main.py` should invoke the pipeline and handle command-line or user-facing execution.

## 6. Shared interface rules

- Function names, parameter order, and return-value order must match this document.
- Use Pandas DataFrames for tabular inputs and outputs.
- Use dictionaries for statistics.
- Do not silently change a function's return shape. Discuss and update this document before changing a contract.
- Add or update unit tests when changing a function.
- Run the tests before opening a Pull Request.

## 7. Open policy question

The current data policy defines how to handle negative Salary values but does not yet define what to do when Salary is genuinely missing or contains non-numeric text. Agree on that rule before implementing Salary validation.
