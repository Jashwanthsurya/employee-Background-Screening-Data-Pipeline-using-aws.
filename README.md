# Employee Background Screening Data Pipeline (AWS)

## Project Overview

This project simulates a real-world employee background verification pipeline using AWS services.

Architecture:
S3 (Raw Data Lake) → AWS Glue (ETL Processing) → RDS PostgreSQL → Analytics

## Services Used

- Amazon S3
- AWS Glue (Spark ETL)
- Amazon RDS (PostgreSQL)
- IAM
- VPC & Security Groups

## Features

- Risk scoring engine
- Automated ETL pipeline
- Duplicate handling
- Secure networking configuration
- SQL analytics queries

## Sample Analytics Queries

High-risk candidates:

```sql
SELECT name, risk_score
FROM candidate_screening
WHERE risk_level = 'High Risk';
