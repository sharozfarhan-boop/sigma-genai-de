# Data Pipeline Design Document

## What This Pipeline Does
This pipeline ingests transaction data, enriches it with merchant information, and computes merchant performance and daily transaction summaries. It stores the data in a DuckDB database, progressing from raw (bronze) to enriched (silver) to aggregated (gold) layers.

## Data Flow Diagram
```
+----------------+      +------------------+      +------------------+
| TRANSACTIONS   | ---> | bronze_transactions | ---> | silver_transactions |
+----------------+      +------------------+      +------------------+
                                                             |
                                                             |
                                                             |
                                                             V
                                                       +--------------------+
                                                       | merchants          |
                                                       +--------------------+
                                                             |
                                                             |
                                                             |
                                                             V
                                                       +--------------------+
                                                       | silver_transactions |
                                                       +--------------------+
                                                             |
                                                             |
                                                             |
                                                             V
                                                       +--------------------+
                                                       | gold_merchant_performance |
                                                       +--------------------+
                                                             |
                                                             |
                                                             |
                                                             V
                                                       +--------------------+
                                                       | gold_daily_summary   |
                                                       +--------------------+
```

## Key Design Decisions
- **Layered Data Storage**: The pipeline uses a three-layer approach (bronze, silver, gold) to ensure data quality and usability at each stage.
- **Data Enrichment**: Merchant information is joined with transaction data in the silver layer to provide context.
- **Aggregation at Gold Layer**: The pipeline performs aggregations and calculations at the gold layer to provide insights and summaries.
- **Error Handling**: The pipeline includes error handling to skip transactions with negative amounts and duplicate transaction IDs.

## Known Limitations
- **Data Freshness**: The pipeline runs once daily, which may not capture real-time insights.
- **Error Handling**: The pipeline does not handle all possible data anomalies, such as missing merchant IDs.
- **Data Volume**: The pipeline is not optimized for very large datasets, which could impact performance.
- **Schema Changes**: The pipeline does not handle schema changes in the source data, which could cause failures.

## Dependencies
- **DuckDB Database**: The pipeline requires a DuckDB database to store and process data.
- **MERCHANTS Data**: The pipeline needs a list of merchants with their details to enrich transaction data.
- **TRANSACTIONS Data**: The pipeline requires clean and dirty transaction data to process.