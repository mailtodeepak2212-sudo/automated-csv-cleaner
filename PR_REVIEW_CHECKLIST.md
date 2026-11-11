# Pull Request Review Checklist

Use this checklist when reviewing a teammate's Pull Request before merging it into `main`.

## 1. Pull Request configuration

- [ ] Base branch is `main`.
- [ ] Compare branch is the correct feature branch.
- [ ] PR title clearly describes the change.
- [ ] PR description explains what changed and how it was tested.
- [ ] The PR is not an accidental duplicate or empty PR.

## 2. Scope and changed files

- [ ] Every changed file is relevant to the PR.
- [ ] Changes stay within the teammate's assigned responsibility, or any additional changes are explained and agreed.
- [ ] No unrelated files, generated files, temporary files, credentials, or local environment files were added.
- [ ] Raw input data under `data/raw/` has not been overwritten or modified unintentionally.

## 3. Interface contract compliance

- [ ] Function names and parameter order match `INTERFACE_CONTRACTS.md`.
- [ ] Inputs and return values have the agreed types.
- [ ] Return-value count and order match the contract.
- [ ] Statistics are returned in dictionaries as agreed.
- [ ] Functions do not unexpectedly mutate caller-owned DataFrames.
- [ ] Any proposed contract change is discussed and documented before merging.

## 4. Data policy compliance

- [ ] Text whitespace and capitalization are standardized consistently.
- [ ] Duplicate rows are removed after text standardization.
- [ ] Originally missing values are distinguished from failed numeric conversions.
- [ ] Invalid Age values (non-numeric, below 0, or above 120) are rejected.
- [ ] Invalid Salary values (non-numeric or negative) are rejected.
- [ ] Genuine missing Age and Salary values in valid records remain eligible for median imputation.
- [ ] Invalid records are preserved with a useful `rejection_reason`.
- [ ] The original raw CSV remains unchanged.

## 5. Tests and quality

- [ ] Relevant unit tests are included or updated.
- [ ] Tests cover normal cases and important edge cases.
- [ ] The full test suite has been run.
- [ ] All test failures have been investigated and resolved or explicitly explained and agreed before merge.
- [ ] Code is readable and reasonably maintainable.
- [ ] No debugging statements or accidental hard-coded local paths remain.

## 6. Outputs and reporting

- [ ] Cleaned output is saved under `data/cleaned/`.
- [ ] Rejected output is saved under `data/rejected/`.
- [ ] Reports are saved under `reports/`.
- [ ] Report statistics match the actual output files.
- [ ] Count reconciliation holds:

```text
input_rows = duplicates_removed + valid_rows + rejected_rows
```

## 7. Review decision

- [ ] All review comments are resolved.
- [ ] Required changes have been completed and pushed.
- [ ] The final diff has been inspected after the latest updates.
- [ ] The PR is ready to merge into `main`.

**Do not merge solely because GitHub displays a green mergeable status.** Confirm that the changes satisfy the project requirements and tests.
