<a name="top" />

[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) > [**Step 3: Setup Enrich**](Setting-up-enrich)

[[https://d3i6fms1cm1j0i.cloudfront.net/github-wiki/images/snowplow-architecture-3-enrichment.png]]

A Snowplow Enrich application processes data from a [Snowplow Collector](Setting-up-a-Collector),
and [stores enriched data](setting-up-alternative-data-stores) in a persistent database.

1. [Choose a Enrichment process](#choose)
2. [Setup your Enrichment process](#setup)

<a name="choose" />

## 1. Choose an Enrichment process

There are currently two Enrichment processes available for setup:

| **Enrichment platform**                                  | **Description**                                     | **Status**       |
|:-----------------------------------------------|:----------------------------------------------------|:-----------------|
| [EmrEtlRunner](setting-up-EmrEtlRunner)        | An application that parses logs from a Collector and stores enriched events to S3 | Production-ready |
| [Stream Enrich](setting-up-stream-enrich) | A Scala application that reads Thrift events from a Kinesis stream and outputs back to a Kinesis stream | Production-ready |
| [Beam Enrich](setting-up-beam-enrich) | An Apache Beam application that reads Thrift events from a PubSub topic and outputs back to a PubSub topic | Production-ready |

<a name="setup" />

## 2. Setup your Enrichment

Choose one of the following setup guides:

- [Setup EmrEtlRunner](setting-up-EmrEtlRunner)
- [Setup Stream Enrich](setting-up-stream-enrich)
- [Setup Beam Enrich](setting-up-beam-enrich)

Back to [Snowplow setup](Setting-up-Snowplow).
