To simplify setting up and running Snowplow, the Snowplow Analytics team provide public hosting for some of the Snowplow sub-components. These hosted assets are publically available through Amazon Web Services (CloudFront and S3), and using them is free for Snowplow community members.

As we release new versions of these assets, we will leave old versions unchanged on their existing URLs - so you won't have to upgrade your own Snowplow installation unless you want to.

**Disclaimer: While Snowplow Analytics Ltd will make every reasonable effort to host these assets, we will not be liable for any failure to provide this service. All of the hosted assets listed below are freely available via [our GitHub repository] [snowplow-repo] and you are encouraged to host them yourselves.** 

The **current versions** of the assets hosted by the Snowplow Analytics team are as follows:

## 0. Snowplow CLI

We are steadily moving over to [Bintray][bintray] for hosting binaries and artifacts which don't have to be hosted on S3.

To make operating Snowplow easier, the EmrEtlRunner and StorageLoader apps are now available as prebuilt executables in a single zipfile here:

    http://dl.bintray.com/snowplow/snowplow-generic/snowplow_emr_r71_stork_billed_kingfisher.zip

Right-click on this [Download link] [emr-download] to save it down locally.

## 1. Trackers

### 1.1 JavaScript Tracker resources

The minified JavaScript tracker is hosted on CloudFront against its full semantic version:

    http(s)://d1fc8wv8zag5ca.cloudfront.net/2.5.2/sp.js

### 2.1 Clojure Collector resources

The Clojure Collector packaged as a complete WAR file, ready for Amazon Elastic Beanstalk, is here:

    s3://snowplow-hosted-assets/2-collectors/clojure-collector/clojure-collector-1.1.0-standalone.war

Right-click on this [Download link] [cc-download] to save it down locally via CloudFront CDN.

### 2.2 Scala Stream Collector resources

See _6. Kinesis resources_ below.

## 3. Enrich

### 3.1 Scala Hadoop Enrich resources

The Scala Hadoop Enrich process uses a single jarfile containing the MapReduce job. This is made available in a public Amazon S3 bucket, for Snowplowers who are running their Hadoop Enrich process on Amazon EMR:

    s3://snowplow-hosted-assets/3-enrich/scala-hadoop-enrich/snowplow-hadoop-enrich-1.2.0.jar

Right-click on this [Download link] [hadoop-enrich-download] to save it down locally via CloudFront CDN.

### 3.2 Scala Hadoop Shred resources

The Scala Hadoop Shred process uses a single jarfile containing the MapReduce job. This is made available in a public Amazon S3 bucket, for Snowplowers who are running their Hadoop Enrich & Shred process on Amazon EMR:

    s3://snowplow-hosted-assets/3-enrich/scala-hadoop-shred/snowplow-hadoop-shred-0.5.0.jar

Right-click on this [Download link] [hadoop-shred-download] to save it down locally via CloudFront CDN.

### 3.3 Scala Hadoop Bad Rows resources

The Scala Hadoop Bad Rows tool uses a single jarfile containing the MapReduce job. This is made available in a public Amazon S3 bucket:

    s3://snowplow-hosted-assets/3-enrich/scala-bad-rows/snowplow-bad-rows-0.1.0.jar

Right-click on this [Download link] [hadoop-bad-rows-download] to save it down locally via CloudFront CDN.

### 3.4 Scala Kinesis Enrich resources

See _6. Kinesis resources_ below.

### 3.3 Shared resources

#### 3.31. MaxMind GeoLiteCity

Both Enrichment processes make use of the free [GeoLite City database] [geolite] from [MaxMind, Inc] [maxmind], also stored in this public Amazon S3 bucket:

    s3://snowplow-hosted-assets/third-party/maxmind/GeoLiteCity.dat

This file is updated every month by the Snowplow Analytics team.

If you are running Scala Kinesis Enrich, you will need a local copy of this file. Right-click on this [Download link] [glc-download] to save it down locally via CloudFront CDN.

## 4. Storage

### 4.1 Redshift Storage resources

Our shredding process for loading JSONs into Redshift uses a standard set of JSON Path files, available here:

    s3://snowplow-hosted-assets/4-storage/redshift-storage/jsonpaths

If you are running StorageLoader, these files will automatically be used for loading corresponding JSONs into Redshift.

### 4.2 Kinesis Elasticsearch Sink resources

See _6. Kinesis resources_ below.

### 4.4 Kinesis LZO S3 Sink resources

See _6. Kinesis resources_ below.

## 5. Analytics

No hosted assets currently.

## 6. Kinesis resources

We are steadily moving over to [Bintray][bintray] for hosting binaries and artifacts which don't have to be hosted on S3.

To make deployment easier, the Kinesis apps Scala Stream Collector, Scala Kinesis Enrich and Kinesis Elasticsearch Sink are now all available in a single zip file here:

    http://dl.bintray.com/snowplow/snowplow-generic/snowplow_kinesis_r67_bohemian_waxwing.zip

Right-click on this [Download link] [kinesis-download] to save it down locally.

The Kinesis S3 app is available for download separately here:

    http://dl.bintray.com/snowplow/snowplow-generic/kinesis_s3_0.3.0.zip

Right-click on this [Download link] [kinesis-s3-download] to save it down locally.

## See also

As well as these hosted assets for running Snowplow, the Snowplow Analytics team also make code components and libraries available through Ruby and Java artifact repositories.

Please see the [[Artifact repositories]] wiki page for more information.

[snowplow-repo]: https://github.com/snowplow/snowplow
[cc-download]: http://d2io1hx8u877l0.cloudfront.net/2-collectors/clojure-collector/clojure-collector-1.1.0-standalone.war
[hadoop-enrich-download]: http://d2io1hx8u877l0.cloudfront.net/3-enrich/scala-hadoop-enrich/snowplow-hadoop-enrich-1.2.0.jar
[hadoop-shred-download]: http://d2io1hx8u877l0.cloudfront.net/3-enrich/scala-hadoop-shred/snowplow-hadoop-shred-0.5.0.jar
[hadoop-bad-rows-download]: http://d2io1hx8u877l0.cloudfront.net/3-enrich/scala-bad-rows/snowplow-bad-rows-0.1.0.jar
[glc-download]: http://d2io1hx8u877l0.cloudfront.net/third-party/maxmind/GeoLiteCity.dat
[geolite]: http://dev.maxmind.com/geoip/legacy/geolite?rld=snowplow
[maxmind]: http://www.maxmind.com/?rld=snowplow

[bintray]: https://bintray.com/
[kinesis-download]: http://dl.bintray.com/snowplow/snowplow-generic/snowplow_kinesis_r67_bohemian_waxwing.zip
[kinesis-s3-download]: http://dl.bintray.com/snowplow/snowplow-generic/kinesis_s3_0.3.0.zip

[emr-download]: http://dl.bintray.com/snowplow/snowplow-generic/snowplow_emr_r71_stork_billed_kingfisher.zip