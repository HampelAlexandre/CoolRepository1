[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](Snowplow setup guide) > [**Storage**](choosing-a-storage-module) 

[[https://d3i6fms1cm1j0i.cloudfront.net/github-wiki/images/4-storage.png]] 

## Available storage modules

| **Storage database**                           | **Description**                                     | **Status**       |
|:-----------------------------------------------|:----------------------------------------------------|:-----------------|
| [S3 / Apache Hive](s3-hive-storage-setup)      | Store your Snowplow events data as flat files in Amazon S3. Use Apache Hive (developed at Facebook) to query that data directly. Scales horizontally. (To petabytes and beyond!) | Production-ready |
| [Infobright](infobright-storage-setup)         | Open source columnar database. Crunch through terabytes of data fast. | Production-ready |


## Databases on the Snowplow roadmap

We plan to grow the number of storage modules available, to include:

1. [PostgreSQL](http://www.postgresql.org/) (for companies without enormous volumes / web and application traffic)
2. [SkyDB](http://skydb.io/) event database
3. [Google bigQuery](https://developers.google.com/bigquery/) (for fast analysis of massive data sets)

## Selection considerations 

| **S3 / Apache Hive**                           | **Infobright**                                 | 
|:-----------------------------------------------|:-----------------------------------------------|
| Scales horizontally to petabytes and beyond    | Scales (vertically) to terabytes (not petabytes)     |
| Pay-as-you-go (on EMR) - each query costs $$$s | fixed cost (dedicated analytics server with LOTS of RAM) |
| Easy to query data using SQL-like syntax via Apache Hive | Easy to query using SQL directly     |
| Integrates with some 3rd party tools e.g. Toad for Cloud databases | Integrates with every analytics tool that integrates with MySQL. |

In general, we recommend using Infobright for smaller volumes of data (up to Terabytes) and S3 / Hive for bigger volumes of data.

Note: if you start off with one storage solution e.g. Infobright, it is reasonably straightforward to migrate to another if need be.

## The Snowplow Storage Loader

To make it easier to automate the loading of data from Amazon S3 into the different databases that Snowplow supports, we've built a [Storage Loader] (storageloader setup). Setup instructions can be found [here](storageloader setup). 

