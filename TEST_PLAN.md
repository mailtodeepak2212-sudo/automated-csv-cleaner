# Test Plan

This document defines the unit, validation, imputation, and integration tests for the Automated CSV Cleaner project. Tests must verify the agreed requirements in `DATA_POLICY.md` and the function interfaces in `INTERFACE_CONTRACTS.md`.

## 1. Test strategy

- Use `pytest` as the test framework.
- Keep unit tests focused on one function or module behavior at a time.
- Use the shared sample input at `data/raw/messy_data.csv` for end-to-end pipeline tests.
- Use small, purpose-built DataFrames for edge cases that are not present in the sample CSV.
- Do not change a test merely to make incorrect implementation pass. First determine whether the implementation, test expectation, or agreed requirement is wrong.

## 2. Cleaning tests — `tests/test_cleaning.py`

Verify that `clean_data(df)`:
- Removes leading and trailing whitespace from text values.
- Standardizes capitalization consistently (for example, city names use title case).
- Removes duplicate rows after text standardization.
- Does not modify the input DataFrame in place.
- Returns exactly two values: a cleaned Pandas DataFrame and a statistics dictionary.
- Reports the input row count and number of duplicates removed accurately.

**Sample CSV expectation:** 8 input rows become 6 rows after standardization and duplicate removal; 2 duplicates are removed.

## 3. Validation tests — `tests/test_validation.py`

Verify that `validate_data(df)`:
- Accepts valid records.
- Rejects non-numeric Age values such as `twenty`.
- Rejects Age values below 0 and above 120.
- Rejects non-numeric Salary values such as `abc`.
- Rejects negative Salary values.
- Keeps genuinely missing Age and Salary values eligible for imputation rather than rejecting them automatically.
- Distinguishes originally missing values from non-empty values that fail numeric conversion.
- Preserves rejected records and includes a useful `rejection_reason`.
- Returns exactly three values: valid DataFrame, rejected DataFrame, and validation statistics.
- Reports valid and rejected counts accurately.

**Sample CSV expectation:** 3 valid records and 3 rejected records after duplicate removal.

## 4. Imputation tests

Verify that `impute_missing_values(valid_df)`:
- Replaces genuinely missing Age values with the median of valid numeric ages.
- Replaces genuinely missing Salary values with the median of valid, non-negative numeric salaries.
- Does not impute invalid text values such as `twenty` or `abc`; validation must reject those records first.
- Handles a column with no missing values without changing its values.
- Handles the case where no usable median can be calculated according to an explicitly agreed behavior.
- Does not modify the input DataFrame in place.
- Returns exactly two values: the imputed DataFrame and an imputation statistics dictionary.
- Reports Age and Salary imputation counts and medians accurately.

**Sample CSV expectation:** Priya's missing Age is imputed to 23, the median of valid ages 21 and 25. No Salary value is missing in the sample CSV, so no Salary imputation should occur.

## 5. Reporting tests

Verify that `generate_report(cleaning_stats, validation_stats, imputation_stats)`:
- Returns a report dictionary.
- Includes input rows, duplicates removed, valid rows, rejected rows, and imputation counts.
- Uses the statistics supplied by the processing stages.
- Does not report counts that contradict the actual output DataFrames.

## 6. Integration tests — `tests/test_pipeline.py`

Run the complete pipeline using `data/raw/messy_data.csv` and verify:
1. The input file is read successfully.
2. Text standardization occurs before duplicate removal.
3. Two duplicate rows are removed.
4. Validation produces 3 valid and 3 rejected records.
5. The genuinely missing Age in the valid Priya record is imputed to 23.
6. Invalid records are not imputed or silently discarded.
7. Cleaned output is saved under `data/cleaned/`.
8. Rejected output is saved under `data/rejected/`.
9. The processing report is saved under `reports/`.
10. The report counts match the actual output files.

### Count reconciliation

After duplicate removal, every remaining row must be accounted for as valid or rejected:

```text
input_rows = duplicates_removed + valid_rows + rejected_rows
8 = 2 + 3 + 3
```

The test should fail if a row is lost or counted more than once.

## 7. Error-handling tests

Verify sensible behavior for:
- Missing input file (`FileNotFoundError`).
- Empty CSV file.
- Malformed CSV input.
- Output directory or file permission errors.
- A dataset with no rows after cleaning.
- Missing or unexpected required columns, according to the pipeline's agreed input-schema behavior.

Errors should be clear and should not leave misleading success reports or silently overwrite the raw input.

## 8. Acceptance criteria before merging a Pull Request

A feature is ready for review when:
- Its implementation follows `INTERFACE_CONTRACTS.md`.
- Relevant unit tests are included or updated.
- Tests pass locally.
- No raw input data is overwritten.
- Rejected records are preserved with reasons.
- Statistics match the actual results.
- The Pull Request targets `main` from the correct feature branch.
- The reviewer has inspected the changed files and test results.

The complete project is ready for release only when the full test suite passes and the end-to-end pipeline produces the expected outputs.
