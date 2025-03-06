# AWS Cost and Usage Report (CUR) Data Dictionary

This document provides a comprehensive description of the columns in the AWS Cost and Usage Report (CUR) simulation.  
The column names and descriptions align with the official AWS CUR Data Dictionary.

## Identity Columns

| Column Name | Description | Data Type | Example |
|-------------|-------------|-----------|---------|
| **identity/LineItemId** | A unique identifier for each line item in the report | String | `9cac56d2-8481-43d1-bbbb-eeeeeeeeee` |
| **identity/TimeInterval** | The time period covered by this line item in ISO 8601 format | String | `2023-01-01T00:00:00Z/2023-01-01T01:00:00Z` |

## Bill Columns

| Column Name | Description | Data Type | Example |
|-------------|-------------|-----------|---------|
| **bill/InvoiceId** | The ID of the invoice that covers this line item | String | `I-20230101` |
| **bill/BillingEntity** | The entity that bills you for AWS usage | String | `AWS` |
| **bill/BillType** | The type of bill | String | `Anniversary` |
| **bill/PayerAccountId** | The account ID of the paying account | String | `111111111111` |
| **bill/BillingPeriodStartDate** | The start date of the billing period in ISO 8601 format | String | `2023-01-01T00:00:00Z` |
| **bill/BillingPeriodEndDate** | The end date of the billing period in ISO 8601 format | String | `2023-01-31T23:59:59Z` |

## Line Item Columns

| Column Name | Description | Data Type | Example |
|-------------|-------------|-----------|---------|
| **lineItem/UsageAccountId** | The account ID that used this line item | String | `222200000001` |
| **lineItem/LineItemType** | The type of charge covered by this line item | String | `Usage`, `Tax`, `DiscountedUsage`, `SavingsPlanCoveredUsage`, `Credit` |
| **lineItem/UsageStartDate** | The start date and time for this line item in ISO 8601 format | String | `2023-01-01T00:00:00Z` |
| **lineItem/UsageEndDate** | The end date and time for this line item in ISO 8601 format | String | `2023-01-01T01:00:00Z` |
| **lineItem/ProductCode** | The code of the AWS product | String | `EC2`, `S3`, `Lambda` |
| **lineItem/UsageType** | The usage type for this line item | String | `APN1-DataTransfer-Out-Bytes`, `USW2-BoxUsage:t2.micro` |
| **lineItem/Operation** | The specific AWS operation covered by this line item | String | `RunInstances`, `GetObject` |
| **lineItem/AvailabilityZone** | The Availability Zone where the usage occurred | String | `us-east-1a`, `ap-southeast-2b` |
| **lineItem/ResourceId** | The ID of the resource used | String | `i-1234567890abcdef0`, `vol-1234567890abcdef0` |
| **lineItem/UsageAmount** | The amount of usage for this line item | Double | `1.0`, `0.5`, `744.0` |
| **lineItem/NormalizationFactor** | The normalization factor for the instance type | String | `1`, `4`, `8` |
| **lineItem/NormalizedUsageAmount** | The normalized amount of usage for this line item | Double | `1.0`, `2.0`, `8.0` |
| **lineItem/CurrencyCode** | The currency that the charges for this line item were billed in | String | `USD` |
| **lineItem/UnblendedRate** | The unblended rate for specific usage | Double | `0.0116`, `0.023`, `0.10` |
| **lineItem/UnblendedCost** | The unblended cost = UnblendedRate × UsageAmount | Double | `8.64`, `0.23`, `24.00` |
| **lineItem/BlendedRate** | The blended rate for specific usage | Double | `0.0116`, `0.023`, `0.10` |
| **lineItem/BlendedCost** | The blended cost = BlendedRate × UsageAmount | Double | `8.64`, `0.23`, `24.00` |
| **lineItem/LineItemDescription** | Description of the line item | String | `$0.023 per GB - first 10 TB / month data transfer out` |
| **lineItem/TaxType** | The type of tax if the line item is for a tax | String | `VAT`, `Sales Tax` |

## Product Columns

| Column Name | Description | Data Type | Example |
|-------------|-------------|-----------|---------|
| **product/ProductName** | The full name of the AWS product | String | `Amazon Elastic Compute Cloud`, `Amazon Simple Storage Service` |
| **product/servicecode** | The code for the AWS service | String | `ec2`, `s3`, `lambda` |
| **product/region** | The AWS region where the usage occurred | String | `us-east-1`, `eu-west-1` |

## Pricing Columns

| Column Name | Description | Data Type | Example |
|-------------|-------------|-----------|---------|
| **pricing/unit** | The pricing unit for the line item | String | `GB`, `Hrs`, `Requests` |
| **pricing/publicOnDemandCost** | The cost based on public on-demand rates | Double | `8.64`, `0.23`, `24.00` |
| **pricing/publicOnDemandRate** | The public on-demand rate | Double | `0.0116`, `0.023`, `0.10` |
| **pricing/term** | The pricing terms for this line item | String | `OnDemand`, `Reserved` |
| **pricing/offeringClass** | The offering class for this line item if applicable | String | `Standard`, `Convertible` |

## Resource Tags

The resource tags are stored in a separate CSV file with the following columns:

| Column Name | Description | Data Type | Example |
|-------------|-------------|-----------|---------|
| **resourceId** | The ID of the resource that this tag is associated with | String | `i-1234567890abcdef0` |
| **key** | The tag key | String | `Project`, `BusinessUnit`, `Environment` |
| **value** | The tag value | String | `MoleculeSynthesizerAI`, `Pharma`, `Production` |

## Tags

The following types of tags are available for each resource:

1. **Name** - The name of the resource (combination of project and service)
2. **Project** - The project name
3. **BusinessUnit** - The business unit this resource belongs to
4. **Stage** - The deployment stage (prod, dev, staging, etc.)
5. **UseCase** - The use case scenario
6. **Environment** - Whether Production or Non-Production
7. **ManagedBy** - Team managing the resource
8. **CostCenter** - Cost center code
9. Service-specific tags (such as InstanceType for EC2 or StorageClass for S3)

## Cost Calculation 

Calculate costs as follows:

1. The annual budget is distributed across projects (roughly equal parts)
2. Each project's budget is distributed across services based on their relative pricing
3. Service budgets are distributed across stages (Production gets ~60%, Staging ~30%, Dev ~10%)
4. Stage budgets are distributed across regions, applying regional cost factors
5. Regional budgets are distributed across resources
6. Resource budgets determine the usage amount based on service rates

Each of these distributions incorporates the project lifecycle pattern (growing, declining, etc.) which adjusts the daily budget allocation.
