<a name="top" />

[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) > [Step 3: Setting up Enrich](Setting-up-enrich) > [Configurable enrichments](Configurable-enrichments) > referer-parser enrichment

### Compatibility

JSON Schema   [iglu:com.snowplowanalytics.snowplow/referer_parser/jsonschema/1-0-0][schema]  
Compatibility 0.9.6+  
Data provider [Snowplow referer-parser][referer-parser-repo]  

### Overview

The referer parser enrichment uses the [Snowplow referer-parser][referer-parser-repo] to extract attribution data from referer URLs. You can provide a list of internal subdomains which will be treated as "internal" rather than unknown.

*Note that we always use the original HTTP misspelling of 'referer' (and thus 'referal') in this project - never 'referrer'.*

### Example

```json
{
	"schema": "iglu:com.snowplowanalytics.snowplow/referer_parser/jsonschema/1-0-0",

	"data": {

		"name": "referer_parser",
		"vendor": "com.snowplowanalytics.snowplow",
		"enabled": true,
		"parameters": {
			"internalDomains": [
				"subdomain1.mysite.com",
				"subdomain2.mysite.com"
			]
		}
	}
}
```

In this example, if an event's referer URL is either "subdomain1.mysite.com" or "subdomain2.mysite.com" it will be counted as internal.

###Data sources

The input value for the enrichment comes from `refr` parameter which is mapped to `page_referrer` field in `atomic.events` table. Also the value representing the domain name of the server the current page servered from is taken from `url` parameter. This value ends up in the `page_urlhost` field of `atomic.events` table. It is extracted during common enrichment process by means of `ConversionUtils.explodeUri` function.

###Algorithm

The referer parser enrichment uses the [Snowplow referer-parser][referer-parser-repo] library to extract attribution data from referer URLs. There are 4 mediums that we support

- `unknown` for when we know the source, but not the medium
- `email` for webmail providers
- `social` for social media services
- `search` for search engines

The `referer-parser` identifies whether a URL is a known referer or not by checking it against the [`referers.yml`](https://github.com/snowplow/referer-parser/blob/master/resources/referers.yml) file.

Additionally the current page domain name (value for `page_urlhost`) is used to determine if this is an internal referer by comparing it with the domain names extracted from `internalDomains` parameter of [`referer_parser.json`](https://github.com/snowplow/snowplow/blob/master/3-enrich/config/enrichments/referer_parser.json).

###Data generated

Below is the summary of the fields in `atomic.events` table driven by the result of this enrichment (no dedicated table).

Field | Purpose
:---|:---
`refr_medium` | Type of referer (ex. 'search', 'internal')
`refr_source` | Name of referer if recognised
`refr_term` | Keywords if source is a search engine

[schema]: http://iglucentral.com/schemas/com.snowplowanalytics.snowplow/referer_parser/jsonschema/1-0-0
[referer-parser-repo]: https://github.com/snowplow/referer-parser
