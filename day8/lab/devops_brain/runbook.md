# Pipeline Overview

This pipeline processes transaction data, transforms it into a cleaned and enriched format, and computes merchant performance and daily summaries. It runs to ensure data is up-to-date for downstream analytics and reporting. If it stops, downstream reports and analytics will be outdated or incorrect.

## Pipeline Steps

1. Connect to the DuckDB database using `get_connection()`.
2. Set up required tables using `setup_tables()`.
3. Load merchant data into the `merchants` table using `load_merchants()`.
4. Load raw transactions into the `bronze_transactions` table using `load_bronze()`.
5. Transform raw transactions into cleaned and enriched data, loading into the `silver_transactions` table using `transform_bronze_to_silver()` and `load_silver()`.
6. Compute merchant performance metrics and load into the `gold_merchant_performance` table using `compute_merchant_performance()` and `load_gold()`.
7. Compute daily summary metrics and load into the `gold_daily_summary` table using `compute_daily_summary()` and `load_gold()`.

## Schedule / Trigger

This pipeline runs every night at 2 AM using a cron job.

## Failure Modes

1. **Database Connection Failure**
   - **Root Cause:** DuckDB service is down or DB file is corrupted.
   - **Symptom:** `get_connection()` fails.
2. **Table Creation Failure**
   - **Root Cause:** Syntax error in SQL or insufficient permissions.
   - **Symptom:** `setup_tables()` throws an error.
3. **Merchant Data Load Failure**
   - **Root Cause:** Corrupted or missing merchant data.
   - **Symptom:** `load_merchants()` fails to insert data.
4. **Bronze Table Load Failure**
   - **Root Cause:** Malformed transaction data or database error.
   - **Symptom:** `load_bronze()` fails to insert data.
5. **Silver Table Transformation Failure**
   - **Root Cause:** Inconsistent or missing merchant IDs in transactions.
   - **Symptom:** `transform_bronze_to_silver()` produces incorrect or incomplete data.

## Recovery Actions

1. **Database Connection Failure**
   - Check DuckDB service status.
   - Verify DB file integrity.
   - Restart DuckDB service if necessary.
2. **Table Creation Failure**
   - Review SQL syntax for errors.
   - Ensure the user has sufficient permissions.
   - Correct SQL and rerun `setup_tables()`.
3. **Merchant Data Load Failure**
   - Verify merchant data integrity.
   - Replace or repair corrupted data.
   - Rerun `load_merchants()`.
4. **Bronze Table Load Failure**
   - Inspect transaction data for errors.
   - Correct malformed data.
   - Rerun `load_bronze()`.
5. **Silver Table Transformation Failure**
   - Check for missing or inconsistent merchant IDs.
   - Ensure all transactions have valid merchant IDs.
   - Rerun `transform_bronze_to_silver()`.

## Known Bugs

- Hardcoded AWS credentials in the source code.
- Lack of null handling in `transform_bronze_to_silver()`.
- Inefficient use of `INSERT OR IGNORE` in `load_merchants()`.

## Escalation Contacts

1. **On-call DE:** Priya Nair (priya.nair@sigmadatatech.in, +91-98400-11111)
2. **Tech Lead:** Arjun Mehta (arjun.mehta@sigmadatatech.in)
3. **Platform Manager:** Kavya Reddy (kavya.reddy@sigmadatatech.in)

## Data Quality Checks

- Verify the number of records in `silver_transactions` matches expectations.
- Check `gold_merchant_performance` for accurate merchant metrics.
- Ensure `gold_daily_summary` reflects correct daily totals and unique counts.