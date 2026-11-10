# Automated CSV Cleaner

A Python project that reads a raw CSV, standardizes and cleans its data, validates records, saves rejected rows separately, and produces a JSON cleaning report.

## Project structure

```text
automated-csv-cleaner/
├── src/
│   ├── __init__.py
│   ├── cleaning.py
│   ├── validation.py
│   ├── reporting.py
│   ├── pipeline.py
│   └── config.py
├── data/
│   ├── raw/
│   └── cleaned/
├── reports/
├── tests/
│   ├── test_cleaning.py
│   ├── test_validation.py
│   └── test_pipeline.py
├── main.py
├── requirements.txt
├── .gitignore
└── README.md
```

## Setup

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Run

```bash
python main.py
```

## Tests

```bash
pytest
```

## Team ownership

- **Member 1:** `src/cleaning.py` and `tests/test_cleaning.py`
- **Member 2:** `src/validation.py`, `src/reporting.py`, and their tests
- **Project lead:** `src/pipeline.py`, `src/config.py`, `main.py`, and integration tests

## Development status

This repository currently contains the initial project scaffold. Function contracts, cleaning rules, and implementation will be agreed upon before feature development begins.
