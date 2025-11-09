# NY-TAXI-Analysis-Using-Spark-Pyspark

Small example project that downloads NYC TLC taxi CSVs, converts them to Parquet with a fixed schema, and runs a Spark SQL aggregation to produce a monthly revenue report.

## Repo layout

- [download_file_locally.sh](download_file_locally.sh) — helper script to download monthly gzipped CSVs from the DataTalksClub release.
- [taxi_schema.py](taxi_schema.py) — CSV → Parquet conversion using Spark. Defines schemas [`taxi_schema.green_schema`](taxi_schema.py) and [`taxi_schema.yellow_schema`](taxi_schema.py).
- [spark_sql_transformation.py](spark_sql_transformation.py) — reads Parquet, normalizes column names, unions green + yellow, and computes the monthly revenue report (outputs to `data/report/revenue/`). Key DataFrames: [`spark_sql_transformation.df_trips_data`](spark_sql_transformation.py), [`spark_sql_transformation.df_result`](spark_sql_transformation.py).

## Prerequisites

- Python 3.x
- Java (JDK) for PySpark
- PySpark and pandas installed in the environment:
  - pip install pyspark pandas
- Running in the provided dev container (Ubuntu 24.04) is recommended.

## Steps

1. Download raw monthly CSVs (example: yellow, year 2020)
   - Make the script executable and run:
     ```
     chmod +x [download_file_locally.sh](http://_vscodecontentref_/0)
     [download_file_locally.sh](http://_vscodecontentref_/1) yellow 2020
     ```
   - This saves files under `data/raw/<taxi_type>/<year>/<month>/`.

2. Convert CSVs to Parquet using the canonical schemas
   - Run the converter which uses the schemas defined in [`taxi_schema.py`](taxi_schema.py):
     ```
     python3 [taxi_schema.py](http://_vscodecontentref_/2)
     ```
   - Outputs are written under `data/pq/{green,yellow}/{year}/{month}/`.

3. Run the Spark SQL transformation and reporting
   - Run:
     ```
     python3 [spark_sql_transformation.py](http://_vscodecontentref_/3)
     ```
   - This script reads Parquet, unifies columns, and writes the revenue report to:
     ```
     data/report/revenue/
     ```

## Notes & tips

- The schemas are declared in [`taxi_schema.py`](taxi_schema.py) as [`taxi_schema.green_schema`](taxi_schema.py) and [`taxi_schema.yellow_schema`](taxi_schema.py). Adjust them if you need additional fields or types.
- The transformation script normalizes pickup/dropoff column names and sets a `service_type` literal before union. Inspect [`spark_sql_transformation.py`](spark_sql_transformation.py) if you need to change aggregation metrics or grouping.
- The project currently uses local-mode Spark (`SparkSession.builder.master("local[*]")`). For larger data or clusters, change the Spark configuration in both scripts.

## Output

- Final monthly revenue report (parquet): `data/report/revenue/`

## Troubleshooting

- If Spark fails to start, confirm Java and PySpark compatibility and environment variables.
- If downloads fail, verify network access from the container and that the DataTalksClub release paths are correct.