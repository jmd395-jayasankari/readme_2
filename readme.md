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

## Prerequisites for SCD Type 2
Before running the SCD Type 2 pipeline, ensure the following prerequisites are met:


**1. Fabric Lakehouse Setup** 

- A working Fabric Lakehouse must be built and accessible.
- Raw and Staging layers must be defined.

**2.Metadata Configuration File**

- A configuration file specifying the tables to process, their primary keys, incremental columns, and selected columns must be prepared.
  
Once the metadata configuration is provided, the framework automatically handles:

  - Full initial loads for new tables or new columns

  - Incremental change detection

  - Type 2 updates for historical data

  - Logging of each operation with row counts and status

    **2.1 MetadataFile Location and Updates**
        - The metadata configuration file should be maintained in the Fabric Lakehouse under the following path:

          Lakehouse_Name/Tables/config/metadata_staging.csv
      
     **2.2 How to update:**
    
        1. Download the existing metadata_staging.csv file locally.
    
        2. Add or modify entries (tables, primary keys, incremental columns, tracked columns).
    
        3. Upload the updated file back to the config folder in the Lakehouse.
     
    **Handling Multiple Primary Keys**
    - In the configuration file, multiple primary keys should be defined using the **| delimiter**.
  
      
       Example: customer_id | region_id

**Note:**  
- The Fabric Starter Kit provisions a Lakehouse and creates the required notebooks, but it does **not** generate the metadata configuration file needed for SCD Type 2 processing.  
- After pushing the Fabric Starter Kit code to your local environment, you must: 
  1. Prepare your metadata file manually.  
  2. Upload it under **workspace_data.csv** locally as metadata_staging.csv  
  3. Upload it again to the Fabric Lakehouse under:  
     `Lakehouse_Name/Tables/config/metadata_staging.csv` 


**3.Primary Key and Incremental Column Defined**

- Each table must have a unique primary key for deduplication.

- Incremental column (e.g., timestamp) is required for detecting changes for incremental loads.

**4.Dependencies**

- Ensure the environment has all required Python packages installed for Spark, Delta Lake, and other data processing utilities.

- Optional: Logging framework for writing processing logs to monitor.staging_log.
  
**5.Data Quality**

Before processing, the source/staging data must meet the following requirements:

| Field                              | Format                                                               |
|--------------------------------    |-----------------------------------------------------------------------------|
| `Incremental Column`               | YYYY-MM-DD HH:MM:SS                                                  |
| `Date / Timestamp Columns`         | YYYY-MM-DD HH:MM:SS                                                  |
| `Tracked Columns`                  | Consistent Data Type                                                 | 

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

**Log File Location**

  - All logs are written to the Fabric Lakehouse table:

    Lakehouse_Name/Tables/monitoring/staging_log


## Contributing

Found a table, column, or feature that’s missing? Want to improve the SCD-2 configuration or logging process? Feel free to open an issue or submit a pull request!



