The benefits of data lakehouses are overstated

Data lakehouse architectures store data in object storage (like S3) to store data in open table formats (like Apache Iceberg or Delta Lake).
Broadly, open table formats make object storage seem less like a data lake and more like a data warehouse, with support for things like transactions and streaming.

The key benefits:

1. Object storage is cheap, accessible and minimises the risk of vendor lock-in.
2. Open table formats are, by design, open and can be queried by a variety of compute engines, so you can pick whatever meets your needs.

I think both of these benefits overstated for most organisations.

# Data lock in risk is quantifiable

You can quantify lock-in risk by calculating the cost of exporting your data from a data warehouse.

For example, imagine you store 1TiB of data in BigQuery. The batch extract job to export a table is free, as long as you co-locate the GCS bucket with the BigQuery dataset in the same region. Egress from Google Cloud to the internet is $0.085/1 gibibyte, so $87.04 for a one time transfer, plus \<$1 for the temporary GCS storage. In reality the transfer will be less, because you can compress the table using Parquet.

You should do this calculation for your particular circumstances to quantify lock-in risk instead of assuming that any lock in risk must be avoided.

# Data warehouses already offer compute flexibility

There are Spark connectors for data warehouses like [BigQuery](https://docs.cloud.google.com/managed-spark/docs/concepts/connectors/bigquery) and [Snowflake](https://docs.snowflake.com/en/user-guide/spark-connector).
They have APIs for their native SQL query engines.
Most enterprises just need SQL query engines for analytics, and can use Spark for everything else.

# Data lakehouses have limited access control

If you store data in object storage then your native access control mechanism is at the bucket level.
There is no table, column or row level access control.
This is a growing problem because people want to give AI agents tightly scoped access to data.
Bucket-level control is too blunt an instrument.

Fixing this problem by adding a data warehouse on top for governed marts just stacks one architectural pattern on top of another. It adds complexity and costs.

# Lakehouse metadata catalogs re-introduce vendor lock-in

To address this problem, vendors are adding access control functionality to the metadata catalog (e.g. in [Unity Catalog](https://docs.databricks.com/aws/en/data-governance/unity-catalog/access-control/), but this just re-introduces vendor lock-in at the governance layer.
This fundamentally undermines the argument for 

