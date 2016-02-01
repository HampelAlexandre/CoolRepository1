<a name="top" />

[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) > [Step 3: Setting up Enrich](Setting-up-enrich) > [**Step 3.2: setting up Scala Kinesis Enrich**](Setting-up-Scala-Kinesis-Enrich) > [3: Running](Running-Scala-Kinesis-Enrich)

**This documentation is for version 0.5.0 of Scala Kinesis Enrich. Documentation for other versions is available:**

**[Version 0.1.0][v0.1]**  
**[Version 0.3.0][v0.3]**

## Running

Scala Kinesis Enrich is an executable jarfile which should be runnable from any Unix-like shell environment. Simply provide the configuration file as a parameter:

    $ ./scala-kinesis-enrich-0.3.0 --config my.conf --resolver file:resolver.json

This will start the Scala Kinesis Enrich app to read raw events from Kinesis and write enriched events back to Kinesis.

If you are using configurable enrichments, provide the path to your enrichments directory as a parameter:

    $ ./scala-kinesis-enrich-0.3.0 --config my.conf --resolver file:resolver.js --enrichments file:path/to/enrichments

If you are storing the resolver and/or enrichments in DynamoDB, use the "dynamodb:" prefix in place of the "file:" prefix:

    $ ./scala-kinesis-enrich-0.3.0 --config my.conf --resolver dynamodb:eu-west-1/ConfigurationTable/resolver --enrichments dynamodb:eu-west-1/ConfigurationTable/enrichment_

The above command that the enrichments and resolver are stored in a table named ConfigurationTable in eu-west-1, that the hash key for that table is "id", that the resolver JSON is stored in an item whose hash key has value "resolver", and the enrichments are stored in items whose hash keys have values beginning with "enrichment_".

### Running in local mode

When developing the [Scala collector](Setting-up-the-Scala-Stream-Collector) and [Kinesis enrichment](setting-up-scala-kinesis-enrich) components, we realized that there were strong parallels between the Kinesis stream processing paradigm and conventional Unix `stdio` I/O streams. As a result, we added the ability for:

1. Scala Stream Collector to write Snowplow raw events to [`stdout`][scala-out] instead of a Kinesis stream
2. Scala Kinesis Enrich to read Snowplow raw events from [`stdin`][kinesis-in], and write enriched events to `stdout`

This has a nice side-effect: it is possible to run Snowplow in a "local mode", where you simply pipe the output of Scala Stream Collector directly into Scala Kinesis Enrich, and can then see the generated enriched events printed to your console. You can run Snowplow in local mode with a shell script like this:

```sh
	
#!/bin/sh

echo "Piping local collector into local enrichment..."
./snowplow-stream-collector-0.1.0 --config ./collector.conf | ./snowplow-kinesis-enrich-0.1.0 --config ./enrich.conf

```

Make sure to set the sources and sinks in your configuration files ([Scala configuration template][scala-template], [Kinesis enrich template][kinesis-template]) to the relevant `stdio`/`stdout` settings.

Snowplow "local mode" could be helpful for debugging Snowplow tracker implementations before putting tags live.

### Configuring the log level

Scala Kinesis Enrich uses [slf4j logging][logging]. If you run the executable jarfile using the `java -jar` command, you can set the log level as a system property:

    $ java -jar -Dorg.slf4j.simpleLogger.defaultLogLevel=debug \
        scala-kinesis-enrich-0.3.0 --config my.conf --resolver file:resolver.json

This will also affect messages logged by the [Kinesis Client Library][kcl] (which Scala Kinesis Enrich uses to read from Kinesis.)

## All done?

You have setup Scala Kinesis Enrich! You are now ready to [setup alternative data stores](Setting-up-alternative-data-stores).

Return to the [setup guide](Setting-up-Snowplow).

[v0.1]: https://github.com/snowplow/snowplow/wiki/Run-Scala-Kinesis-Enrich-v0.1
[v0.3]: https://github.com/snowplow/snowplow/wiki/Run-Scala-Kinesis-Enrich-v0.3

[logging]: http://www.slf4j.org/api/org/slf4j/impl/SimpleLogger.html
[kcl]: https://github.com/awslabs/amazon-kinesis-client

[scala-out]: https://github.com/snowplow/snowplow/wiki/Configure-the-Scala-Stream-Collector#2-sinks
[scala-template]: https://github.com/snowplow/snowplow/wiki/Configure-the-Scala-Stream-Collector#template
[kinesis-in]: https://github.com/snowplow/snowplow/wiki/Configure-Scala-Kinesis-Enrich#source
[kinesis-template]: https://github.com/snowplow/snowplow/wiki/Configure-Scala-Kinesis-Enrich#template
