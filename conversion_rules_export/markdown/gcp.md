| FOCUS Dimension    |   Transform Step | Source Column                   | Source Column Type   | Transform Type      | Filters/Process/Etc.                                                                  |
|:-------------------|-----------------:|:--------------------------------|:---------------------|:--------------------|:--------------------------------------------------------------------------------------|
| BillingAccountId   |                1 | billing_account_id              | Not Defined          | RENAME_COLUMN       |                                                                                       |
| BillingPeriodEnd   |                1 | invoice.month                   | Not Defined          | UNNEST_COLUMN       |                                                                                       |
| BillingPeriodEnd   |                2 | BillingPeriodEnd                | Not Defined          | PARSE_DATETIME      | %Y%m                                                                                  |
| BillingPeriodEnd   |                3 | BillingPeriodEnd                | Not Defined          | ASSIGN_TIMEZONE     | America/Los_Angeles                                                                   |
| BillingPeriodEnd   |                4 | BillingPeriodEnd                | Not Defined          | CONVERT_TIMEZONE    | UTC                                                                                   |
| BillingPeriodStart |                1 | invoice.month                   | Not Defined          | UNNEST_COLUMN       |                                                                                       |
| BillingPeriodStart |                2 | BillingPeriodStart              | Not Defined          | PARSE_DATETIME      | %Y%m                                                                                  |
| BillingPeriodStart |                3 | BillingPeriodStart              | Not Defined          | ASSIGN_TIMEZONE     | America/Los_Angeles                                                                   |
| BillingPeriodStart |                4 | BillingPeriodStart              | Not Defined          | CONVERT_TIMEZONE    | UTC                                                                                   |
| ChargePeriodStart  |                1 | usage_start_time                | Not Defined          | RENAME_COLUMN       |                                                                                       |
| ChargePeriodStart  |                2 | ChargePeriodStart               | Not Defined          | ASSIGN_UTC_TIMEZONE |                                                                                       |
| ChargePeriodEnd    |                1 | usage_end_time                  | Not Defined          | RENAME_COLUMN       |                                                                                       |
| ChargePeriodEnd    |                2 | ChargePeriodEnd                 | Not Defined          | ASSIGN_UTC_TIMEZONE |                                                                                       |
| Region             |                1 | location.region                 | Not Defined          | UNNEST_COLUMN       |                                                                                       |
| Region             |                2 | location.location               | Not Defined          | UNNEST_COLUMN       |                                                                                       |
| Region             |                3 | tmp_region_Region               | Not Defined          | SQL_CONDITION       | conditions:                                                                           |
|                    |                  |                                 |                      |                     | - WHEN tmp_region_Region is not null THEN tmp_region_Region                           |
|                    |                  |                                 |                      |                     | default_value: tmp_location_Region                                                    |
| Publisher          |                1 | seller_name                     | Not Defined          | SQL_CONDITION       | conditions:                                                                           |
|                    |                  |                                 |                      |                     | - WHEN seller_name is not NULL THEN seller_name                                       |
|                    |                  |                                 |                      |                     | default_value: '''Google Cloud'''                                                     |
| BilledCost         |                1 | credits.amount                  | Not Defined          | UNNEST_COLUMN       | {'children_type': 'list', 'aggregation_operation': 'sum'}                             |
| BilledCost         |                2 | tmp_credits_amount_BilledCost   | Not Defined          | SQL_QUERY           | SELECT *, (cost + tmp_credits_amount_BilledCost) AS BilledCost from {{ TABLE_NAME }}  |
| ChargeType         |                1 | cost_type                       | Not Defined          | SQL_CONDITION       | conditions:                                                                           |
|                    |                  |                                 |                      |                     | - WHEN cost_type = 'tax' THEN 'Tax'                                                   |
|                    |                  |                                 |                      |                     | - WHEN cost_type = 'regular' THEN 'Purchase'                                          |
|                    |                  |                                 |                      |                     | - WHEN cost_type = 'adjustment' THEN 'Adjustment'                                     |
|                    |                  |                                 |                      |                     | default_value: '''Usage'''                                                            |
| Provider           |                1 | NA                              | Not Defined          | ASSIGN_STATIC_VALUE | static_value: Google Cloud                                                            |
| ServiceCategory    |                1 | service.description             | Not Defined          | UNNEST_COLUMN       |                                                                                       |
| ServiceCategory    |                2 | tmp_description_ServiceCategory | Not Defined          | LOOKUP              | destination_value: ServiceCategory                                                    |
|                    |                  |                                 |                      |                     | reference_dataset_path: conversion_configs/gcp/mapping_files/gcp_category_mapping.csv |
|                    |                  |                                 |                      |                     | source_value: ConsumedService                                                         |
| AvailabilityZone   |                1 | location.zone                   | Not Defined          | UNNEST_COLUMN       |                                                                                       |
| BillingCurrency    |                1 | currency                        | Not Defined          | RENAME_COLUMN       |                                                                                       |
| ResourceId         |                1 | resource.global_name            | Not Defined          | UNNEST_COLUMN       |                                                                                       |