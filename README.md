<img src="https://github.com/user-attachments/assets/93640f49-d8fb-45cf-925e-6b7075f83927#gh-light-mode-only" alt="Light Mode" />
<img src="https://github.com/user-attachments/assets/71380a65-3419-41f4-ba29-2b74c7e6a66b#gh-dark-mode-only" alt="Dark Mode" />

---

# Reporting tables
The Nameless Analytics reporting tables is a set of tables in BigQuery where users, sessions and events data are stored.

For an overview of how Nameless Analytics works [start from here](https://github.com/tommasomoretti/nameless-analytics/).

Table of contents:
- [Create tables and table functions](#create-tables-and-table-functions)
- Tables
  - [Events raw table](#events-raw-table)
  - [Dates table](#dates-table)
- Table functions
  - [Events](#events)
  - [Users](#users)
  - [Sessions](#sessions)
  - [Pages](#pages)
  - [Transactions](#transactions)
  - [Products](#products)
  - [Shopping stages open funnel](#shopping-stages-open-funnel)
  - [Shopping stages closed funnel](#shopping-stages-closed-funnel)
  - [GTM performances](#gtm-performances)
  - [Consents](#consents)


## Tables
### Events raw table
This is the schema of the raw data main table. It's a partitioned table by event_date, clustered by client_id, session_id and event_name.

| Nome campo                 | Tipo     | Modalità | Descrizione                                                                                                                                                                                                                   |
|----------------------------|----------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| event_date                 | DATE     | REQUIRED | Date of the request                                                                                                                                                                                                           |
| event_datetime             | DATETIME | NULLABLE | Datetime of the request                                                                                                                                                                                                       |
| event_timestamp            | INTEGER  | REQUIRED | Insert timestamp of the event                                                                                                                                                                                                 |
| processing_event_timestamp | INTEGER  | NULLABLE | Nameless Analytics Server-side Client Tag received event timestamp when hits are sent from a website or a Streaming Protocol request                                                                                          |
| event_origin               | STRING   | REQUIRED | "Streaming Protocol" if the hit comes from streaming protocol, "Website" if the hit comes from browser                                                                                                                        |
| job_id                     | STRING   | NULLABLE | Job id for Streaming Protocol hits                                                                                                                                                                                            |
| content_length             | INTEGER  | NULLABLE | Size of the message body, in bytes                                                                                                                                                                                            |
| client_id                  | STRING   | REQUIRED | Client ID                                                                                                                                                                                                                     |
| user_data                  | RECORD   | REPEATED | User data                                                                                                                                                                                                                     |
| session_id                 | STRING   | REQUIRED | Session ID                                                                                                                                                                                                                    |
| session_data               | RECORD   | REPEATED | Session data                                                                                                                                                                                                                  |
| event_id                   | STRING   | REQUIRED | Event ID                                                                                                                                                                                                                      |
| event_name                 | STRING   | REQUIRED | Event name                                                                                                                                                                                                                    |
| event_data                 | RECORD   | REPEATED | Event data                                                                                                                                                                                                                    |
| ecommerce                  | JSON     | NULLABLE | Ecommerce object                                                                                                                                                                                                              |
| datalayer                  | JSON     | NULLABLE | Current dataLayer value                                                                                                                                                                                                       |
| consent_data               | RECORD   | REPEATED | Consent data                                                                                                   

 
### Dates table
This is the schema of the Dates table. It's a partitioned table by date, clustered by month_name and day_name.

| Nome campo         | Tipo    | Modalità | Descrizione                                                    |
|--------------------|---------|----------|----------------------------------------------------------------|
| date               | DATE    | REQUIRED | The date value                                                 |
| year               | INTEGER | NULLABLE | Year extracted from the date                                   |
| quarter            | INTEGER | NULLABLE | Quarter of the year (1-4) extracted from the date              |
| month_number       | INTEGER | NULLABLE | Month number of the year (1-12) extracted from the date        |
| month_name         | STRING  | NULLABLE | Full name of the month (e.g., January) extracted from the date |
| week_number_sunday | INTEGER | NULLABLE | Week number of the year, starting on Sunday                    |
| week_number_monday | INTEGER | NULLABLE | Week number of the year, starting on Monday                    |
| day_number         | INTEGER | NULLABLE | Day number of the month (1-31)                                 |
| day_name           | STRING  | NULLABLE | Full name of the day of the week (e.g., Monday)                |
| day_of_week_number | INTEGER | NULLABLE | Day of the week number (1 for Monday, 7 for Sunday)            |
| is_weekend         | BOOLEAN | NULLABLE | True if the day is Saturday or Sunday                          |



## Table functions
### Users raw latest
This is the schema of the Users raw latest table function.


### Users
This is the schema of the Users table function.


### Sessions
This is the schema of the Sessions table function.


### Pages
This is the schema of the Pages table function.


### Transactions
This is the schema of the Transaction table function.


### Products
This is the schema of the Products table function.


### Shopping stages open funnel
This is the schema of the Shopping stages open funnel table function.


### Shopping stages closed funnel
This is the schema of the Shopping stages closed funnel table function.


### GTM performances
This is the schema of the GTM performances table function.


### Consents
This is the schema of the Consents table function.



## Create tables and table functions

<details><summary>To create the tables use this DML statement.</summary>
  
```sql
# NAMELESS ANALYTICS

# Project settings
declare project_name string default 'tom-moretti';  -- Change this
declare dataset_name string default 'nameless_analytics'; -- Change this
declare dataset_location string default 'eu'; -- Change this

# Tables
declare main_table_name string default 'events_raw';
declare dates_table_name string default 'calendar_dates';

# Paths
declare main_dataset_path string default CONCAT('`', project_name, '.', dataset_name, '`');
declare main_table_path string default CONCAT('`', project_name, '.', dataset_name, '.', main_table_name,'`');
declare dates_table_path string default CONCAT('`', project_name, '.', dataset_name, '.', dates_table_name,'`');


# -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Enable BigQuery advanced runtime (for more info https://cloud.google.com/bigquery/docs/advanced-runtime)
declare enable_bigquery_advanced_runtime string default format(
  """
    ALTER PROJECT `%s`
    SET OPTIONS (
      `region-%s.query_runtime` = 'advanced' # default null
    );
  """
, project_name, dataset_location);


# Main dataset (for more info https://cloud.google.com/bigquery/docs/datasets#sql)
declare main_dataset_sql string default format(
  """
    create schema if not exists %s
    options (
      # default_kms_key_name = 'KMS_KEY_NAME',
      # default_partition_expiration_days = PARTITION_EXPIRATION,
      # default_table_expiration_days = TABLE_EXPIRATION,
      # max_time_travel_hours = HOURS, # default 168 hours => 7 days 
      # storage_billing_model = BILLING_MODEL # Phytical or logical (default)  
      description = 'Nameless Analytics',
      location = '%s'
    );
  """
, main_dataset_path, dataset_location);


# Main table
declare main_table_sql string default format(
  """
    create table if not exists %s (
      client_id STRING NOT NULL OPTIONS (description = 'Client ID'),
      user_id STRING OPTIONS (description = 'User ID'),
      user_date DATE NOT NULL OPTIONS (description = 'User date'),
      user_data ARRAY<
        STRUCT<
          name STRING OPTIONS (description = 'User data parameter name'),
          value STRUCT<
            string STRING OPTIONS (description = 'User data parameter string value'),
            int INT64 OPTIONS (description = 'User data parameter int number value'),
            float FLOAT64 OPTIONS (description = 'User data parameter float number value'),
            json JSON OPTIONS (description = 'User data parameter JSON value')
          > OPTIONS (description = 'User data parameter value name')
        >
      > OPTIONS (description = 'User data'),

      session_id STRING NOT NULL OPTIONS (description = 'Session ID'),
      session_date DATE NOT NULL OPTIONS (description = 'Session date'),
      session_data ARRAY<
        STRUCT<
          name STRING OPTIONS (description = 'Session data parameter name'),
          value STRUCT<
            string STRING OPTIONS (description = 'Session data parameter string value'),
            int INT64 OPTIONS (description = 'Session data parameter int number value'),
            float FLOAT64 OPTIONS (description = 'Session data parameter float number value'),
            json JSON OPTIONS (description = 'Session data parameter JSON value')
          > OPTIONS (description = 'Session data parameter value name')
        >
      > OPTIONS (description = 'Session data'),  

      page_id STRING NOT NULL OPTIONS (description = 'Page ID'),
      page_date DATE NOT NULL OPTIONS (description = 'Page date'),
      page_data ARRAY<
        STRUCT<
          name STRING OPTIONS (description = 'Page data parameter name'),
          value STRUCT<
            string STRING OPTIONS (description = 'Page data parameter string value'),
            int INT64 OPTIONS (description = 'Page data parameter int number value'),
            float FLOAT64 OPTIONS (description = 'Page data parameter float number value'),
            json JSON OPTIONS (description = 'Page data parameter JSON value')
          > OPTIONS (description = 'Page data parameter value name')
        >
      > OPTIONS (description = 'Page data'),

      event_name STRING NOT NULL OPTIONS (description = 'Event name'),
      event_id STRING NOT NULL OPTIONS (description = 'Event ID'),
      event_date DATE NOT NULL OPTIONS (description = 'Event date'),
      -- event_datetime DATETIME OPTIONS (description = 'Event datetime'),
      event_timestamp int NOT NULL OPTIONS (description = 'Insert event timestamp'),
      event_origin STRING NOT NULL OPTIONS (description = 'Event origin: Website or Streaming protocol'),
      event_data ARRAY<
        STRUCT<
          name STRING OPTIONS (description = 'Event data parameter name'),
          value STRUCT<
            string STRING OPTIONS (description = 'Event data parameter string value'),
            int INT64 OPTIONS (description = 'Event data parameter int number value'),
            float FLOAT64 OPTIONS (description = 'Event data parameter float number value'),
            json JSON OPTIONS (description = 'Event data parameter JSON value')
          > OPTIONS (description = 'Event data parameter value name')
        >
      > OPTIONS (description = 'Event data'),

      ecommerce JSON OPTIONS (description = 'Ecommerce object'),

      datalayer JSON OPTIONS (description = 'Current dataLayer value'),

      consent_data ARRAY<
        STRUCT<
          name STRING OPTIONS (description = 'Consent data parameter name'),
          value STRUCT<
            string STRING OPTIONS (description = 'Consent data parameter string value')
          > OPTIONS (description = 'Consent data parameter value name')
        >
      > OPTIONS (description = 'Consent data'),

      gtm_data ARRAY<
        STRUCT<
          name STRING OPTIONS (description = 'GTM execution parameter name'),
          value STRUCT<
            string STRING OPTIONS (description = 'GTM execution parameter string value'),
            int INT64 OPTIONS (description = 'Event data parameter int number value')
          > OPTIONS (description = 'GTM execution parameter value name')
        >
      > OPTIONS (description = 'GTM execution data')

    )

    PARTITION BY event_date
    CLUSTER BY user_date, session_date, page_date, event_name
    OPTIONS(
      description = 'Nameless Analytics | Main table',
      require_partition_filter = FALSE
    );
  """
, main_table_path);


# Dates table
declare dates_table_sql string default FORMAT(
  """
    create table if not exists %s (
      date DATE NOT NULL OPTIONS(description = "The date value"),
      year INT64 OPTIONS(description = "Year extracted from the date"),
      quarter INT64 OPTIONS(description = "Quarter of the year (1-4) extracted from the date"),
      month_number INT64 OPTIONS(description = "Month number of the year (1-12) extracted from the date"),
      month_name STRING OPTIONS(description = "Full name of the month (e.g., January) extracted from the date"),
      week_number_sunday INT64 OPTIONS(description = "Week number of the year, starting on Sunday"),
      week_number_monday INT64 OPTIONS(description = "Week number of the year, starting on Monday"),  
      day_number INT64 OPTIONS(description = "Day number of the month (1-31)"),
      day_name STRING OPTIONS(description = "Full name of the day of the week (e.g., Monday)"),
      day_of_week_number INT64 OPTIONS(description = "Day of the week number (1 for Monday, 7 for Sunday)"),
      is_weekend BOOL OPTIONS(description = "True if the day is Saturday or Sunday")
    ) 
    
    PARTITION BY DATE_TRUNC(date, year)
    CLUSTER BY month_name, day_name
    OPTIONS (description = 'Nameless Analytics | Dates utility table')
    
    AS (
      SELECT 
        date,
        EXTRACT(YEAR FROM date) AS year,
        EXTRACT(QUARTER FROM date) AS quarter,
        EXTRACT(MONTH FROM date) AS month_number,
        FORMAT_DATE('%%B', date) AS month_name,
        EXTRACT(WEEK(SUNDAY) FROM date) AS week_number_sunday,
        EXTRACT(WEEK(MONDAY) FROM date) AS week_number_monday,
        EXTRACT(DAY FROM date) AS day_number,
        FORMAT_DATE('%%A', date) AS day_name,
        EXTRACT(DAYOFWEEK FROM date) AS day_of_week_number, 
        IF(EXTRACT(DAYOFWEEK FROM date) IN (1, 7), TRUE, FALSE) AS is_weekend
      FROM UNNEST(GENERATE_DATE_ARRAY('1970-01-01', '2050-12-31', INTERVAL 1 DAY)) AS date
    );
  """
, dates_table_path);


# -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Create tables 
-- execute immediate enable_bigquery_advanced_runtime;
-- execute immediate main_dataset_sql;
execute immediate main_table_sql;
-- execute immediate dates_table_sql;
```
</details>

---

Reach me at: [Email](mailto:hello@tommasomoretti.com) | [Website](https://tommasomoretti.com/?utm_source=github.com&utm_medium=referral&utm_campaign=nameless_analytics) | [Twitter](https://twitter.com/tommoretti88) | [Linkedin](https://www.linkedin.com/in/tommasomoretti/)
