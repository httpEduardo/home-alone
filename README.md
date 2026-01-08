# Stripe dbt Package

[![dbt](https://img.shields.io/badge/dbt-%3E%3D0.13.0-orange.svg)](https://www.getdbt.com/)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

A collection of SQL-based analytics models for Stripe data, built with [dbt (data build tool)](https://www.getdbt.com/).

---

## Table of Contents

- [Overview](#overview)
- [Models Included](#models-included)
- [Compatibility](#compatibility)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Data Sources](#data-sources)
- [Customization](#customization)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

This dbt package provides a comprehensive set of analytics models for Stripe data. It transforms raw Stripe event data into analytics-ready tables, enabling you to analyze customer behavior, subscriptions, revenue, and more.

The package is designed to be modular and customizable, allowing you to use only the models relevant to your business needs.

---

## Models Included

### Core Entities

Generic models that extract and transform core Stripe entities. These models are relevant for most businesses using Stripe:

- **`stripe_customers`** - Customer dimensions and attributes
- **`stripe_invoices`** - Invoice details and statuses
- **`stripe_invoice_items`** - Line items within invoices
- **`stripe_plans`** - Subscription plan information
- **`stripe_coupons`** - Coupon and discount details
- **`stripe_subscriptions`** - Subscription lifecycle data

### Event Filters

Models that filter and organize Stripe events by entity type:

- **`stripe_customer_events`** - Customer-related events
- **`stripe_invoice_events`** - Invoice-related events
- **`stripe_invoice_item_events`** - Invoice item events
- **`stripe_subscription_events`** - Subscription lifecycle events
- **`stripe_plan_events`** - Plan-related events
- **`stripe_discount_events`** - Discount application events
- **`stripe_coupon_events`** - Coupon-related events
- **`stripe_charge_events`** - Charge and payment events

### Transactions Prep

Models that prepare transaction-level data for analysis:

- **`stripe_transactions`** - Consolidated transaction data
- **`stripe_subscription_transactions`** - Subscription-based transactions
- **`stripe_addon_transactions`** - Add-on and one-time charges
- **`stripe_proration_transactions`** - Proration adjustments
- **`stripe_subscription_transactions_amortized`** - Amortized subscription revenue
- **`stripe_subscription_transactions_amortized_daily`** - Daily revenue amortization

### MRR (Monthly Recurring Revenue)

Models for calculating and analyzing monthly recurring revenue:

- **`stripe_mrr`** - Monthly recurring revenue calculations
- **`stripe_mrr_base`** - Base MRR calculations
- **`stripe_mrr_filled`** - Gap-filled MRR data
- **`stripe_mrr_unioned`** - Combined MRR views
- **`stripe_mrr_xf`** - Transformed MRR data
- **`stripe_mrr_final_churn`** - Churn analysis for MRR

---

## Compatibility

> **Note:** While the `core_entities` and `event_filters` models are generic and likely relevant for all businesses using Stripe, the `transactions_prep` and `mrr` models may or may not be applicable to your specific business case.
>
> If you find these models useful in your analytics, great! Otherwise, you can disable them in your project by setting `enabled: false` for the relevant folders in your `dbt_project.yml`.

---

## Prerequisites

- **dbt version:** `>=0.13.0`
- **Data warehouse:** Compatible with dbt-supported warehouses (BigQuery, Snowflake, Redshift, etc.)
- **Stripe events table:** A table containing Stripe webhook events

---

## Installation

### 1. Add Package Dependency

Add this package as a dependency to your dbt project. In your `packages.yml` file:

```yaml
packages:
  - git: "https://github.com/httpEduardo/home-alone.git"
    revision: "v1.0"  # Pin to a specific version or tag
```

> **Recommendation:** Reference a specific tag or version so you can control the upgrade process when new versions are released.

### 2. Install Dependencies

Run the following command to download the package:

```bash
dbt deps
```

---

## Configuration

Add the following configuration to your `dbt_project.yml`:

```yaml
models:
  stripe:
    enabled: true
    materialized: view
    vars:
      # Insert the location of your Stripe events table here as 'schema.table'
      events_table: 'stripe.stripe_events'
      # Optional: Set your timezone (defaults to America/New_York)
      timezone: 'America/New_York'
```

### Optional: Disable Specific Model Groups

If you want to disable certain model groups (e.g., `mrr` or `transactions_prep`), add this to your `dbt_project.yml`:

```yaml
models:
  stripe:
    enabled: true
    materialized: view
    # Disable MRR models if not needed
    mrr:
      enabled: false
    # Disable transaction prep models if not needed
    transactions_prep:
      enabled: false
    vars:
      events_table: 'stripe.stripe_events'
```

---

## Usage

### Run All Models

To build all Stripe models, run:

```bash
dbt run --models stripe
```

### Run Specific Model Groups

To run only specific model groups:

```bash
# Run only core entities
dbt run --models stripe.core_entities

# Run only event filters
dbt run --models stripe.event_filters

# Run only MRR models
dbt run --models stripe.mrr

# Run only transaction prep models
dbt run --models stripe.transactions_prep
```

### Test Models

To run tests on the Stripe models:

```bash
dbt test --models stripe
```

### Generate Documentation

To generate and serve documentation:

```bash
dbt docs generate
dbt docs serve
```

---

## Data Sources

This package expects a Stripe events table containing webhook event data from Stripe. The table should include:

- Event ID
- Event type
- Event data (JSON or structured columns)
- Created timestamp
- Additional metadata fields

You can configure the location of this table in your `dbt_project.yml` using the `events_table` variable.

---

## Customization

### Adjusting Timezone

The package uses a configurable timezone for date calculations. You can adjust this in your `dbt_project.yml`:

```yaml
models:
  stripe:
    vars:
      timezone: 'America/Los_Angeles'  # Or any valid timezone
```

### Extending Models

You can extend or customize any model by creating a new model in your project that references the package models:

```sql
-- models/my_custom_stripe_customers.sql
select
  *,
  custom_field_1,
  custom_field_2
from {{ ref('stripe_customers') }}
```

---

## Contributing

Contributions are welcome! Please feel free to submit issues, feature requests, or pull requests to improve this package.

---

## License

This project is licensed under the Apache License 2.0. See the [LICENSE](LICENSE) file for details.
