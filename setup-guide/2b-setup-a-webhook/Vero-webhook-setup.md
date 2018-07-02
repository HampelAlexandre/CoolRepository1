<a name="top" />

[**HOME**](Home) » [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) » [**Step 2b: setup a Webhook**](Setting-up-a-webhook) » [[Vero webhook setup]]

## Contents

- 1 [Overview](#overview)
  - 1.1 [Compatibility](#compat)
- 2 [Setup](#setup)
  - 2.1 [Vero](#setup-vero)
  - 2.2 [Snowplow Redshift](#setup-redshift)

<a name="overview" />

## 1. Overview

This webhook integration lets you track a variety of events logged by [Vero][vero-website].

Available events are:

- Bounced
- Clicked
- Created
- Delivered
- Opened
- Sent
- Unsubscribed
- Updated

For the technical implementation, see [[Vero webhook adapter]].

<a name="compat" />

### 1.1 Compatibility

* [Snowplow R107 Trypillia][snowplow-release]
* [Vero webhooks][vero-webhooks]

<a name="setup" />

## 2. Setup

Integrating Vero's webhooks into Snowplow is a two-stage process:

1. Configure Vero to send events to Snowplow
2. (Optional) Create the Vero events tables for Amazon Redshift

<a name="setup-vero" />

## 2.1 Vero

First login to Vero. Select **Settings** from the menu panel along the left-hand side of the screen, then navigate to **Integrations** in the Settings menu.

Select **Custom Integration (Webhooks)** from the list by clicking the row. Ensure it's switched **ON** in order to send events to Snowplow.

Click **View** on the right side of the **Custom Integration (Webhooks)** icon.

For the **Notification url** field you will need to provide the URI to your Snowplow Collector.  We use a special path to tell Snowplow that these events are generated by Vero:

```
http://<collector host>/com.getvero/v1
```

If you want, you can also manually override the event's `platform` parameter by appending a query string to the end of the URL so:

```
http://<collector host>/com.getvero/v1?p=<platform code>
```

Supported platform codes can again be found in the [Snowplow Tracker Protocol][tracker-protocol]; if not set, then the value for `platform` will default to `srv` for a server-side application.

The other values you can set up manually in the similar fashion are `nuid`, `aid`, `cv`, `eid`, `ttm`, and `url`.

Before we save our Vero webhook we can configure what types of events Vero will send to our webhook and what channels will trigger these events.  Simply select the boxes that are applicable to you and Vero will send these events to our webhook.

Once you click the **Save** button you are ready to receive events from Vero.

<a name="setup-redshift" />

## 2.2 Redshift

If you are running the Snowplow batch flow with Amazon Redshift, then you should deploy the relevant event tables into your Amazon Redshift.

You can find the table definitions here:

* [com_getvero_bounced_1.sql][bounced-sql]
* [com_getvero_clicked_1.sql][clicked-sql]
* [com_getvero_created_1.sql][created-sql]
* [com_getvero_delivered_1.sql][delivered-sql]
* [com_getvero_opened_1.sql][opened-sql]
* [com_getvero_sent_1.sql][sent-sql]
* [com_getvero_unsubscribed_1.sql][unsubscribed-sql]
* [com_getvero_updated_1.sql][updated-sql]

Make sure to deploy this table into the same schema as your `events` table.

That's it - with these tables deployed, your Vero events should automatically flow through into Redshift.

[vero-website]: https://www.getvero.com/
[vero-webhooks]: https://help.getvero.com/articles/setting-up-veros-webhooks
[tracker-protocol]: https://github.com/snowplow/snowplow/wiki/snowplow-tracker-protocol#1-common-parameters-platform-and-event-independent


[vero-adapter]: https://github.com/snowplow/snowplow/blob/master/3-enrich/scala-common-enrich/src/main/scala/com.snowplowanalytics.snowplow.enrich/common/adapters/registry/VeroAdapter.scala
[snowplow-release]: https://github.com/snowplow/snowplow/releases/tag/r107-trypillia

[bounced-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/bounced_1.sql
[clicked-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/clicked_1.sql
[created-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/created_1.sql
[delivered-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/delivered_1.sql
[opened-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/opened_1.sql
[sent-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/sent_1.sql
[unsubscribed-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/unsubscribed_1.sql
[updated-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/updated_1.sql