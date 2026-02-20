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
```
## Replace the following 
```
.option("url", "jdbc:postgresql://<RDS_ENDPOINT>:5432/postgres")
.option("user", "<USERNAME>")
.option("password", "<PASSWORD>")
```

## The output of the Data analytics 
<img width="866" height="629" alt="Screenshot from 2026-02-18 11-16-47" src="https://github.com/user-attachments/assets/9dc2b650-d1fe-473d-90cc-eb966c3391fb" />

## Employee-screening ETL jobs 
<img width="1862" height="1048" alt="Screenshot from 2026-02-18 11-16-55" src="https://github.com/user-attachments/assets/629bfc72-573e-44c9-84fb-1e894af30173" />

## Place the following code into AWS-Glue script JOBS
```
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job
from pyspark.sql.functions import when, col

# Initialize Glue Context
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)

# Read raw data from S3
df = spark.read.csv(
    "s3://employee-screening-raw-data/raw/candidates_raw.csv",
    header=True,
    inferSchema=True
)

# Convert Yes/No to Boolean
df = df.withColumn("education_verified",
    when(col("education_verified") == "Yes", True).otherwise(False)
)

df = df.withColumn("employment_verified",
    when(col("employment_verified") == "Yes", True).otherwise(False)
)

df = df.withColumn("criminal_record",
    when(col("criminal_record") == "Yes", True).otherwise(False)
)

df = df.withColumn("address_verified",
    when(col("address_verified") == "Yes", True).otherwise(False)
)

# Risk Score Logic
df = df.withColumn("risk_score",
    (when(col("criminal_record") == True, 50).otherwise(0) +
     when(col("employment_verified") == False, 20).otherwise(0) +
     when(col("education_verified") == False, 15).otherwise(0) +
     when(col("address_verified") == False, 10).otherwise(0))
)

# Risk Level Classification
df = df.withColumn("risk_level",
    when(col("risk_score") >= 50, "High Risk")
    .when(col("risk_score") >= 21, "Medium Risk")
    .otherwise("Low Risk")
)

# Write to RDS PostgreSQL
df.write \
    .format("jdbc") \
    .option("url", "jdbc:postgresql://YOUR_RDS_ENDPOINT:5432/postgres") \
    .option("dbtable", "candidate_screening") \
    .option("user", "postgres") \
    .option("password", "YOUR_PASSWORD") \
    .mode("append") \
    .save()

job.commit()

```

## then run the job 
If everything runs without any error the spark engine has done the job and the resultant will be displayed after data computation in the Postgresql s3 bucket in proccessed files.

