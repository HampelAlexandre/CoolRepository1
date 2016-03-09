[**HOME**](Home) » **EVENTS AND CONTEXTS**

##Overview

**Snowplow** is an *event analytics platform*. Once you have set up one or more [Snowplow trackers](Setting-up-a-tracker), every time an event occurs, Snowplow should spot the event, generate a packet of data (context) to describe the event, and send that event into the *Snowplow data pipeline*.

> The **Snowplow pipeline** is built to enable a very clean separation of the following steps in the data processing flow:
> 
> - [Data collection](setting-up-a-collector)
> - [Data enrichment](setting-up-enrich)
> - [Data modelling](getting-started-with-data-modeling)
> - [Data analysis](getting-started-analyzing-snowplow-data)

To use Snowplow successfully, you need to have a good idea of:

- What events you care about in your business
- What events occur in your website / mobile application / server side systems / factories / call centers / dispatch centers / etc
- What decisions you make based on those events
- What you need to know about those events to make those decisions

That's where **event dictionary** comes into play. It is a document that defines the universe of events that a company is interested in tracking. For each event, it defines:

- What the event is. Often this might be illustrated e.g. with screenshots
- What data is captured when the event occurs, that represents the event. This is a data schema for the event
- Details on how the relevant Snowplow tracker has been setup to pass the event data into Snowplow

When an **event** occurs, it generally involves a number of **entities**, and takes place in a particular setting. Those entities we call **contexts**.

> A **context** is the group of entities associated with or describing the setting in which an **event** has taken place. 

In fact, we can consider an event a *named context* where "named" means a name given to a specific event. That is it signifies an action for which we gave a specific name and we are interested in a bunch of data (context) characterising the event.

Due to the nature of *custom* (as well as Snowplow authored) events/contexts there has to be some mechanism in place ensuring validity of the captured data. JSON schema plays a significant part in this mechanism. Both events and contexts have schemas which define what data is recorded about the event, or context, at data capture time.

> **JSON schema** specifies a *JSON*-based format to define the structure of JSON data for validation, documentation, and interaction control. 

<p></p>

> **JSON** (JavaScript Object Notation) is an open-standard format that uses human-readable text to transmit data objects consisting of attribute–value pairs.

Snowplow requires that you put together schemas for your events and contexts, ahead of data collection time. It then uses those schemas to process the data, in particular:

1. To validate that the data coming in is "good data" that conforms to the schema
2. Process the data correctly, in particular, shredding the JSONs that represent the data into tidy tables in Redshift suitable for analysis

**Iglu** is a key technology for making this possible. It is machine-readable, open-source *schema repository* for JSON Schema from the team at Snowplow Analytics. A schema repository (sometimes called a registry) is like `git` but holds data schemas instead of software or code.

###Further reading

To find out more about the concepts mentioned above and ultimately how to set up custom events and contexts and send them to Snowplow pipeline, follow the links below.

- [Events](Events-overview)
- [Contexts](Contexts-overview)
- [Event dictionary](Event-dictionary)
- [Iglu repository](Iglu-repository)