# Flight Booking Data Engineering Project

## Overview

This project implements a data engineering pipeline for flight booking data using Databricks Community Edition. The solution focuses on building a robust and scalable medallion architecture (raw, bronze, silver layers) to prepare data for analysis, leveraging the features available in Databricks Community Edition and Unity Catalog. It includes the dynamic handling of Slowly Changing Dimensions (SCD Type 2) in the silver layer using Delta Live Tables (DLT) and dbt snapshots.

## Architecture

The project utilizes the following technologies and concepts:

*   **Databricks Community Edition:** Free, web-based platform for Spark development and data engineering tasks.
*   **Unity Catalog:** Unified governance solution for data and AI on Databricks, providing centralized access control, auditing, and data discovery.
*   **Delta Lake:** Open-source storage layer that brings reliability to data lakes, ensuring data quality and supporting ACID transactions.
*   **Auto Loader:** Databricks feature for incrementally and efficiently processing new data files as they arrive.
*   **Databricks Jobs:** For orchestrating and scheduling the data pipeline tasks.
*   **Delta Live Tables (DLT):** A declarative framework for building reliable, maintainable, and testable data pipelines on Databricks. It simplifies ETL development and automates execution.
*   **dbt (data build tool):** A transformation workflow that enables data teams to build and deploy data models, facilitating version control, testing, and documentation. It is used for implementing {Link: SCD Type 2 https://sarathd.medium.com/slowly-changing-dimensions-using-dbt-snapshots-2a46727f0d39} using snapshots.
*   **Medallion Architecture:** Structuring data into layers for improved data quality and maintainability: raw (bronze), cleaned and conformed (silver), and aggregated/curated (gold).

## Data Flow

1.  **Data Ingestion (Raw to Bronze):**
    *   Source data (Flight bookings fact, Passenger dim, Flights dim, Airports Dim) is ingested into the raw layer of Unity Catalog.
    *   Databricks Auto Loader is used for dynamic and incremental ingestion of new data files into the bronze layer Delta tables.
    *   Databricks Jobs are configured to automate the ingestion process using a loop and task values to handle multiple data sources and ensure efficient processing.

2.  **Data Transformation (Bronze to Silver) - Including Slowly Changing Dimensions (SCDs):**
    *   Delta Live Tables (DLT) pipelines are used to process data from the bronze layer, performing cleaning and transformations.
    *   **Slowly Changing Dimensions (SCDs):** Specifically, DLT is used in conjunction with dbt snapshots to handle Slowly Changing Dimensions (SCD Type 2) for relevant dimension tables like Passenger and Airports. This involves capturing and tracking changes to these dimensions over time, ensuring historical data is preserved for accurate analysis.
    *   The transformed and conformed data, including the SCD Type 2 dimensions, are stored as Delta tables in the silver layer of Unity Catalog.

3.  **Data Curating (Silver to Gold):**
    *   (Details to be added later based on the user's progress)

## Project Setup

### Prerequisites

*   Databricks Community Edition account ([Link: Sign up here https://community.cloud.databricks.com/])
*   Basic understanding of Databricks notebooks and Spark.
*   Familiarity with dbt concepts, particularly snapshots.

### Databricks Configuration

1.  **Unity Catalog Setup:**
    *   Ensure Unity Catalog is enabled for your Databricks Community Edition workspace.
    *   Create catalogs and schemas within Unity Catalog to organize your project's tables (raw, bronze, silver, gold).

2.  **Data Ingestion (Raw to Bronze):**
    *   **Data Sources:** Place your raw data files for Flight bookings, Passenger, Flights, and Airports in designated input locations accessible by Databricks Community Edition. These locations can be internal to your Databricks workspace as you are not using external cloud storage in this edition.
    *   **Auto Loader Configuration:** Create Databricks notebooks utilizing Auto Loader with the `cloudFiles` source to ingest the data into separate bronze layer Delta tables within Unity Catalog. Configure schema inference and evolution with `cloudFiles.schemaLocation` and `cloudFiles.schemaEvolutionMode` options to handle schema changes over time.
    *   **Databricks Job with Loop and Task Values:** Create a Databricks Job to orchestrate the ingestion of all raw data into the bronze layer.
        *   Implement a loop within the job to iterate over the various data sources (fact and dimension tables).
        *   Utilize task values to dynamically pass source names, target table names, and other relevant parameters to the Auto Loader notebooks for each iteration.

3.  **Data Transformation (Bronze to Silver) - With SCDs using DLT and dbt:**
    *   **Delta Live Tables Pipelines:** Develop DLT pipelines to consume data from the bronze layer Delta tables.
        *   Within these pipelines, implement data cleaning, standardization, and enrichment logic.
        *   For dimension tables that require historical tracking (e.g., Passenger, Airports), leverage DLT's capabilities to integrate with dbt snapshots. You can use the `APPLY CHANGES INTO` syntax within DLT, which streamlines the process of synchronizing data and maintaining the integrity of the SCD tables.
    *   **dbt Snapshots:** Configure dbt snapshots for the relevant dimension tables to implement the SCD Type 2 logic. This will automatically track changes to the dimension attributes, creating new records with effective start and end dates to maintain historical versions.
    *   The resulting silver layer tables will be Delta tables within Unity Catalog, containing cleaned, conformed, and historically tracked dimension data.

## Usage

1.  **Run the Databricks Job:** Execute the configured Databricks Job to initiate the data ingestion process. The Auto Loader will incrementally process new data and update the bronze layer tables.
2.  **Execute DLT Pipelines:** Run the DLT pipelines to process data from the bronze layer, perform transformations, and handle SCDs, populating the silver layer tables.
3.  **Verify Data in Silver Layer:** Query the silver layer tables in Unity Catalog to confirm that the data has been successfully transformed and that historical changes are being tracked correctly in the SCD Type 2 dimension tables.
4.  **Monitor Job and Pipeline Execution:** Monitor the Databricks Job and DLT pipeline runs for status and any potential issues encountered during the ingestion and transformation processes.

## Contributing

Contributions to this project are welcome. Follow these guidelines:

1.  Fork the repository.
2.  Create a new branch for your feature or bug fix.
3.  Make your changes and ensure they adhere to the project's coding standards.
4.  Submit a pull request with a detailed description of your changes.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
