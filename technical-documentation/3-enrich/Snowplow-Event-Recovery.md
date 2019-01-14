[**HOME**](Home) > [**SNOWPLOW TECHNICAL DOCUMENTATION**](Snowplow-technical-documentation) > [**Enrich**](Enrich) > [[Snowplow Event Recovery]]

## Overview

The different Snowplow pipelines being all non-lossy, if something goes wrong during, for example,
schema validation or enrichment the payloads (alongside the errors that happened) are stored into a
bad rows storage solution, be it a data stream or object storage, instead of being discarded.

The goal of recovery is to fix the payloads contained in these bad rows so that they are ready to be
processed successfully by a Snowplow enrichment platform.

Snowplow Event Recovery lets you run data recoveries on data emitted by real-time Snowplow pipelines
on AWS and GCP.

## Recovery scenarios

The main ideas behind recovery, as presented here, are recovery scenarios. What are recovery
scenarios? They are modular and composable processing units that will deal with a specific case you
want to recover from.

As such, recovery scenarios are, at their essence, made of two things:

- an error filter, which will serve as a router between bad rows and the appropriate recovery
scenario
- a mutate function, which will "fix" the payload

For example, if we wanted to recover a set of bad rows consisting of:

- Bad rows that were created due to a missing schema
- Bad rows that were created due to the payload not conforming to its schema
- Bad rows that were created due to an enrichment failing

We would use a different recovery scenario for each of them, so three in total:

- a first recovery scenario consisting of
  - an error filter checking for missing schema errors
  - a mutate function which does nothing (assuming the schema has been added since the bad rows
occurred)
- a second recovery scenario consisting of
  - an error filter checking for payloads not conforming to their schema errors
  - a mutate function which makes the payloads fit their schema
- a third recovery scenario consisting of
  - an error filter checking for a particular enrichment failing errors
  - a mutate function which does nothing (assuming the enrichment was misconfigured and we just want
to rerun it)

### Out of the box recovery scenarios

For the most common recovery scenarios, it makes sense to support them out of the box and make them
accessible through the recovery job’s configuration which is covered in the next section.

| Name | Mutation | Example use case | Parameters |
|:----:|:--------:|:----------------:|:----------:|
| `PassThrough` | Does not mutate the payload in any way | A missing schema that was added after the fact | `error` |
| `ReplaceInQueryString` | Replaces part of the query string according to a regex | Misspecified a schema when using the Iglu webhook | `error`, `toReplace`, `replacement` |
| `RemoveFromQueryString` | Removes part of the query string according to a regex | Property was wrongfully tracked and is not part of the schema | `error`, `toRemove` |
| `ReplaceInBase64FieldInQueryString` | Replaces part of a base64 field in the query string according to a regex | Property was sent as a string but should be an numeric | `error`, `base64Field` (`cx` or `ue_px`), `toReplace`, `replacement` |
| `ReplaceInBody` | Replaces part of the body according to a regex | Misspecified a schema when using the Iglu webhook | `error`, `toReplace`, `replacement` |
| `RemoveFromBody` | Removes part of the body according to a regex | Property was wrongfully tracked and is not part of the schema | `error`, `toRemove` |
| `ReplaceInBase64FieldInBody` | Replaces part of a base64 field in the body according to a regex | Property was sent as a string but should be an numeric | `error`, `base64Field` (`cx` or `ue_px`), `toReplace`, `replacement` |

Note that, for every recovery scenario leveraging a regex, it's possible to use capture groups. For
example, to remove brackets but keep their content we would have a `toReplace` argument containing
`\\{.*\\}` and a `replacement` argument containing `$1` (capture groups are one-based numbered).

### Custom recovery scenario

If your recovery scenario is not covered by the ones listed above, you can define your own by
extending `RecoveryScenario`. To extend `RecoveryScenario` you will need two things:

- an `error` which will be used to filter the incoming bad rows
- a `mutate` function which will be used to actually modify the collector payload

As an example, we can define a path mutating recovery scenario in
[`RecoveryScenario.scala`](core/src/main/scala/com.snowplowanalytics.snowplow.event.recovery/RecoveryScenario.scala):

```scala
final case class ReplaceInPath(
  error: String,
  toReplace: String,
  replacement: String
) extends RecoveryScenario {
  def mutate(payload: CollectorPayload): CollectorPayload = {
    if (payload.path != null)
      payload.path = payload.path.replaceAll(toReplace, replacement)
    payload
  }
}
```

If you think your recovery scenario will be useful to others, please consider opening a pull
request!

## Configuration

Once you have identified the different recovery scenarios you will want to run, you can construct
the configuration that will be leveraging them. Here, we make use of each and every one of them as a
showcase.

```json
{
  "schema": "iglu:com.snowplowanalytics.snowplow/recoveries/jsonschema/1-0-0",
  "data": [
    # Schema com.acme/my_schema/jsonschema/1-0-0 was added after the fact
    {
      "name": "PassThrough",
      "error": "Could not find schema with key iglu:com.acme/my_schema/jsonschema/1-0-0 in any repository"
    },
    # Typo in the schema name when using the Iglu webhook
    {
      "name": "ReplaceInQueryString",
      "error": "Could not find schema with key iglu:com.snowplowanalytics.snowplow/screen_vie/jsonschema/1-0-0 in any repository",
      "toReplace": "schema=iglu%3Acom.snowplowanalytics.snowplow%2Fscreen_vie%2Fjsonschema%2F1-0-0",
      "replacement": "schema=iglu%3Acom.snowplowanalytics.snowplow%2Fscreen_view%2Fjsonschema%2F1-0-0"
    },
    # Removes illegal curlies in query strings (e.g. templates that haven't been filled)
    {
      "name": "RemoveFromQueryString",
      "error": "Exception extracting name-value pairs from querystring",
      "toRemove": "\\{.*\\}"
    },
    # Replaces a string by an integer in ue_px, it can be reused for ReplaceInBase64FieldInBody
    {
      "name": "ReplaceInBase64FieldInQueryString",
      "error": "instance type (string) does not match any allowed primitive type (allowed: [\"integer\"])\n    level: \"error\"\n    schema: {\"loadingURI\":\"#\",\"pointer\":\"/properties/sessionIndex\"",
      "base64Field": "ue_px",
      "toReplace": "\"sessionIndex\":\"(\\d+)\"",
      # $1 refers to the first capture group
      "replacement": "\"sessionIndex\":$1"
    },
    # Replaces the device created timestamp by a string
    {
      "name": "ReplaceInBody",
      "error": "instance type (integer) does not match any allowed primitive type (allowed: [\"string\"])\n    level: \"error\"\n    schema: {\"loadingURI\":\"#\",\"pointer\":\"/items/properties/dtm\"",
      "toReplace": "\"dtm\":(\\d+)",
      "replacement": "\"dtm\":\"$1\""
    },
    # Removes a field which shouldn't be there
    {
      "name": "RemoveFromBody",
      "error": "object instance has properties which are not allowed by the schema: [\"test\"]",
      "toRemove": "\"test\":\".*\",?"
    },
    # Same as ReplaceInBase64FieldInQueryString
    {
      "name": "ReplaceInBase64FieldInBody",
      "error": "instance type (string) does not match any allowed primitive type (allowed: [\"integer\"])\n    level: \"error\"\n    schema: {\"loadingURI\":\"#\",\"pointer\":\"/properties/sessionIndex\"",
      "base64Field": "ue_px",
      "toReplace": "\"sessionIndex\":\"(\\d+)\"",
      # $1 refers to the first capture group
      "replacement": "\"sessionIndex\":$1"
    },
    # Our custom recovery scenario, replaces a wrong Iglu webhook path
    {
      "name": "ReplaceInPath",
      "error": "Payload with vendor com.iglu and version v1 not supported",
      "toReplace": "com.iglu/v1",
      "replacement": "com.snowplowanalytics.iglu/v1"
    }
  ]
}
```

# See also:

+ [Setup guide][setup]
+ [Repository][sev]

[setup]: https://github.com/snowplow/snowplow/wiki/setting-up-snowplow-event-recovery
[sev]: https://github.com/snowplow-incubator/snowplow-event-recovery/

