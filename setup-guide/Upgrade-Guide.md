[**HOME**](Home) > **UPGRADE GUIDE**

On this page, we are posting the steps to upgrade sequentially after a Snowplow release with the latest version at the top. Here *sequentially* means from the previous to the following.

You can also use [Snowplow Version Matrix](Snowplow-version-matrix) as a guidance to the internal component dependencies for a particular release.

For easier navigation, please, follow the links below.

- [Snowplow 78 Great Hornbill](#r78) (**r78**) 2016-03-15
- [Snowplow 77 Great Auk](#r77) (**r77**) 2016-02-29
- [Snowplow 76 Changeable Hawk-Eagle](#r76) (**r76**) 2016-01-26
- [Snowplow 75 Long-Legged Buzzard](#r75) (**r75**) 2016-01-02
- [Snowplow 74 European Honey Buzzard](#r74) (**r74**) 2015-12-22
- [Snowplow 73 Cuban Macaw](#r73) (**r73**) 2015-12-04
- [Snowplow 72 Great Spotted Kiwi](#r72) (**r72**) 2015-10-15
- [Snowplow 71 Stork-Billed Kingfisher](#r71) (**r71**) 2015-10-02
- [Snowplow 70 Bornean Green Magpie](#r70) (**r70**) 2015-08-19
- [Snowplow 69 Blue-Bellied Roller](#r69) (**r69**) 2015-07-24
- [Snowplow 68 Turquoise Jay](#r68) (**r68**) 2015-07-23
- [Snowplow 67 Bohemian Waxwing](#r67) (**r67**) 2015-07-13
- [Snowplow 66 Oriental Skylark](#r66) (**r66**) 2015-06-16
- [Snowplow 65 Scarlet Rosefinch](#r65) (**r65**) 2015-05-08
- [Snowplow 64 Palila](#r64) (**r64**) 2015-04-16
- [Snowplow 63 Red-Cheeked Cordon-Bleu](#r63) (**r63**) 2015-04-02
- [Snowplow 62 Tropical Parula](#r62) (**r62**) 2015-03-17
- [Snowplow 61 Pygmy Parrot](#r61) (**r61**) 2015-03-02
- [Snowplow 60 Bee Hummingbird](#r60) (**r60**) 2015-02-03
- [Snowplow 0.9.14](#v0.9.14) (**v0.9.14**) 2014-12-31
- [Snowplow 0.9.13](#v0.9.13) (**v0.9.13**) 2014-12-01
- [Snowplow 0.9.12](#v0.9.12) (**v0.9.12**) 2014-11-26
- [Snowplow 0.9.11](#v0.9.11) (**v0.9.11**) 2014-11-10
- [Snowplow 0.9.10](#v0.9.10) (**v0.9.10**) 2014-11-06
- [Snowplow 0.9.9](#v0.9.9) (**v0.9.9**) 2014-10-27
- [Snowplow 0.9.8](#v0.9.8) (**v0.9.8**) 2014-09-18
- [Snowplow 0.9.7](#v0.9.7) (**v0.9.7**) 2014-09-02
- [Snowplow 0.9.6](#v0.9.6) (**v0.9.6**) 2014-07-26
- [Snowplow 0.9.5](#v0.9.5) (**v0.9.5**) 2014-07-09
- [Snowplow 0.9.4](#v0.9.4) (**v0.9.4**) 2014-05-30
- [Snowplow 0.9.3](#v0.9.3) (**v0.9.3**) 2014-05-21
- [Snowplow 0.9.2](#v0.9.2) (**v0.9.2**) 2014-04-30
- [Snowplow 0.9.1](#v0.9.1) (**v0.9.1**) 2014-04-11
- [Snowplow 0.9.0](#v0.9.0) (**v0.9.0**) 2014-02-04

<a name="r78" />
##Snowplow 78 Great Hornbill

This release brings our Kinesis pipeline functionally up-to-date with our Hadoop pipeline, and makes various further improvements to the Kinesis pipeline.

### Upgrade steps

The Kinesis apps for R78 Great Hornbill are now all available in a single zip file here:

```
http://dl.bintray.com/snowplow/snowplow-generic/snowplow_kinesis_r78_great_hornbill.zip
```

####Configuration file

Upgrading will require the following configuration changes to the applications' individual HOCON configuration files.

#####Scala Stream Collector

Add a `collector.cookie.name` field to the HOCON and set its value to `"sp"`.

Also note that the configuration file no longer supports loading AWS credentials from the classpath using [ClasspathPropertiesFileCredentialsProvider](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/auth/ClasspathPropertiesFileCredentialsProvider.html). If your configuration looks like this:

```json
{
	"aws": {
		"access-key": "cpf",
		"secret-key": "cpf"
	}
}
```

then you should change "cpf" to "default" to use the [DefaultAWSCredentialsProviderChain](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/auth/DefaultAWSCredentialsProviderChain.html). You will need to ensure that your credentials are available in one of the places the AWS Java SDK looks. For more information about this, see the [Javadoc](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/auth/DefaultAWSCredentialsProviderChain.html).

#####Kinesis Elasticsearch Sink

Replace the `sink.kinesis.out` string with an object with two fields:

```json
{
	"sink": {
		"good": "elasticsearch",  # or "stdout"
		"bad": "kinesis"          # or "stderr" or "none"
}
```

Additionally, move the `stream-type` setting from the `sink.kinesis.in` section to the `sink` section.

If you are loading Snowplow bad rows into for example Elasticsearch, please make sure to update all applications.

For a complete example, see our sample [`config.hocon`](https://github.com/snowplow/snowplow/blob/r78-great-hornbill/4-storage/kinesis-elasticsearch-sink/src/main/resources/config.hocon.sample) template.

### Read more

* [R78 Blog Post](http://snowplowanalytics.com/blog/2016/03/15/snowplow-r78-great-hornbill-released/)
* [R78 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r78-great-hornbill)

<a name="r77" />
##Snowplow 77 Great Auk

This release focuses on the command-line applications used to orchestrate Snowplow, bringing Snowplow up-to-date with the new 4.x series of Elastic MapReduce releases.

### Upgrade steps

Running EmrEtlRunner and StorageLoader as *Ruby* (rather than *JRuby* apps) is no longer actively supported.

The latest version of the EmrEtlRunner and StorageLoader are available from our Bintray [here](http://dl.bintray.com/snowplow/snowplow-generic/snowplow_emr_r77_great_auk.zip).

Note that the [`snowplow-runner-and-loader.sh`](https://github.com/snowplow/snowplow/blob/r77-great-auk/4-storage/storage-loader/bin/snowplow-runner-and-loader.sh) script has been also updated to use the *JRuby* binaries rather than the raw *Ruby* project.

####Configuration file

The recommended AMI version to run Snowplow is now 4.3.0 - update your configuration YAML as follows:

```yaml
emr:
  ami_version: 4.3.0 # WAS 3.7.0
```

You will need to update the jar versions in the same section:

```yaml
  versions:
    hadoop_enrich: 1.6.0        # WAS 1.5.1
    hadoop_shred: 0.8.0         # WAS 0.7.0
    hadoop_elasticsearch: 0.1.0 # UNCHANGED
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/r77-great-auk/3-enrich/emr-etl-runner/config/config.yml.sample) template.

### Read more

* [R77 Blog Post](http://snowplowanalytics.com/blog/2016/02/28/snowplow-r77-great-auk-released-with-emr-4.x-series-support/)
* [R77 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r77-great-auk)

<a name="r76" />
##Snowplow 76 Changeable Hawk-Eagle

This release introduces an event de-duplication process which runs on Hadoop, and also includes an important bug fix for our SendGrid webhook support.

### Upgrade steps

Upgrading to this release is simple - the only changed components are the jar versions for Hadoop Enrich and Hadoop Shred. 

####Configuration file

In the `config.yml` file for your EmrEtlRunner, update your `hadoop_enrich` and `hadoop_shred` job versions like so:

```yaml
  versions:
    hadoop_enrich: 1.5.1 # WAS 1.5.0
    hadoop_shred: 0.7.0 # WAS 0.6.0
    hadoop_elasticsearch: 0.1.0 # Unchanged
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/r76-changeable-hawk-eagle/3-enrich/emr-etl-runner/config/config.yml.sample) template. 

### Read more

* [R76 Blog Post](http://snowplowanalytics.com/blog/2016/01/26/snowplow-r76-changeable-hawk-eagle-released/)
* [R76 Release Notes](https://github.com/snowplow/snowplow/releases/r76-changeable-hawk-eagle)

<a name="r75" />
##Snowplow 75 Long-Legged Buzzard

This release lets you warehouse the event streams generated by Urban Airship and SendGrid, and also updates our [web-recalculate](https://github.com/snowplow/snowplow/tree/master/5-data-modeling/sql-runner/redshift/sql/web-recalculate) data model.

### Upgrade steps

####EmrEtlRunner and StorageLoader

The corresponding version of the EmrEtlRunner and StorageLoader are available from our Bintray [here](http://dl.bintray.com/snowplow/snowplow-generic/snowplow_emr_r75_long_legged_buzzard.zip).

In your EmrEtlRunner’s `config.yml` file, update your `hadoop_enrich` job’s version to 1.5.0, like so:

```yaml
  versions:
    hadoop_enrich: 1.5.0 # WAS 1.4.0
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/r75-long-legged-buzzard/3-enrich/emr-etl-runner/config/config.yml.sample) template.  

####Redshift

You'll need to deploy the Redshift tables for any webhooks you plan on ingesting into Snowplow. You can find the Redshift table deployment instructions on the corresponding webhook setup wiki pages:

- [SendGrid webhook Redshift setup](https://github.com/snowplow/snowplow/wiki/SendGrid-webhook-setup#22-redshift)
- [UrbanAirship Connect webhook Redshift setup](https://github.com/snowplow/snowplow/wiki/Urban-Airship-Connect-webhook-setup#22-redshift)

### Read more

* [R75 Blog Post](http://snowplowanalytics.com/blog/2016/01/02/snowplow-r75-long-legged-buzzard-released/)
* [R75 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r75-long-legged-buzzard)

<a name="r74" />
##Snowplow 74 European Honey Buzzard

This release adds a Weather Enrichment to the Hadoop pipeline - making Snowplow the first event analytics platform with built-in weather analytics!

Data provider: [OpenWeatherMap](http://openweathermap.org/)

### Upgrade steps

####Configuration files

To take advantage of this new enrichment, update the `hadoop_enrich` jar version in the `emr` section of your configuration YAML:

```yaml
  versions:
    hadoop_enrich: 1.4.0 # WAS 1.3.0
    hadoop_shred: 0.6.0 # UNCHANGED
    hadoop_elasticsearch: 0.1.0 # UNCHANGED
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/r74-european-honey-buzzard/3-enrich/emr-etl-runner/config/config.yml.sample) template.  

Make sure to add a [`weather_enrichment_config.json`](https://github.com/snowplow/snowplow/blob/master/3-enrich/config/enrichments/weather_enrichment_config.json) configured as explained [here](Weather-enrichment) into your `enrichments` folder too. The file should conform to this [JSON Schema](https://github.com/snowplow/iglu-central/blob/master/schemas/com.snowplowanalytics.snowplow.enrichments/weather_enrichment_config/jsonschema/1-0-0).

The corresponding JSONPaths file could be found [here](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/jsonpaths/org.openweathermap/weather_1.json).

####Redshift

If you are using Snowplow with Amazon Redshift, you will need to deploy the [org_openweathermap_weather_1](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/org.openweathermap/weather_1.sql) table into your database.

### Read more

* [R74 Blog Post](http://snowplowanalytics.com/blog/2015/12/22/snowplow-r74-european-honey-buzzard-with-weather-enrichment-released/)
* [R74 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r74-european-honey-buzzard)

<a name="r73" />
##Snowplow 73 Cuban Macaw

This release adds the ability to automatically load bad rows from the Snowplow Elastic MapReduce jobflow into [Elasticsearch](https://www.elastic.co/) for analysis, and formally separates the Snowplow enriched event format from the TSV format used to load Redshift.

### Upgrade steps

####EmrEtlRunner and StorageLoader

The corresponding version of the EmrEtlRunner and StorageLoader are available from our Bintray [here](http://dl.bintray.com/snowplow/snowplow-generic/snowplow_emr_r73_cuban_macaw.zip).

####Configuration file

You will need to update the jar versions in the `emr` section of your configuration YAML:

```yaml
  versions:
    hadoop_enrich: 1.3.0 # Version of the Hadoop Enrichment process
    hadoop_shred: 0.6.0 # Version of the Hadoop Shredding process
    hadoop_elasticsearch: 0.1.0 # Version of the Hadoop to Elasticsearch copying process
```

In order to start loading bad rows from the EMR jobflow into Elasticsearch, you will need to add an Elasticsearch target to the `targets` section of your configuration YAML.

```yaml
  targets:
    - name: "Our Elasticsearch cluster" # Name for the target - used to label the corresponding jobflow step
      type: elasticsearch # Marks the database type as Elasticsearch
      host: "ec2-43-1-854-22.compute-1.amazonaws.com" # Elasticsearch host
      database: snowplow # The Elasticsearch index
      port: 9200 # Port used to connect to Elasticsearch
      table: bad_rows # The Elasticsearch type
      es_nodes_wan_only: false # Set to true if using Amazon Elasticsearch Service
      username: # Not required for Elasticsearch
      password: # Not required for Elasticsearch
      sources: # Leave blank or specify: ["s3://out/enriched/bad/run=xxx", "s3://out/shred/bad/run=yyy"]
      maxerror:  # Not required for Elasticsearch
      comprows: # Not required for Elasticsearch
```

Note that the `database` and `table` fields actually contain the index and type respectively where bad rows will be stored.

The `sources` field is an array of buckets from which to load bad rows. If you leave this field blank, then the bad rows buckets created by the current run of the EmrEtlRunner will be loaded. Alternatively you can explicitly specify an array of bad row buckets to load.

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/r73-cuban-macaw/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Running EmrEtlRunner

Note these updates to EmrEtlRunner's command-line arguments:

- You can skip loading data into Elasticsearch by running EmrEtlRunner with the `--skip elasticsearch` option
- To run just the Elasticsearch load without any other EmrEtlRunner steps, explicitly skip all other steps using `--skip staging,s3distcp,enrich,shred,archive_raw`
- Note that running EmrEtlRunner with `--skip enrich,shred` will no longer skip the EMR job, since there is still the Elasticsearch step to run
- If you are using Postgres rather than Redshift, you should no longer pass the `--skip shred` option to EmrEtlRunner. This is because the shred step now removes JSON fields from the enriched event TSV.

####Database

Use the appropriate migration script to update your version of the `atomic.events` table to the relevant schema:

- [The Redshift migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/migrate_0.7.0_to_0.8.0.sql)
- [The PostgreSQL migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/postgres-storage/sql/migrate_0.6.0_to_0.7.0.sql)

If you are upgrading to this release from an older version of Snowplow, we also provide [Redshift migration scripts][migrate-redshift-sql] to `atomic.events` version 0.8.0 from 0.4.0, 0.5.0 and 0.6.0 versions.

**Warning**: these migration scripts will alter your `atomic.events` table in-place, deleting the `unstruct_event`, `contexts`, and `derived_contexts` columns. We recommend that you make a full backup before running these scripts.

### Read more

* [R73 Blog Post](http://snowplowanalytics.com/blog/2015/12/04/snowplow-r73-cuban-macaw-released/)
* [R73 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r73-cuban-macaw)

<a name="r72"></a>
##Snowplow 72 Great Spotted Kiwi

This release adds the ability to track clicks through the Snowplow Clojure Collector, adds a cookie extractor enrichment and introduces new de-duplication queries leveraging [R71](#r71)'s event fingerprint

### Upgrade steps

####Clojure Collector

This release bumps the Clojure Collector to version 1.1.0.

To upgrade to this release:

1. Download the new warfile by right-clicking on [this link](http://s3-eu-west-1.amazonaws.com/snowplow-hosted-assets/2-collectors/clojure-collector/clojure-collector-1.1.0-standalone.war) and selecting “Save As…”
2. Log in to your Amazon Elastic Beanstalk console
3. Browse to your Clojure Collector’s application
4. Click the “Upload New Version” and upload your warfile

####Configuration files

You need to update the version of the Enrich jar in your configuration file:

```yaml
    hadoop_enrich: 1.2.0 # Version of the Hadoop Enrichment process
```

If you wish to use the new cookie extractor enrichment, write a configuration JSON and add it to your `enrichments` folder. The example JSON can be found [here](https://github.com/snowplow/snowplow/blob/master/3-enrich/config/enrichments/cookie_extractor_config.json).

This default configuration is capturing the Scala Stream Collector's own `sp` cookie - in practice you would probably extract other more valuable cookies available on your domain. Each extracted cookie will end up a single derived context following the JSON Schema [`org.ietf/http_cookie/jsonschema/1-0-0`](https://github.com/snowplow/iglu-central/blob/master/schemas/org.ietf/http_cookie/jsonschema/1-0-0).

**Note**: This enrichment only works with events recorded by the Scala Stream Collector - the CloudFront and Clojure Collectors do not capture HTTP headers.

#####JSONPaths files

- [`org.ietf/http_cookie_1.json`](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/jsonpaths/org.ietf/http_cookie_1.json)
- [`com.snowplowanalytics.snowplow/uri_redirect_1.json`](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/jsonpaths/com.snowplowanalytics.snowplow/uri_redirect_1.json)

####Redshift

If you are using Snowplow with Amazon Redshift and wish to use the new cookie extractor enrichment, you will need to deploy the [`org_ietf_http_cookie_1`](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/org.ietf/http_cookie_1.sql) table into your database.

For the new URI redirect functionality, install the [`com_snowplowanalytics_snowplow_uri_redirect_1`](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/com.snowplowanalytics.snowplow/uri_redirect_1.sql) table.

### Read more

* [R72 Blog Post](http://snowplowanalytics.com/blog/2015/10/15/snowplow-r72-great-spotted-kiwi-released/)
* [R72 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r72-great-spotted-kiwi)

<a name="r71" />
##Snowplow 71 Stork-Billed Kingfisher

This release significantly overhauls Snowplow's handling of time and introduces event fingerprinting to support de-duplication efforts. It also brings our validation of unstructured events and custom context JSONs "upstream" from our Hadoop Shred process into our Hadoop Enrich process.

### Upgrade steps

####EmrEtlRunner and StorageLoader

The latest version of the EmrEtlRunner and StorageLoadeder are available from our Bintray [here](http://dl.bintray.com/snowplow/snowplow-generic/snowplow_emr_r71_stork_billed_kingfisher.zip).

Unzip this file to a sensible location (e.g. `/opt/snowplow-r71`).

####Configuration files

You should update the versions of the Enrich and Shred jars in your [configuration file][https://github.com/snowplow/snowplow/blob/r71-stork-billed-kingfisher/3-enrich/emr-etl-runner/config/config.yml.sample]:

```yaml
    hadoop_enrich: 1.1.0 # Version of the Hadoop Enrichment process
    hadoop_shred: 0.5.0 # Version of the Hadoop Shredding process
```

You should also update the AMI version field:

```yaml
    ami_version: 3.7.0
```

For each of your database targets, you must add the new `ssl_mode` field:

```
  targets:
    - name: "My Redshift database"
      ...
      ssl_mode: disable # One of disable (default), require, verify-ca or verify-full
```

If you wish to use the new event fingerprint enrichment, write a configuration JSON and add it to your `enrichments` folder. The example JSON can be found [here](https://github.com/snowplow/snowplow/blob/master/3-enrich/config/enrichments/event_fingerprint_enrichment.json).

####Database

Use the appropriate migration script to update your version of the `atomic.events` table to the corresponding schema:

- [The Redshift migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/migrate_0.6.0_to_0.7.0.sql)
- [The PostgreSQL migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/postgres-storage/sql/migrate_0.5.0_to_0.6.0.sql)

If you are ingesting Cloudfront access logs with Snowplow, use the [Cloudfront access log migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/com.amazon.aws.cloudfront/migrate_wd_access_log_1_r3_to_r4.sql) to update your `com_amazon_aws_cloudfront_wd_access_log_1` table.

### Read more

* [R71 Blog Post](http://snowplowanalytics.com/blog/2015/10/02/snowplow-r71-stork-billed-kingfisher-released/)
* [R71 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r71-stork-billed-kingfisher)

<a name="r70" />
##Snowplow 70 Bornean Green Magpie

This release focuses on improving our StorageLoader and EmrEtlRunner components and is the first step towards combining the two into a single CLI application.

### Upgrade steps

####EmrEtlRunner and StorageLoader

Download the EmrEtlRunner and StorageLoader from [Bintray](http://dl.bintray.com/snowplow/snowplow-generic/snowplow_emr_r70_bornean_green_magpie.zip).

Unzip this file to a sensible location (e.g. `/opt/snowplow-r70`).

Check that you have a compatible JRE (1.7+) installed:

```sh
$ ./snowplow-emr-etl-runner --version
snowplow-emr-etl-runner 0.17.0
```

####Configuration files

Your two old configuration files will no longer work. Use the aforementioned [`combine_configurations.rb`](https://github.com/snowplow/snowplow/blob/master/3-enrich/emr-etl-runner/config/combine_configurations.rb) script to turn them into a unified configuration file and a resolver JSON.

For reference:

- [`config/iglu_resolver.json`](https://github.com/snowplow/snowplow/blob/master/3-enrich/config/iglu_resolver.json) - example resolver JSON
- [`emr-etl-runner/config/config.yml.sample`][config-yml] - example unified configuration YAML

Note that field names in the unified configuration file no longer start with a colon - so `region: us-east-1` not `:region: us-east-1`.

####Running EmrEtlRunner and StorageLoader

The EmrEtlRunner now **requires** a `--resolver` argument which should be the path to your new resolver JSON.

Also note that when specifying steps to skip using the `--skip` option, the "*archive*" step has been renamed to "*archive_raw*" in the EmrEtlRunner and "*archive_enriched*" in the StorageLoader. This is in preparation for merging the two applications into one.

### Read more

* [R70 Blog Post](http://snowplowanalytics.com/blog/2015/08/19/snowplow-r70-bornean-green-magpie-released/)
* [R70 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r70-bornean-green-magpie)

<a name="r69" />
##Snowplow 69 Blue-Bellied Roller

This release contains new and updated SQL data models.

The SQL data models are a standalone and optional part of the Snowplow pipeline. Users who don't use the SQL data models are therefore not affected by this release.

### Upgrade steps

To implement the SQL data models, first execute the relevant [setup queries](https://github.com/snowplow/snowplow/tree/master/5-data-modeling/sql-runner/redshift/setup) in Redshift. Then use [SQL Runner](https://github.com/snowplow/sql-runner) to execute the [queries](https://github.com/snowplow/snowplow/tree/master/5-data-modeling/sql-runner/redshift/sql) on a regular basis. SQL Runner is an [open source app](https://github.com/snowplow/sql-runner) that makes it easy to execute SQL statements programmatically as part of the Snowplow data pipeline.

The web and mobile data models come in two variants: `recalculate` and `incremental`.

The `recalculate` models drop and recalculate the derived tables using all events, and can therefore be replaced without having to upgrade the tables.

The `incremental` models update the derived tables using only the events from the most recent batch. The updated `incremental` model comes with a [migration script](https://github.com/snowplow/snowplow/blob/master/5-data-modeling/sql-runner/redshift/migration/web-incremental-1-to-2/migration.sql).

### Read more

* [R69 Blog Post](http://snowplowanalytics.com/blog/2015/07/24/snowplow-r69-blue-bellied-roller-released/)
* [R69 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r69-blue-bellied-roller)

<a name="r68" />
##Snowplow 68 Turquoise Jay

This is a small release which adapts the EmrEtlRunner to use the new [Elastic MapReduce](http://aws.amazon.com/elasticmapreduce/) API.

### Upgrade steps

####EmrEtlRunner

You need to update EmrEtlRunner to the version **0.16.0** on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout r68-turquoise-jay
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
$ cd ../../4-storage/storage-loader
$ bundle install --deployment
```

### Read more

* [R68 Blog Post](http://snowplowanalytics.com/blog/2015/07/23/snowplow-r68-turquoise-jay-released/)
* [R68 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r68-turquoise-jay)

<a name="r67" />
##Snowplow 67 Bohemian Waxwing

This release brings a host of upgrades to our real-time [Amazon Kinesis](http://aws.amazon.com/kinesis/) pipeline as well as the embedding of Snowplow tracking into this pipeline.

### Upgrade steps

The Kinesis apps for r67 Bohemian Waxwing are now all available in a single zip file [here](http://dl.bintray.com/snowplow/snowplow-generic/snowplow_kinesis_r67_bohemian_waxwing.zip). Upgrading will require various configuration changes to each of the three applications’ HOCON configuration files.

####Scala Stream Collector

- Change `collector.sink.kinesis.stream.name` to `collector.sink.kinesis.stream.good` in the HOCON
- Add `collector.sink.kinesis.stream.bad` to the HOCON

####Scala Kinesis Enrich

If you want to include Snowplow tracking for this application please append the following:

```
enrich {

    ...

    monitoring {
        snowplow {
            collector-uri: ""
            collector-port: 80
            app-id: ""
            method: "GET"
        }
    }
}
```

Note that this is a wholly optional section; if you do not want to send application events to a second Snowplow instance, simply do not add this to your configuration file.

For a complete example, see our [`config.hocon.sample`](https://github.com/snowplow/snowplow/blob/r67-bohemian-waxwing/3-enrich/scala-kinesis-enrich/src/main/resources/config.hocon.sample) file.

####Kinesis Elasticsearch Sink

- Add `max-timeout` into the `elasticsearch` fields
- Merge location fields into the `elasticsearch` section
- If you want to include Snowplow Tracking for this application please append the following:

```
sink {

    ...

    monitoring {
        snowplow {
            collector-uri: ""
            collector-port: 80
            app-id: ""
            method: "GET"
        }
    }
}
```

Again, note that Snowplow tracking is a wholly optional section.

For a complete example, see our [`config.hocon.sample`](https://github.com/snowplow/snowplow/blob/r67-bohemian-waxwing/4-storage/kinesis-elasticsearch-sink/src/main/resources/config.hocon.sample) file.

### Read more

* [R67 Blog Post](http://snowplowanalytics.com/blog/2015/07/13/snowplow-r67-bohemian-waxwing-released/)
* [R67 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r67-bohemian-waxwing)

<a name="r66" />
##Snowplow 66 Oriental Skylark

This release upgrades our Hadoop Enrichment process to run on Hadoop 2.4, re-enables our Kinesis-Hadoop lambda architecture and also introduces a new scriptable enrichment powered by JavaScript.

### Upgrade steps

####EmrEtlRunner

You need to update EmrEtlRunner to the version **0.15.0** on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout r66-oriental-skylark
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
$ cd ../../4-storage/storage-loader
$ bundle install --deployment
```

####Configuration file

You need to update your EmrEtlRunner's `config.yml` file to reflect the new Hadoop 2.4.0 and AMI 3.6.0 support:

```
:emr:
  :ami_version: 3.6.0 # WAS 2.4.2
```

And:

```
  :versions:
    :hadoop_enrich: 1.0.0 # WAS 0.14.1
```

####JavaScript scripting enrichment

You can enable this enrichment by creating a self-describing JSON and adding into your `enrichments` folder. The [configuration JSON](https://github.com/snowplow/snowplow/blob/master/3-enrich/config/enrichments/javascript_script_enrichment.json) should validate against the [`javascript_script_config`](https://github.com/snowplow/iglu-central/blob/master/schemas/com.snowplowanalytics.snowplow/javascript_script_config/jsonschema/1-0-0) schema.

### Read more

* [R66 Blog Post](http://snowplowanalytics.com/blog/2015/06/16/snowplow-r66-oriental-skylark-released/)
* [R66 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r66-oriental-skylark)

<a name="r65" />
##Snowplow 65 Scarlet Rosefinch

This release greatly improves the speed, efficiency, and reliability of Snowplow’s real-time [Kinesis](http://aws.amazon.com/kinesis/) pipeline.

### Upgrade steps

####Kinesis applications

The Kinesis apps for r65 Scarlet Rosefinch are all available in a single zip file [here](http://dl.bintray.com/snowplow/snowplow-generic/snowplow_kinesis_r65_scarlet_rosefinch.zip).

####Configuration files

Upgrading will require various configuration changes to each of the four applications.

#####Scala Stream Collector

Add `backoffPolic`y and buffer fields to the configuration HOCON.

#####Scala Kinesis Enrich

- Add `backoffPolicy` and `buffer` fields to the configuration HOCON
- Extract the resolver from the configuration HOCON into its own JSON file, which can be stored locally or in DynamoDB
- Update the command line arguments as detailed [here](http://snowplowanalytics.com/blog/2015/05/08/snowplow-r65-scarlet-rosefinch-released/#dynamodb)

#####Kinesis LZO S3 Sink

- Rename the outermost key in the configuration HOCON from "connector" to "sink"
- Replace the "s3/endpoint" field with an "s3/region" field (such as `us-east-1`)

#####Kinesis Elasticsearch Sink

Rename the outermost key in the configuration HOCON from "connector" to "sink"

### Read more

* [R65 Blog Post](http://snowplowanalytics.com/blog/2015/05/08/snowplow-r65-scarlet-rosefinch-released/)
* [R65 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r65-scarlet-rosefinch)

<a name="r64"/>
##Snowplow 64 Palila

This is a major release which adds a new [data modeling](https://github.com/snowplow/snowplow/tree/master/5-data-modeling/sql-runner/redshift/) stage to the Snowplow pipeline, as well as fixes a small number of important bugs across the rest of Snowplow.

### Upgrade steps

####EmrEtlRunner

You need to update EmrEtlRunner to the code **0.14.0** on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout r64-palila
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
$ cd ../../4-storage/storage-loader
$ bundle install --deployment
```

####Configuration file

From this release onwards, you must specify IAM roles for Elastic MapReduce to use. If you have not already done so, you can create these default EMR roles using the [AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/installing.html), like so:

```
$ aws emr create-default-roles
```

Now update your EmrEtlRunner's `config.yml` file to add the default roles you just created:

```
:emr:
  :ami_version: 2.4.2       # Choose as per http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/emr-plan-ami.html
  :region: eu-west-1        # Always set this
  :jobflow_role: EMR_EC2_DefaultRole # NEW LINE
  :service_role: EMR_DefaultRole     # NEW LINE
```

This release also bumps the Hadoop Enrichment process to version **0.14.1**. Update `config.yml` like so:

```yaml
  :versions:
    :hadoop_enrich: 0.14.1 # WAS 0.14.0
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/r64-palila/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Database

This release widens the `mkt_clickid` field in `atomic.events`. You need to use the appropriate migration script to update to the new table definition:

- [The Redshift migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/migrate_0.5.0_to_0.6.0.sql)
- [The PostgreSQL migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/postgres-storage/sql/migrate_0.4.0_to_0.5.0.sql)

### Read more

* [R64 Blog Post](http://snowplowanalytics.com/blog/2015/04/16/snowplow-r64-palila-released/)
* [R64 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r64-palila)

<a name="r63" />
##Snowplow 63 Red-Cheeked Cordon-Bleu

This is a major release which adds two new enrichments, upgrades existing enrichments and significantly extends and improves our Canonical Event Model for loading into Redshift, Elasticsearch and Postgres.

The new and upgraded enrichments are as follows:

1. New enrichment: parsing useragent strings using the [`ua_parser`](https://github.com/ua-parser) library
2. New enrichment: converting the money amounts in e-commerce transactions into a base currency using [Open Exchange Rates](https://openexchangerates.org/signup?r=snowplow)
3. Upgraded: extracting click IDs in our campaign attribution enrichment, so that Snowplow event data can be more precisely joined with campaign data
4. Upgraded: our existing MaxMind-powered IP lookups
5. Upgraded: useragent parsing using the `user_agent_utils` library can now be disabled

### Upgrade steps

####Enrichments

To continue parsing useragent strings using the `user_agent_utils` library, you must add a new JSON configuration file into your folder of enrichment JSONs:

```
{
	"schema": "iglu:com.snowplowanalytics.snowplow/user_agent_utils_config/jsonschema/1-0-0",

	"data": {

		"vendor": "com.snowplowanalytics.snowplow",
		"name": "user_agent_utils_config",
		"enabled": true,
		"parameters": {}
	}
}
```

The name of the file is not important but must end in `.json`.

Configuring other enrichments is at your discretion. Useful resources here are:

- [Configurable enrichments wiki page](https://github.com/snowplow/snowplow/wiki/configurable-enrichments)
- [Example enrichment JSON configuration files](https://github.com/snowplow/snowplow/tree/master/3-enrich/config/enrichments)

####Elastic MapReduce Pipeline

There are two steps to upgrading the EMR pipeline:

1. Upgrade your EmrEtlRunner to use the latest Hadoop job versions
2. Upgrade your Redshift and/or Postgres `atomic.events` table to the relevant definitions

#####Configuration file

This release bumps:

- The Hadoop Enrichment process to version **0.14.0**
- The Hadoop Shredding process to version **0.4.0**

In your EmrEtlRunner's `config.yml` file, update your Hadoop jobs versions like so:

```yaml
  :versions:
    :hadoop_enrich: 0.14.0 # WAS 0.13.0
    :hadoop_shred: 0.4.0 # WAS 0.3.0
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/r63-red-cheeked-cordon-bleu/3-enrich/emr-etl-runner/config/config.yml.sample) template.

#####Database

You need to use the appropriate migration script to update to the new table definition:

- [The Redshift migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/migrate_0.4.0_to_0.5.0.sql)
- [The PostgreSQL migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/postgres-storage/sql/migrate_0.3.0_to_0.4.0.sql)

If you want to make use of the new *ua_parser* based useragent parsing enrichment in Redshift, you must also deploy the new table into your `atomic` schema:

- [com_snowplowanalytics_snowplow_ua_parser_context_1](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/com.snowplowanalytics.snowplow/ua_parser_context_1.sql)

####Kinesis pipeline

This release updates:

- *Scala Kinesis Enrich*, to version **0.4.0**
- *Kinesis Elasticsearch Sink*, to version **0.2.0**

The new version of the Kinesis pipeline is available on [Bintray](http://dl.bintray.com/snowplow/snowplow-generic/snowplow_kinesis_r61_red_cheeked_cordon_bleu.zip). The download contains the latest versions of all of the Kinesis apps (Scala Stream Collector, Scala Kinesis Enrich, Kinesis Elasticsearch Sink, and Kinesis S3 Sink).

#####Upgrading a live Kinesis pipeline

Our recommended approach for upgrading is as follows:

1. Kill your Scala Kinesis Enrich cluster
2. Leave your Kinesis Elasticsearch Sink cluster running until all remaining enriched events are loaded, then kill this cluster too
3. Upgrade your Scala Kinesis Enrich cluster to the new version
4. Upgrade your Kinesis Elasticsearch Sink cluster to the new version
5. Restart your Scala Kinesis Enrich cluster
6. Restart your Kinesis Elasticsearch Sink cluster

### Read more

* [R63 Blog Post](http://snowplowanalytics.com/blog/2015/04/02/snowplow-r63-red-cheeked-cordon-bleu-released/)
* [R63 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r63-red-cheeked-cordon-bleu)


<a name="r62" />
##Snowplow 62 Tropical Parula 

This release is designed to fix an incompatibility issue between [r61](#r61)'s EmrEtlRunner and some older Elastic Beanstalk configurations. It also includes some other EmrEtlRunner improvements.

### Upgrade steps

####EmrEtlRunner

You need to update EmrEtlRunner to the code **0.13.0** on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout r62-tropical-parula
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
$ cd ../../4-storage/storage-loader
$ bundle install --deployment
```

You **must** also update your EmrEtlRunner's configuration file, or else you will get a Contract failure on start. See the next section for details.

####Configuration file

Whether or not you use the new bootstrap option, you must update your EmrEtlRunner's `config.yml` file to include an entry for it:

In the `:emr:` section of your EmrEtlRunner's `config.yml` file, add in a `:bootstrap:` property like so:

```
:emr:
  ...
  :ec2_key_name: ADD HERE
  :bootstrap: []          # No custom boostrap actions
  :software:
    ...
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/r62-tropical-parula/3-enrich/emr-etl-runner/config/config.yml.sample) template.

### Read more

* [R62 Blog Post](http://snowplowanalytics.com/blog/2015/03/17/snowplow-r62-tropical-parula-released/)
* [R62 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r62-tropical-parula)

<a name="r61" />
##Snowplow 61 Pygmy Parrot

This release has a variety of new features, operational enhancements and bug fixes. The major additions are:

1. You can now parse Amazon CloudFront access logs using Snowplow
2. The latest Clojure Collector version supports Tomcat 8 and CORS, ready for cross-domain `POST` from JavaScript and ActionScript
3. EmrEtlRunner's failure handling and Clojure Collector log handling have been improved

### Upgrade steps

####EmrEtlRunner

You need to update EmrEtlRunner to the code **0.12.0** on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout r61-pygmy-parrot
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
$ cd ../../4-storage/storage-loader
$ bundle install --deployment
```

If you currently use `snowplow-runner-and-loader.sh`, upgrade to the [relevant version](https://github.com/snowplow/snowplow/blob/r61-pygmy-parrot/4-storage/storage-loader/bin/snowplow-runner-and-loader.sh) too.

####Configuration file

This release bumps the Hadoop Enrichment process to version **0.13.0**.

In your EmrEtlRunner's `config.yml` file, update your `hadoop_enrich` and `hadoop_shred` jobs' versions like so:

```
  :versions:
    :hadoop_enrich: 0.13.0 # WAS 0.12.0
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/r61-pygmy-parrot/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Clojure Collector

This release bumps the Clojure Collector to version **1.0.0**.

You will **not** be able to upgrade an existing Tomcat 7 cluster to use this version. Instead, to upgrade to this release:

1. Download the new warfile by right-clicking on [this link](http://d2io1hx8u877l0.cloudfront.net/2-collectors/clojure-collector/clojure-collector-1.0.0-standalone.war) and selecting "Save As…"
2. Log in to your Amazon Elastic Beanstalk console
3. Browse to your Clojure Collector's application
4. Click the "Launch New Environment" action
5. Click the "Upload New Version" and upload your warfile

When you are confident that the new collector is performing as expected, you can choose the "Swap Environment URLs" action to put the new collector live.

### Read more

* [R61 Blog Post](http://snowplowanalytics.com/blog/2015/03/02/snowplow-r61-pygmy-parrot-released/)
* [R61 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r61-pygmy-parrot)

<a name="r60" />
##Snowplow 60 Bee Hummingbird

This release focuses on the Snowplow Kinesis flow, and includes:

1. A new Kinesis “sink app” that reads the Scala Stream Collector’s Kinesis stream of raw events and stores these raw events in Amazon S3 in an optimized format
2. An updated version of our Hadoop Enrichment process that supports as an input format the events stored in S3 by the new Kinesis sink app

Together, these two features let you robustly archive your Kinesis event stream in S3, and process and re-process it at will using our tried-and-tested Hadoop Enrichment process. 

*Up until now, all Snowplow releases have used semantic versioning. We will continue to use semantic versioning for Snowplow's many constituent applications and libraries, but our releases of the Snowplow platform as a whole will be known by their release number plus a codename. The codenames for 2015 will be birds in ascending order of size, starting with the Bee Hummingbird.*

### Upgrade steps

####EmrEtlRunner

We recommend upgrading EmrEtlRunner to the version **0.11.0**, given the bugs fixed in this release. You also must upgrade if you want to use Hadoop to process the events stored by the Kinesis LZO S3 Sink.

Upgrade is as follows:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout r60-bee-hummingbird
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
$ cd ../../4-storage/storage-loader
$ bundle install --deployment
```

####Configuration file

This release bumps the Hadoop Enrichment process to version **0.12.0**.

In your EmrEtlRunner's config.yml file, update your `hadoop_enrich` job's version like so:

```
  :versions:
    :hadoop_enrich: 0.12.0 # WAS 0.11.0
```

If you want to run the Hadoop Enrichment process against the output of the Kinesis LZO S3 Sink, you will have to change the collector_format field in the configuration file to `thrift`:

```
:collector_format: thrift
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/r60-bee-hummingbird/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Kinesis pipeline

We are steadily moving over to Bintray for hosting binaries and artifacts which don't have to be hosted on S3. To make deployment easier, the Kinesis apps (Scala Stream Collector, Scala Kinesis Enrich, Kinesis Elasticsearch Sink, and Kinesis S3 Sink) are now all available in a single [zip file](http://dl.bintray.com/snowplow/snowplow-generic/snowplow_kinesis_r60_bee_hummingbird.zip).

### Read more

* [R60 Blog Post](http://snowplowanalytics.com/blog/2015/02/03/snowplow-60-bee-hummingbird-released/)
* [R60 Release Notes](https://github.com/snowplow/snowplow/releases/tag/r60-bee-hummingbird)

<a name="v0.9.14" />
##Snowplow 0.9.14

This release contains a variety of important bug fixes, plus support for three new event streams which can be loaded into your Snowplow event warehouse and unified log:

- Mandrill - for tracking email and email-related events delivered by [Mandrill](https://mandrill.com/)
- PagerDuty - for tracking incidents generated by [PagerDuty](http://www.pagerduty.com/)
- Pingdom - for tracking site outages detected by [Pingdom](https://www.pingdom.com/)

### Upgrade steps

####EmrEtlRunner

You need to update EmrEtlRunner to the code **0.10.0** on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout 0.9.14
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
$ cd ../../4-storage/storage-loader
$ bundle install --deployment
```

####Configuration file

This release bumps the Hadoop Enrichment process to version 0.11.0 and the Hadoop Shredding process to version 0.3.0.

In your EmrEtlRunner's `config.yml` file, update your hadoop_enrich and hadoop_shred jobs' versions like so:

```
  :versions:
    :hadoop_enrich: 0.11.0 # WAS 0.10.1
    :hadoop_shred: 0.3.0 # WAS 0.2.1
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.14/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Clojure Collector

This release bumps the Clojure Collector to version 0.9.1.

To upgrade to this release:

1. Download the new warfile by right-clicking on [this link](http://s3-eu-west-1.amazonaws.com/snowplow-hosted-assets/2-collectors/clojure-collector/clojure-collector-0.9.1-standalone.war) and selecting "Save As…"
2. Log in to your Amazon Elastic Beanstalk console
3. Browse to your Clojure Collector’s application
4. Click the "Upload New Version" and upload your warfile

####CloudFront Collector

You can find the new pixel in our GitHub repository as `2-collectors/cloudfront-collector/static/i` - upload this to S3, overwriting your existing pixel.

Remember to invalidate the pixel in your CloudFront distribution.

####Redshift

Make sure to deploy Redshift tables for any of the new webhooks that you plan on ingesting into Snowplow. You can find the Redshift table deployment instructions on the corresponding webhook setup wiki pages:

- [Mandrill webhook Redshift setup](https://github.com/snowplow/snowplow/wiki/Mandrill-webhook-setup)
- [Pingdom webhook Redshift setup](https://github.com/snowplow/snowplow/wiki/Pingdom-webhook-setup)
- [PagerDuty webhook Redshift setup](https://github.com/snowplow/snowplow/wiki/Pagerduty-webhook-setup)

### Read more

* [v0.9.14 Blog Post](http://snowplowanalytics.com/blog/2014/12/31/snowplow-0.9.14-released-with-additional-webhooks/)
* [v0.9.14 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.14)

<a name="v0.9.13" />
##Snowplow 0.9.13

This release is fixing two bugs found in the [previous](#v0.9.12) release:

1. Safer URI parsing
2. Dependency conflict with the version of Specs2 in Kinesis Enrich

### Upgrade steps

This release bumps Common Enrich to 0.9.1, Hadoop Enrich to version [0.10.1](s3://snowplow-hosted-assets/3-enrich/hadoop-etl/snowplow-hadoop-etl-0.10.1.jar), and Kinesis Enrich to [0.2.1](s3://snowplow-hosted-assets/3-enrich/scala-kinesis-enrich/snowplow-kinesis-enrich-0.2.1) with the latter two publically available on S3.

In your EmrEtlRunner's `config.yml` file, update your Hadoop enrich job's version to 0.10.1:

```
  :versions:
    :hadoop_enrich: 0.10.1
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.13/3-enrich/emr-etl-runner/config/config.yml.sample) template.

### Read more

* [v0.9.13 Blog Post](http://snowplowanalytics.com/blog/2014/12/01/snowplow-0.9.13-released-with-important-bug-fixes/)
* [v0.9.13 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.13)

<a name="v0.9.12" />
##Snowplow 0.9.12

This release significantly improves and extends our Kinesis support. The major new feature is our all new Kinesis Elasticsearch Sink, which streams event data from Kinesis into Elasticsearch in real-time. The data is then available to power real-time dashboards and analysis (e.g. using Kibana).

In addition to enabling real-time loading of data into Elasticsearch, we have made a number of other improvements to the real-time flow:

1. Bad rows of data are now loaded into a dedicated bad rows stream in Kinesis
2. The real-time flow now runs the latest version of Scala Common Enrich, making it possible to employ the same configurable enrichments in the real-time flow that are currently available in the batch flow.

This release also makes some improvements to Snowplow Common Enrich and Hadoop Enrich which should be invaluable for users of our batch-based event pipeline. 

### Upgrade steps

####Kinesis pipeline

There are several changes you need to make to move to the new versions of the Scala Stream Collector and Scala Kinesis Enrich:

- You must provide a "region" field (with a value like “us-east-1”) in the configuration files
- You must provide a "resolver" field in the Scala Kinesis Enrich containing the data used to configure the Iglu resolver
- If you run Scala Kinesis Enrich without the -enrichments option, the IP anonymization enrichment and the IP address lookup enrichment will not run automatically

New templates for the two configuration files can be found on GitHub (you will need to edit the AWS credentials and the stream names):

- [Scala Stream Collector configuration](https://github.com/snowplow/snowplow/blob/67ae2602c1d2e67fcd2824ed8c880de78e7c7916/2-collectors/scala-stream-collector/src/main/resources/config.hocon.sample)
- [Scala Kinesis Enrich configuration](https://github.com/snowplow/snowplow/blob/dbaaff2bd914b5eb9248d0ecd3d9d2c3ee6eaa16/3-enrich/scala-kinesis-enrich/src/main/resources/config.hocon.sample)

And a sample enrichment directory containing sensible configuration JSONs can be found [here](https://github.com/snowplow/snowplow/tree/master/3-enrich/config/enrichments).

####Hadoop pipeline

This release bumps the Hadoop Enrichment process to version 0.10.0.

In your EmrEtlRunner's `config.yml` file, update your Hadoop enrich job's version to 0.10.0, like so:

```
  :versions:
    :hadoop_enrich: 0.10.0 # WAS 0.9.0
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.12/3-enrich/emr-etl-runner/config/config.yml.sample) template.

### Read more

* [v0.9.12 Blog Post](http://snowplowanalytics.com/blog/2014/11/26/snowplow-0.9.12-released-with-real-time-load-into-elasticsearch-beta/)
* [v0.9.12 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.12)

<a name="v0.9.11" />
##Snowplow 0.9.11

For the first time, you can now use Snowplow to collect, store and analyze event streams generated by supported third-party software.

Many Software-as-a-Service vendors publish their own internal event streams for customers to consume - these event stream APIs are often referred to as "webhooks", sometimes as "streaming APIs", "postbacks" or "HTTP response APIs". Snowplow 0.9.11 adds first-class support for an initial set of these third-party webhooks.

For our initial 0.9.11 release we are adding support for three different webhook sources:

- MailChimp - for tracking email and email-related events delivered by [MailChimp](http://mailchimp.com/)
- CallRail - for tracking completed telephone calls recorded by [CallRail](http://www.callrail.com/)
- Iglu - for tracking [Iglu](https://github.com/snowplow/iglu)-compatible self-describing events, enabling you to use schema-less webhook APIs such as [AD-X Tracking](http://adxtracking.com/)

### Upgrade steps

####Configuration file

This release bumps the Hadoop Enrichment process to version 0.9.0.

In your EmrEtlRunner's `config.yml` file, update your Hadoop enrich job's version to 0.9.0, like so:

```
  :versions:
    :hadoop_enrich: 0.9.0 # WAS 0.8.0
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.11/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Clojure Collector

This release bumps the Clojure Collector to version 0.9.0.

To upgrade to this release:

1. Download the new warfile by right-clicking on [this link](http://s3-eu-west-1.amazonaws.com/snowplow-hosted-assets/2-collectors/clojure-collector/clojure-collector-0.9.0-standalone.war) and selecting "Save As…"
2. Log in to your Amazon Elastic Beanstalk console
3. Browse to your Clojure Collector's application
4. Click the “Upload New Version” and upload your warfile

####Redshift

If you have installed the `com_snowplowanalytics_snowplow_change_form_1` table following the [0.9.10](#v0.9.10) release, then please upgrade it by using the upgrade script, [`migrate_change_form_1_r1_to_r2.sql`](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/com.snowplowanalytics.snowplow/migrate_change_form_1_r1_to_r2.sql).

Also make sure to deploy Redshift tables for any webhooks you plan on ingesting into Snowplow. You can find the Redshift table deployment instructions on the corresponding webhook setup wiki pages:

- [MailChimp webhook Redshift setup](https://github.com/snowplow/snowplow/wiki/MailChimp-webhook-setup#22-redshift)
- [CallRail webhook Redshift setup](https://github.com/snowplow/snowplow/wiki/CallRail-webhook-setup#22-redshift)
- [Iglu webhook Redshift setup](https://github.com/snowplow/snowplow/wiki/Iglu-webhook-setup#22-redshift)

### Read more

* [v0.9.11 Blog Post](http://snowplowanalytics.com/blog/2014/11/10/snowplow-0.9.11-released-with-webhook-support/)
* [v0.9.11 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.11)

<a name="v0.9.10" />
##Snowplow 0.9.10

This is a minimalistic release designed to support the new events and context of the Snowplow JavaScript Tracker v2.1.1.

This release is primarily targeted at Snowplow users of Amazon Redshift who are upgrading to the Snowplow JavaScript Tracker (v2.1.0+).

### Upgrade steps

You will need to deploy the tables for any new events/context you want to support into your Amazon Redshift database. Make sure to deploy these into the same schema as your `events` table resides in.

You can find all Redshift table definitions in our GitHub repository under [`4-storage/redshift-storage/sql`](https://github.com/snowplow/snowplow/tree/master/4-storage/redshift-storage/sql).

The StorageLoader will automatically pick up the new JSON Paths files - you do not have need to deploy these.

### Read more

* [v0.9.10 Blog Post](http://snowplowanalytics.com/blog/2014/11/06/snowplow-0.9.10-released-for-js-tracker-2.1.0-support/)
* [v0.9.10 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.10)

<a name="v0.9.9" />
##Snowplow 0.9.9

This is primarily a comprehensive bug fix release, although it also adds the new `campaign_attribution` enrichment to our enrichment registry.

### Upgrade steps

####EmrEtlRunner and StorageLoader

You need to update EmrEtlRunner and StorageLoader to the code 0.9.2 and 0.3.3 respectively on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout 0.9.9
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
$ cd ../../4-storage/storage-loader
$ bundle install --deployment
```

####Configuration file

This release bumps the Hadoop Enrichment process to version 0.8.0.

In your EmrEtlRunner's `config.yml` file, update your Hadoop enrich job’s version to 0.8.0, like so:

```
  :versions:
    :hadoop_enrich: 0.8.0 # WAS 0.7.0
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.9/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Campaign attribution

*If you upgrade Hadoop Enrich to version 0.8.0 as above, you **must** also follow these steps, or else campaign attribution will be disabled.*

To use the new enrichment, add a "campaign_attribution.json" file containing a `campaign_attribution` enrichment JSON to your enrichments directory. Note that the previously automatic behaviour of populating the `mkt_` fields based on the `utm_` querystring fields no longer occurs by default. To reproduce it you **must** use the [Google-like manual tagging configuration](https://github.com/snowplow/snowplow/blob/master/3-enrich/config/enrichments/campaign_attribution.json).

####Clojure Collector

This release bumps the Clojure Collector to version 0.8.0.

To upgrade to this release:

1. Download the new warfile by right-clicking on [this link](http://s3-eu-west-1.amazonaws.com/snowplow-hosted-assets/2-collectors/clojure-collector/clojure-collector-0.8.0-standalone.war) and selecting "Save As…"
2. Log in to your Amazon Elastic Beanstalk console
3. Browse to your Clojure Collector's application
4. Click the "Upload New Version" and upload your warfile

### Read more

* [v0.9.9 Blog Post](http://snowplowanalytics.com/blog/2014/10/27/snowplow-0.9.9-released-with-campaign-attribution-enrichment/)
* [v0.9.9 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.9)

<a name="v0.9.8" />
##Snowplow 0.9.8

With this release we are adding event analytics support for iOS and Android applications. Mobile event analytics is a major step in Snowplow’s journey from a web analytics tool to a general-purpose event analytics platform.

Adding mobile support for Snowplow is really a few different releases:

- Snowplow 0.9.8, which adds POST support to our Clojure Collector and upgrades our Enrichment process to support POST payloads containing multiple events
- A new event tracker for iOS, see today’s accompanying [iOS Tracker blog post](http://snowplowanalytics.com/blog/2014/09/17/snowplow-ios-tracker-0.1.1-released/)
- A new event tracker for Android, see today’s accompanying [Android Tracker blog post](http://snowplowanalytics.com/blog/2014/09/17/snowplow-android-tracker-0.1.1-released/)
- New mobile-specific JSON Schemas available in Iglu Central, [mobile_context](http://www.iglucentral.com/schemas/com.snowplowanalytics.snowplow/mobile_context/jsonschema/1-0-0) and [geolocation_context](http://www.iglucentral.com/schemas/com.snowplowanalytics.snowplow/geolocation_context/jsonschema/1-0-0)

### Upgrade steps

####Configuration file

This release bumps the Hadoop Enrichment process to version 0.7.0.

In your EmrEtlRunner's `config.yml` file, update your Hadoop enrich job's version to 0.7.0, like so:

```
  :versions:
    :hadoop_enrich: 0.7.0 # WAS 0.6.0
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.8/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Clojure Collector

***Please make sure that you upgrade the Hadoop Enrichment process to 0.7.0 before upgrading your collector.***

This release bumps the Clojure Collector to version 0.7.0.

To upgrade to this release:

1. Download the new warfile by right-clicking on [this link](http://s3-eu-west-1.amazonaws.com/snowplow-hosted-assets/2-collectors/clojure-collector/clojure-collector-0.7.0-standalone.war) and selecting "Save As…"
2. Log in to your Amazon Elastic Beanstalk console
3. Browse to your Clojure Collector's application
4. Click the "Upload New Version" and upload your warfile

####Redshift

Both of the new trackers send mobile-related context conforming to the [mobile_context](http://www.iglucentral.com/schemas/com.snowplowanalytics.snowplow/mobile_context/jsonschema/1-0-0) JSON Schema, as a custom context automatically attached to each event.

If you are running Redshift, you can deploy the `mobile_context` table into your database using this [this script](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/com.snowplowanalytics.snowplow/mobile_context_1.sql).

The Android Tracker also optionally sends a geolocation-related context relating to the [geolocation_context](http://www.iglucentral.com/schemas/com.snowplowanalytics.snowplow/geolocation_context/jsonschema/1-0-0) JSON Schema; support for this in the iOS Tracker is planned soon.

### Read more

* [v0.9.8 Blog Post](http://snowplowanalytics.com/blog/2014/09/18/snowplow-0.9.8-released-for-mobile-analytics/)
* [v0.9.8 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.8)

<a name="v0.9.7" />
##Snowplow 0.9.7

This release is a "tidy-up" release which fixes some important bugs, particularly:

1. A bug in v0.9.5 onwards which was preventing events containing multiple JSONs from being shredded successfully 
2. Our Hive table definition falling behind Snowplow 0.9.6’s enriched event format updates
3. A bug in EmrEtlRunner causing issues running Snowplow inside some VPC environments

As well as these important fixes, 0.9.7 comes with a set of smaller bug fixes plus two new features:

- The ability to perform shredding without prior enrichment (i.e. shred an existing folder of enriched events)
- The ability to load Redshift from an S3 bucket in a region different to Redshift's own region

### Upgrade steps

####EmrEtlRunner and StorageLoader

You need to update EmrEtlRunner and StorageLoader to the  0.9.7 code release on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout 0.9.7
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
$ cd ../../4-storage/storage-loader
$ bundle install --deployment
```

####Configuration file

In your EmrEtlRunner's `config.yml` file, update your Hadoop shred job's version to 0.2.1, like so:

```
  :versions:
    ...
    :hadoop_shred: 0.2.1 # WAS 0.2.0
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.7/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Hive

Hive users can find the updated Hive file in our repository as [4-storage/hive-storage/hiveql/table-def.q](https://github.com/snowplow/snowplow/blob/0.9.7/4-storage/hive-storage/hiveql/table-def.q).

Note that enriched events generated by pre-0.9.6 Snowplow are not compatible with this updated Hive definition, and will need to be re-generated.

### Read more

* [v0.9.7 Blog Post](http://snowplowanalytics.com/blog/2014/09/02/snowplow-0.9.7-released-with-important-bug-fixes/)
* [v0.9.7 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.7)

<a name="v0.9.6" />
##Snowplow 0.9.6

This release does four things:

1. It fixes some important bugs discovered in Snowplow [0.9.5](#v0.9.5), related to our new shredding functionality
2. It introduces new JSON-based configurations for Snowplow's existing enrichments
3. It extends our geo-IP lookup enrichment to support all five of [MaxMind](https://www.maxmind.com/en/geolocation_landing?rld=snowplow)'s commercial databases
4. It extends our referer-parsing enrichment to support a user-configurable list of internal domains

### Upgrade steps

####EmrEtlRunner and StorageLoader

You need to update EmrEtlRunner and StorageLoader to the 0.9.6 code release on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout 0.9.6
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
$ cd ../../4-storage/storage-loader
$ bundle install --deployment
```

####Configuration file

Update your EmrEtlRunner's `config.yml` file. First update both of your Hadoop job versions to, respectively:

```
  :versions:
    :hadoop_enrich: 0.6.0 # WAS 0.5.0
    :hadoop_shred: 0.2.0 # WAS 0.1.0
```

Next, completely delete the `:enrichments:` section at the bottom:

```
:enrichments:
  :anon_ip:
    :enabled: true
    :anon_octets: 2
```

For a complete example, see our sample [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.6/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Enrichments

Finally, if you wish to use any of the configurable enrichments, you need to create a directory of configuration JSONs and pass that directory to the EmrEtlRunner using the new `--enrichments` option.

For help on this, please read our [release blog](http://snowplowanalytics.com/blog/2014/07/26/snowplow-0.9.6-released-with-configurable-enrichments/); also check out the example [enrichments directory](https://github.com/snowplow/snowplow/tree/master/3-enrich/config/enrichments), and review the [configuration guide](https://github.com/snowplow/snowplow/wiki/configurable-enrichments) for the new JSON-based enrichments.

**Important**: don’t forget to update any Bash script that you use to run your EmrEtlRunner job, to include the `--enrichments` argument. If you forget to do this, then all of your enrichments will be switched off. You can see updated versions of these Bash files here:

- [snowplow-emr-etl-runner.sh](https://github.com/snowplow/snowplow/blob/0.9.6/3-enrich/emr-etl-runner/bin/snowplow-emr-etl-runner.sh)
-[snowplow-runner-and-loader.sh](https://github.com/snowplow/snowplow/blob/0.9.6/4-storage/storage-loader/bin/snowplow-runner-and-loader.sh)

####Database

You need to use the appropriate migration script to update to the new table definition:

- [The Redshift migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/migrate_0.3.0_to_0.4.0.sql)
- [The PostgreSQL migration script](https://github.com/snowplow/snowplow/blob/master/4-storage/postgres-storage/sql/migrate_0.2.0_to_0.3.0.sql)

### Read more

* [v0.9.6 Blog Post](http://snowplowanalytics.com/blog/2014/07/26/snowplow-0.9.6-released-with-configurable-enrichments/)
* [v0.9.6 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.6)

<a name="v0.9.5" />
##Snowplow 0.9.5

This release makes Snowplow the first event analytics system to validate incoming event and context JSONs (using JSON Schema), and then automatically shred those JSONs into dedicated tables in Amazon Redshift.

### Upgrade steps

####EmrEtlRunner

You need to update EmrEtlRunner to the code release 0.9.5 on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout 0.9.5
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
```

You also need to update the [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.5/3-enrich/emr-etl-runner/config/config.yml.sample) file for EmrEtlRunner.  For more information on how to populate the new configuration file correctly, see the [Configuration section of the EmrEtlRunner setup guide](https://github.com/snowplow/snowplow/wiki/1-Installing-EmrEtlRunner#4-configuration).

####StorageLoader

You need to upgrade your StorageLoader installation to the  code 0.9.5 on Github:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout 0.9.5
$ cd snowplow/4-storage/storage-loader
$ bundle install --deployment
```

You also need to update the `config.yml` file for StorageLoader. 

####New Snowplow-authored events

If you want to add support for the new Snowplow-authored events e.g. link clicks to your Snowplow installation, this is a two step process:

1. Deploy the Redshift table definition available in the Snowplow repo into your Redshift database (same schema as `atomic.events`)
2. (If using Looker) deploy the LookML model available in the Snowplow repo into your Looker instance

####Custom events and contexts

Snowplow 0.9.5 lets you define your own custom unstructured events and contexts, and configure Snowplow to processing these from collection through into Redshift and even Looker.

Setting this up is outside of the scope of this release blog post. We have documented the process on our wiki, split into two pages:

1. [Configuring shredding in EmrEtlRunner](https://github.com/snowplow/snowplow/wiki/6-Configuring-shredding)
2. [Loading shredded types using StorageLoader](https://github.com/snowplow/snowplow/wiki/4-Loading-shredded-types)

### Read more

* [v0.9.5 Blog Post](http://snowplowanalytics.com/blog/2014/07/09/snowplow-0.9.5-released-with-json-validation-shredding/)
* [v0.9.5 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.5)

<a name="v0.9.4" />
##Snowplow 0.9.4

This release includes a new base LookML data model and dashboard to get Snowplow users started with [Looker](http://looker.com/).

The new base model has some significant improvements over the old one:

- Querying the data is much faster. When new Snowplow event data is loaded into Redshift, Looker automatically detects it and generates the relevant session-level and visitor-level derived tables, so that they are ready to be queried directly. We’ve tuned the derived tables with the relevant dist keys and sort keys to make sure any underlying table joins in Redshift are performant
- New visualizations are now supported including geographic plots
- Looker's new functionality around global filters: this makes it possible to drill into subsets of visitors by a range of dimensions, and see a wide range of different visualizations for that subset of users on the same screen, opening up new creative ways of exploring your Snowplow data 
- Metrics and dimensions have been renamed to make it easier for a new user unfamiliar with Snowplow to explore the data through Looker

### Upgrade steps

To make use of the new models, you'll need to have a Looker license or be on a Looker trial.

First you will need to load a new country codes dataset into Redshift / Postgres: this maps two character ISO country codes (outputed by our Maxmind enrichment) to three character ISO country codes (used by Looker for geographic visualizations) and country names.

Clone the Snowplow repo:

```
$ git clone https://github.com/snowplow/snowplow.git
```

You need to run the contents of `snowplow/5-data-modeling/reference-data/redshift/iso-country-codes.sql` in our Redshift database. This can be done using PSQL e.g.

```
psql -U $username -p $port -h $host -d $database -f snowplow/5-data-modeling/reference-data/redshift/iso-country-codes.sql
```

Alternatively you can copy and paste the [content](https://github.com/snowplow/snowplow/blob/master/5-data-modeling/reference-data/redshift/iso-country-codes.sql) of the file into your favorite SQL editor.

You then need to make sure that our Looker user has access to the new data. In PSQL, execute:

```sql
GRANT USAGE ON SCHEMA reference_data TO looker;
GRANT SELECT ON TABLE reference_data.country_codes TO looker;
```

Assuming that the user credentials you share with Looker have username "looker".

Next, you need to transfer our [LookML files from the Snowplow repo into the repo](https://github.com/snowplow/snowplow/tree/master/5-data-modeling/looker/lookml) you use for Looker, either directly (via Git) or by creating the files in the Looker UI (in the models section), and then copying and pasting the contents. Note that may need to update the [`snowplow.model.lookml`](https://github.com/snowplow/snowplow/blob/0.9.4/5-analytics/looker-analytics/lookml/snowplow.model.lookml) so that it references your connection in Redshift to your Snowplow dataset: the example file assumes that your connection is called "snowplow", which may not be the case.

Once copied over, you should be able to start exploring the "events", "sessions" and "visitors" views, and playing around directly with the "Traffic Pulse" dashboard.

### Read more

* [v0.9.4 Blog Post](http://snowplowanalytics.com/blog/2014/05/30/snowplow-0.9.4-released-with-updated-looker-models-and-dashboard/)
* [v0.9.4 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.4)

<a name="v0.9.3" />
##Snowplow 0.9.3

These release deals with incremental improvements to EmrEtlRunner, plus two important bug fixes for Clojure Collector users.

The first Clojure Collector issue was a problem in the file move functionality in EmrEtlRunner, which was preventing Clojure Collector users from scaling beyond a single instance without data loss. 

The second Clojure Collector issue involved the Elastic Beanstalk's Apache proxy's IP address(es) showing up in the `atomic.events` table in place of the expected end-user's IPs. We were unable to reproduce this issue when running multiple instances, so we do not believe this problem is as widespread.

### Upgrade steps

Upgrading is a two step process:

1. Update EmrEtlRunner
2. Update Clojure Collector [optional]

####EmrEtlRunner 

You need to update EmrEtlRunner to the code 0.7.0 on GitHub:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout 0.9.3
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
```

You also need to update your EmrEtlRunner's `config.yml` file in a few places. First add a logging section at the top:

```
:logging:
  :level: DEBUG # You can optionally switch to INFO for production
```

Next you need to replace this:

```
:emr:
  :hadoop_version: 1.0.3
```

with this:

```
:emr:
  :ami_version: 2.4.2
```

If you need to use a different Hadoop version, check out [this handy table](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/emr-plan-hadoop-version.html) to determine the correct AMI version.

Finally, add the region in:

```
:emr:
  :ami_version: 2.4.2
  :region: us-east-1 # Or your region
```

Your `:region:` will be your existing `:placement:` without the character on the end. Note that if you are running your EMR job in an EC2 subnet, you no longer need to set the `:placement:` field.

Once you have made these changes, do check your final version against the updated [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.3/3-enrich/emr-etl-runner/config/config.yml.sample) template.

####Clojure Collector

This release bumps the Clojure Collector to version **0.6.0**. Upgrading to this release is only necessary if you have been encountering the issue with proxy IPs appearing in `atomic.events`, as discussed in [this email thread](https://groups.google.com/forum/#!topic/snowplow-user/rCSrtBwpcac) (issue [#719](https://github.com/snowplow/snowplow/issues/719)).

To upgrade to this release:

1. Download the new warfile by right-clicking on [this link](http://s3-eu-west-1.amazonaws.com/snowplow-hosted-assets/2-collectors/clojure-collector/clojure-collector-0.6.0-standalone.war) and selecting “Save As…”
2. Log in to your Amazon Elastic Beanstalk console
3. Browse to your Clojure Collector's application
4. Click the “Upload New Version” and upload your warfile

### Read more

* [v0.9.3 Blog Post](http://snowplowanalytics.com/blog/2014/05/21/snowplow-0.9.3-released-with-clojure-collector-fixes/)
* [v0.9.3 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.3)

<a name="v0.9.2" />
##Snowplow 0.9.2

This release adds Snowplow support for the updated [CloudFront access log file format](http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/AccessLogs.html) introduced by Amazon on the morning of 29th April 2014. 

If you currently use the Snowplow CloudFront-based event collector, you are recommended to upgrade to this release as soon as possible.

As well as support for the new log file format, this release also features a new standalone Scalding job to make re-processing “bad” rows easier, and also some Hive script updates to bring our Hive support in step with our Postgres and Redshift schemas.

### Upgrade steps

Before upgrading, please ensure that you are on [Snowplow 0.9.1](#v0.9.1) version, which introduced changes to the Snowplow enriched event format.

*If you attempt to jump straight to 0.9.2 (from versions before 0.9.1), your enriched events will not load into your legacy Redshift or Postgres schema*.

####Configuration file

Upgrading is super simple: simply update the `config.yml` file for EmrEtlRunner to use the version 0.5.0 of the Hadoop ETL:

```
:snowplow:
  :hadoop_etl_version: 0.5.0
```

####Recover missing data

***Important**: since releasing this version of Snowplow, we have learnt that the suggested upgrade process listed below has the unfortunate side effect of URL-encoding all string columns in the recovered data. For that reason, we recommend updating to [Snowplow 0.9.3](#v0.9.3), where this bug is addressed.*

Any Snowplow batch runs *after* the CloudFront change but *before* your upgrade to 0.9.2 will have resulted in valid events ending up in your `bad` rows bucket. Happily, we can use the [Snowplow Hadoop Bad Rows](https://github.com/snowplow/snowplow/tree/master/3-enrich/scala-hadoop-bad-rows) job to recover them.

For every run to recover data from, you can run the Hadoop Bad Rows job using the [Amazon Ruby EMR client](http://aws.amazon.com/developertools/2264):

```
$ elastic-mapreduce --create --name "Extract raw events from Snowplow bad row JSONs" \
  --instance-type m1.xlarge --instance-count 3 \
  --jar s3://snowplow-hosted-assets/3-enrich/scala-bad-rows/snowplow-bad-rows-0.1.0.jar \
  --arg com.snowplowanalytics.hadoop.scalding.SnowplowBadRowsJob \
  --arg --hdfs \
  --arg --input --arg s3n://[[PATH_TO_YOUR_FIXABLE_BAD_ROWS]] \
  --arg --output --arg s3n://[[PATH_WILL_BE_STAGING_FOR_EMRETLRUNNER]]
```

Replace the `[[...]]` placeholders above with the appropriate bucket paths. ***Please note***: if you have multiple runs to fix, then we suggest running the above multiple times, one per run to fix, rather than running it against your whole bad rows bucket - it should be much faster.

Now you are ready to process the recovered raw events with Snowplow. Unfortunately, the filenames generated by the Bad Rows job are not compatible with the EmrEtlRunner currently (we will fix this in a future release). In the meantime, here is a workaround:

1. Edit `config.yml` and change `:collector_format: cloudfront` to `:collector_format: clj-tomcat`
2. Edit `config.yml` and point the `:processing:` bucket setting to wherever your extracted bad rows are located
3. Run EmrEtlRunner with `--skip staging`

If you are a Qubole and/or Hive user, you can find an alternative approach to recovering the bad rows in our blog post, [Reprocessing bad rows of Snowplow data using Hive, the JSON Serde and Qubole](http://snowplowanalytics.com/blog/2013/09/11/reprocessing-bad-data-using-hive-the-json-serde-and-qubole/).

### Read more

* [v0.9.2 Blog Post](http://snowplowanalytics.com/blog/2014/04/30/snowplow-0.9.2-released-for-new-cloudfront-log-format/)
* [v0.9.2 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.2)

<a name="v0.9.1" />
##Snowplow 0.9.1

This release introduces initial support for JSON-based custom unstructured events and custom contexts in the Snowplow Enrichment and Storage processes; this is the most-requested feature from our community and a key building block for mobile and app event tracking in Snowplow.

Snowplow’s event trackers have supported custom [unstructured events](https://github.com/snowplow/snowplow/wiki/2-Specific-event-tracking-with-the-Javascript-tracker#381-trackunstructevent) and [custom contexts](http://snowplowanalytics.com/blog/2014/01/27/snowplow-custom-contexts-guide/) for some time, but prior to 0.9.1 there had been no way of working with these JSON-based objects “downstream” in the rest of the Snowplow data pipeline. This release adds preliminary support like this:

1. Parse incoming custom unstructured events and contexts to ensure that they are valid JSON
2. Where possible, clean up the JSON (e.g. remove whitespace)
3. Store the JSON as `json`-type fields in Postgres, and in large `varchar` fields in Redshift

As well as this new JSON-based functionality, 0.9.1 also includes a host of additional features and updates.

### Upgrade steps

####EmrEtlRunner

You need to update EmrEtlRunner to the code 0.9.1 on Github:

```sh
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout 0.9.1
$ cd snowplow/3-enrich/emr-etl-runner
$ bundle install --deployment
```

You also need to update the `config.yml` file for EmrEtlRunner to use the Hadoop ETL version 0.4.0:

```
:snowplow:
  :hadoop_etl_version: 0.4.0
```

Don't forget to add in the new subnet (VPC) argument too:

```
:emr:
  ...
  :ec2_subnet_id: ADD HERE # Leave blank if not running in VPC
```

See a complete example of the EmrEtlRunner [`config.yml`](https://github.com/snowplow/snowplow/blob/0.9.1/3-enrich/emr-etl-runner/config/config.yml.sample) file on Github repo.

####StorageLoader

You need to upgrade your StorageLoader installation to the  code 0.9.1 on Github:

```
$ git clone git://github.com/snowplow/snowplow.git
$ git checkout 0.9.1
$ cd snowplow/4-storage/storage-loader
$ bundle install --deployment
```

####Database

We have updated the Redshift and Postgres table definitions for `atomic.events`. You can find the latest versions in the GitHub repository, along with migration scripts to handle the upgrade from recent prior versions. *Please review any migration script carefully before running and check that you are happy with how it handles the upgrade*.

Database | Table definition | Migration script
---|:---:|:---:
Redshift | [0.3.0](https://github.com/snowplow/snowplow/blob/0.9.1/4-storage/redshift-storage/sql/atomic-def.sql) | [Migrate from 0.2.2 to 0.3.0](https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/migrate_0.2.2_to_0.3.0.sql)
Postgres | [0.2.0](https://github.com/snowplow/snowplow/blob/0.9.1/4-storage/postgres-storage/sql/atomic-def.sql) | [Migrate from 0.1.x to 0.2.0](https://github.com/snowplow/snowplow/blob/master/4-storage/postgres-storage/sql/migrate_0.1.x_to_0.2.0.sql)

### Read more

* [v0.9.1 Blog Post](http://snowplowanalytics.com/blog/2014/04/11/snowplow-0.9.1-released-with-initial-json-support/)
* [v0.9.1 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.1)

<a name="v0.9.0" />
##Snowplow 0.9.0

This release introduces our initial beta support for [Amazon Kinesis](http://aws.amazon.com/kinesis/) in the Snowplow Collector and Enrichment components.

At Snowplow we are hugely excited about Kinesis's potential, not just to enable near-real-time event analytics, but more fundamentally to serve as a business’s unified log, aka its “digital nervous system”. This is a concept we introduced recently in our blog post [The three eras of business data processing](http://snowplowanalytics.com/blog/2014/01/20/the-three-eras-of-business-data-processing/), and further explored at the [Inaugural Kinesis London meetup](http://snowplowanalytics.com/blog/2014/01/30/inaugural-amazon-kinesis-meetup/).

### Upgrade steps

No upgrade steps as the release introduces the whole "new" concept. If you want to take it onboard you would need to set up a new environment.

### Read more

* [v0.9.0 Blog Post](http://snowplowanalytics.com/blog/2014/02/04/snowplow-0.9.0-released-with-beta-kinesis-support/)
* [v0.9.0 Release Notes](https://github.com/snowplow/snowplow/releases/tag/0.9.0)


[config-yml]: https://github.com/snowplow/snowplow/blob/master/3-enrich/emr-etl-runner/config/config.yml.sample
[migrate-postgre-sql]: https://github.com/snowplow/snowplow/tree/master/4-storage/postgres-storage/sql
[migrate-redshift-sql]: https://github.com/snowplow/snowplow/tree/master/4-storage/redshift-storage/sql
