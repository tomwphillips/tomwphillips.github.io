+++
title = 'The benefits of data lakehouses are overstated: open data != open platform'
date = 2026-08-26T21:25:32+01:00
draft = true
description = "Data lakehouses offer real benefits, but the case for cheaper storage, compute flexibility and reduced lock-in is more nuanced than vendors suggest."
[params]
	licence = "CC-BY-4.0"
+++

A [data lakehouse](https://www.cidrdb.org/cidr2021/papers/cidr2021_paper17.pdf) architecture stores data in open table formats (like [Apache Iceberg](https://iceberg.apache.org/) or [Delta Lake](https://delta.io/)) on object storage (like S3).
Broadly, open table formats make object storage behave less like a data lake and more like a data warehouse by adding support for things like transactions and streaming writes.

The usual arguments in favour of lakehouses are that object storage is cheap and reduces the risk of vendor lock-in, and that open table formats let you choose from a range of compute engines to meet your specific needs.

I think both benefits are overstated for most organisations.

# Data lock-in risk is quantifiable

You can quantify part of the lock-in risk by calculating the cost of exporting your data from a data warehouse.

Exporting 1 TiB from BigQuery to the internet costs $87.04.[^bq]
For many enterprises, this is a negligible contribution to their cloud bill.

You should do this calculation for your circumstances, instead of assuming "open" is automatically better.

# Data warehouses already offer compute flexibility

There are Spark connectors for data warehouses like [BigQuery](https://docs.cloud.google.com/managed-spark/docs/concepts/connectors/bigquery) and [Snowflake](https://docs.snowflake.com/en/user-guide/spark-connector).
Both have APIs for their native SQL query engines.
Many enterprises only need SQL query engines for analytics, and can use Spark for everything else, so the additional advantage of arbitrary compute engines for a lakehouse is smaller than advertised.

# Open table formats have limited access control

If you store data in an open table format on object storage, then the native access control mechanism is at the bucket-level.
This is a blunt instrument. Enterprises typically need fine-grained access controls at the table, column, and row levels.
These controls are becoming increasingly important as organisations want to give AI agents tightly scoped access to data.

Fixing this problem by adding a data warehouse on top for governed marts just stacks one architectural pattern on top of another. It adds complexity and costs.

# Lakehouse metadata catalogs create vendor lock-in

To address this problem vendors are adding access control functionality to metadata catalogs (e.g. in [Unity Catalog](https://docs.databricks.com/aws/en/data-governance/unity-catalog/access-control/)).
This weakens the argument that open table formats reduce lock-in, because to meet enterprise requirements, this moves vendor lock-in to the governance layer.

# No architecture is inherently superior

Despite what lakehouse vendors suggest, lakehouses are not inherently superior to warehouses.

To be clear, the case for a lakehouse is real. Object storage is cheap, open table formats are useful, and separating compute from storage can be beneficial.

The question is whether these benefits outweigh the additional complexity to match the functionality of data warehouses. Choosing one or the other involves trade-offs for your use case. Lock-in risks are present in both architectures, just in different places.

Open storage and open table formats give you portable data, but they don't necessarily give you an open data platform.

[^bq]: The [batch extract job to export a table is free](https://cloud.google.com/bigquery/pricing?hl=en#data-extraction-pricing), as long as you co-locate the GCS bucket with the BigQuery dataset in the same region. [Egress on standard tier pricing from Google Cloud to the internet from europe-west2 is $0.085/1 gibibyte](https://cloud.google.com/vpc/network-pricing?hl=en), so $87.04 for a one time transfer. There would be a small marginal cost for the temporary GCS storage. The actual transfer will be less, because you can compress the table using Parquet.
