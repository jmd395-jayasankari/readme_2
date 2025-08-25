# Slowly Changing Dimension Type 2 (SCD-2)
<p align="center">
  
</p>

*“Our Ready-to-Use SCD-2 Framework”*

The SCD-2 logic in this framework enables tracking historical changes in staging tables while preserving previous versions of records. This ensures accurate auditing and historical reporting.

## Overview

**Purpose:**
- Manage incremental updates to staging tables while preserving historical data.

**Scope:**
- Only tables and columns defined in the configuration file are processed.

**Metadata_columns:**

- is_active – indicates if the record is the current active version.

- effective_from_date – start date of record validity.

- effective_to_date – end date of record validity.

## What Users Need to Configure
- **Tables**: Only include tables that require SCD-2 processing.

- **Columns**: Specify the columns you want tracked. The framework ignores irrelevant columns.

- **Primary Key & Incremental Column**: Ensure these are correctly defined to detect changes accurately.

No changes to the underlying code are needed—the framework handles all SCD-2 logic automatically.



## Utility Functions
A set of helper methods for table validation, column selection, hashing, key generation, and SCD metadata management.

| Function                           | Purpose                                                              |
|--------------------------------    |-----------------------------------------------------------------------------|
| `table_exists(..)`                 | Checks if a Delta table exists in the lakehouse                      |
| `fetch_incremental_records(...)`   | Retrieves records newer than the last loaded timestamp               |
| `fetch_deleted_records(...)`       | Retrieves records that were soft deleted after the last known delete | 
| `select_columns(...)`              | Selects a fixed set of columns from a DataFrame                      |
| `add_hash_col(...)`                | Adds a row_check_sum column for change detection                     |
| `generate_primary_key(...)`        | Builds a single key for both simple and composite primary keys       |
| `add_scd_columns(...)`             | Adds SCD2 metadata columns like _is_active, _effective_from_date,etc |


## Logging and Monitoring
Each processing run logs:

- Type of operation (Full Load, Incremental, SCD)

- Source and sink metadata

- Row counts for new, updated, deleted, and inactive rows

Status (Success, Fail, Skipped)

- Timestamp and descriptive message

The logs are written to a Fabric Lakehouse table: monitor.staging_log



