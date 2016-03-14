<a name="top" />

[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) > [**Step 4: setting up alternative data stores**](Setting-up-alternative-data-stores) > [Kinesis-Elasticsearch-Sink-Setup](Kinesis-Elasticsearch-Sink-Setup)

This documentation is for version 0.4.0 of Kinesis Elasticsearch Sink.  For previous versions:

**[Version 0.1.0 - 0.3.0][0.1]**

## Overview

If you are using [Snowplow Kinesis Enrich][ske] to write enriched Snowplow events to one stream and bad events to another, you can use the Kinesis Elasticsearch Sink to read events from either of those streams and write them to [Elasticsearch][elasticsearch].

__NOTE__: This application is not compatible with the [Amazon Elasticsearch Service](https://aws.amazon.com/elasticsearch-service/) as it does not currently support sending via the TransportClient; which the Elasticsearch Sink uses.

## Configuring Elasticsearch

### Getting started

First off, install and set up Elasticsearch version 1.4.0. For more information check out the [installation guide][installation-guide].

### Raising the file limit

Elasticsearch keeps a lot of files open simultaneously so you will need to increase the maximum number of files a user can have open. To do this:

```
sudo vim /etc/security/limits.conf
```

Append the following lines to the file:

```
{{USERNAME}} soft nofile 32000
{{USERNAME}} hard nofile 32000
```

Where {{USERNAME}} is the name of the user running Elasticsearch. You will need to logout and restart Elasticsearch before the new file limit takes effect.

To check that this new limit has taken effect you can run the following command from the terminal:

```
curl localhost:9200/_nodes/process?pretty
```

If the `max_file_descriptors` equals 32000 it is running with the new limit.

### Defining the mapping

Use the following request to create the mapping for the enriched event type:

```
curl -XPUT 'http://localhost:9200/snowplow' -d '{
    "settings": {
        "analysis": {
            "analyzer": {
                "default": {
                    "type": "keyword"
                }
            }
        }
    },
    "mappings": {
        "enriched": {
            "_timestamp" : {
                "enabled" : "yes",
                "path" : "collector_tstamp"
            },
            "_ttl": {
              "enabled":true,
              "default": "604800000"
            },
            "properties": {
                "geo_location": {
                    "type": "geo_point"
                }
            }
        }
    }
}'
```

Elasticsearch will then treat the collector_tstamp field as the timestamp and the geo_location field as a "geo_point". Documents will be automatically deleted one week (604800000 milliseconds) after their collector_tstamp.

This initialization sets the default analyzer to "keyword". This means that string fields will not be split into separate tokens for the purposes of searching. This saves space and ensures that URL fields are handled correctly.

If you want to tokenize specific string fields, you can change the "properties" field in the mapping like this:

```
curl -XPUT 'http://localhost:9200/snowplow' -d '{
    "settings": {
        "analysis": {
            "analyzer": {
                "default": {
                    "type": "keyword"
                }
            }
        }
    },
    "mappings": {
        "enriched": {
            "_timestamp" : {
                "enabled" : "yes",
                "path" : "collector_tstamp"
            },
            "_ttl": {
              "enabled":true,
              "default": "604800000"
            },
            "properties": {
                "geo_location": {
                    "type": "geo_point"
                },
                "field_to_tokenize": {
                    "type": "string",
                    "analyzer": "english"
                }
            }
        }
    }
}'
```

## Installing the Kinesis Elasticsearch Sink

You can choose to either:

1. Download the Kinesis Elasticsearch Sink executable jarfile, _or:_
2. Compile it from source

### Downloading the executable jarfile

To get a local copy, you can download the executable jarfile directly from our Hosted Assets bucket on Amazon S3 - please see our [[Hosted assets]] page for details.

You will need to add the executable flag onto the file:

```
$ chmod +x snowplow-elasticsearch-sink-0.1.0
```

### Compiling from source

Alternatively, you can build it from the source files. To do so, you will need [scala][scala] and [sbt][sbt] installed. 

To do so, clone the Snowplow repo:

```
$ git clone https://github.com/snowplow/snowplow.git
```

Navigate into the Kinesis Elasticsearch Sink folder:

```
$ cd 4-storage/kinesis-elasticsearch-sink
```

Use `sbt` to resolve dependencies, compile the source, and build an [assembled][assembly] fat JAR file with all dependencies.

```
$ sbt assembly
```

The `jar` file will be saved as `snowplow-elasticsearch-sink-0.1.0` in the `target/scala-2.10` subdirectory. It is now ready to be deployed.

## Using the Kinesis Elasticsearch Sink

### Configuration

The sink is configured using a HOCON file. These are the fields:

* `source`: Change this from "kinesis" to "stdin" to run the sink in local mode. You can pipe in the output of [Scala Kinesis Enrich][scala-kinesis-enrich] and the sink will log the resulting JSONs to stdout and any events which couldn't be converted to JSON to stderr.
* `sink`: Change this from "kinesis" to "stdouterr" to write to stdout rather than Elasticsearch and stderr rather than Kinesis. Log messages will still be logged to stdout, so make sure to filter them out if you intend to pipe the output of the sink to another process.
* `aws.access-key` and `aws.secret-key`: Change these to your AWS credentials. You can alternatively leave them as "default", in which case the [DefaultAWSCredentialsProviderChain][DefaultAWSCredentialsProviderChain] will be used.
* `kinesis.in.stream-name`: The name of the input Kinesis stream
* `kinesis.in.stream-type`: "good" if the input stream contains successfully enriched events; "bad" if it contains bad rows.
* `kinesis.in.initial-position`: Where to start reading from the stream the first time the app is run. "TRIM_HORIZON" for as far back as possible, "LATEST" for as recent as possibly.
* `kinesis.out.stream-name`: The name of the output Kinesis stream. Records which cannot be converted to JSON or can be converted but are rejected by Elasticsearch get sent here. If this stream doesn't exist already it will be created automatically.
* `kinesis.out.shards`: If the out stream doesn't exist, create it with this many shards.
* `kinesis.region`: The Kinesis region name to use.
* `kinesis.app-name`: Unique identifier for the app which ensures that if it is stopped and restarted, it will restart at the correct location.
* `elasticsearch.cluster-name`: The Elasticesarch cluster name
* `elasticsearch.endpoint`: The Elasticesarch cluster endpoint
* `elasticsearch.max-timeout`: The Elasticesarch maximum timeout in milliseconds
* `elasticsearch.index`: The Elasticsearch index name
* `elasticsearch.type`: The Elasticesarch type name
* `buffer.byte-limit`: Whenever the total size of the buffered records exceeds this number, they will all be sent to Elasticsearch.
* `buffer.record-limit`: Whenever the total number of buffered records exceeds this number, they will all be sent to Elasticsearch.
* `buffer.time-limit`: If this length of time passes without the buffer being flushed, the buffer will be flushed.

### Monitoring

You can also now include Snowplow Monitoring in the application.  This is setup through a new section at the bottom of the config.  You will need to ammend:

+ `monitoring.snowplow.collector-uri` insert your snowplow collector URI here.
+ `monitoring.snowplow.app-id` the app-id used in decorating the events sent.

If you do not wish to include Snowplow Monitoring please remove the entire `monitoring` section from the config.

An [example][conf-example] is available in the repo.

You will need to configure the names of the input and output streams.

### Execution

The Kinesis Elasticsearch Sink is an executable jarfile which should be runnable from any Unix-like shell environment. Simply provide the configuration file as a parameter:

```
$ ./kinesis-elasticsearch-sink-0.4.0 --config my.conf
```

This will start the process of reading events from Kinesis and writing them to an Elasticsearch cluster.

[ske]: Scala-Kinesis-Enrich
[elasticsearch]: http://www.elasticsearch.org/overview/
[installation-guide]: http://www.elastic.co/guide/en/elasticsearch/guide/current/_installing_elasticsearch.html
[DefaultAWSCredentialsProviderChain]: http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/auth/DefaultAWSCredentialsProviderChain.html
[scala-kinesis-enrich]: https://github.com/snowplow/snowplow/wiki/Scala-Kinesis-Enrich
[conf-example]: https://github.com/snowplow/snowplow/blob/r67-bohemian-waxwing/4-storage/kinesis-elasticsearch-sink/src/main/resources/config.hocon.sample
[0.1]: https://github.com/snowplow/snowplow/wiki/kinesis-elasticsearch-sink-setup-0.1.0
