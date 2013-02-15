To simplify setting up and running SnowPlow, the SnowPlow Analytics team provide public hosting for some of the SnowPlow sub-components. These hosted assets are publically available through Amazon Web Services (CloudFront and S3), and using them is free for SnowPlow community members.

As we release new versions of these assets, we will leave old versions unchanged on their existing URLs - so you won't have to upgrade your own SnowPlow installation unless you want to.

**While SnowPlow Analytics Ltd will make every reasonable effort to host these assets, we will not be liable for any failure to provide this service. All of the hosted assets listed below are freely available via [our GitHub repository] [snowplow-repo] and you are encouraged to host them yourselves.** 

The **current versions** of the assets hosted by the SnowPlow Analytics team are as follows:

## 1. Trackers

### 1.1 JavaScript tracker resources

The minified JavaScript tracker is hosted on CloudFront:

    http(s)://d1fc8wv8zag5ca.cloudfront.net/0.10.0/sp.js

**We will be updating this URL structure for the next version of the tracker.**

## 2. Collectors

### 2.1 Clojure collector resources

The Clojure collector packaged as a complete WAR file, ready for Amazon Elastic Beanstalk, is here:

    s3://snowplow-hosted-assets/2-collectors/clojure-collector/clojure-collector-0.2.0-standalone.war

Right-click on this [Download link] [war-download] to save it down locally.

## 3. ETL

### 3.1 Hive ETL resources

The Hive ETL process uses a HiveQL file and a Hive deserializer. These are both made available in a public Amazon S3 bucket, for SnowPlowers who are running their Hive ETL process on Amazon EMR:

#### HiveQL scripts

    s3://snowplow-emr-assets/hive/hiveql/hive-rolling-etl-0.5.5.q
    s3://snowplow-emr-assets/hive/hiveql/non-hive-rolling-etl-0.0.6.q

#### Hive deserializer

    s3://snowplow-emr-assets/hive/serdes/snowplow-log-deserializers-0.5.4.jar

## 4. Storage

No hosted assets currently.

## 5. Analytics

No hosted assets currently.

## See also

As well as these hosted assets for running SnowPlow, the SnowPlow Analytics team also make code components and libraries available through Ruby and Java artifact repositories.

Please see the [[Artifact repositories]] wiki page for more information.

[snowplow-repo]: https://github.com/snowplow/snowplow
[war-download]: http://s3-eu-west-1.amazonaws.com/snowplow-hosted-assets/2-collectors/clojure-collector/clojure-collector-0.2.0-standalone.war