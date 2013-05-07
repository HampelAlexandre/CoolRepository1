## Table of Contents

1. [Introduction](#intro)
2. [Tracking ad impressions](#adimps)
3. [Tracking ad clicks and conversions](#clicksconvs)

<a name="intro"/>
## Introduction

Although most of our Snowplow examples focus on website/app tracking, Snowplow is also a powerful tool for tracking and analyzing display ad impressions across your ad network or websites.

Simply append the Snowplow tracking code to your adserver's ad tags, and every ad impression (and thus web visitor) will be sent by Snowplow to Amazon S3 for warehousing. This is especially useful if you are using a hosted adserver which does not give you access to your own "atomic" ad impression data.

Warehousing your ad impression data on Amazon S3 is an excellent first step in building a custom Data Management Platform ([DMP] [dmp]) to start to define audience segments and track advertising effectiveness.

<a name="adimps"/>
## Tracking ad impressions

### Anatomy of a tracked impression

Tracking ad impressions is handled by a dedicated Snowplow JavaScript function, called `trackImpression()`. The arguments for impression tracking are as follows:

| **Name**       | **Required?** | **Description**                                                                              |
|---------------:|:--------------|:---------------------------------------------------------------------------------------------|
|     `BannerID` | Yes           | Adserver identifier for the ad banner (creative) being displayed                             |
|   `CampaignID` | No            | Adserver identifier for the ad campaign which the banner belongs to                          |
| `AdvertiserID` | No            | Adserver identifier for the advertiser which the campaign belongs to                         |
|       `UserID` | No            | Adserver identifier for the web user. Not to be confused with Snowplow's own user identifier |

You will want to set these arguments programmatically, across all of your ad zones/slots - for guidelines on how to achieve this with the [OpenX adserver] [openx], please see the following sub-sections.

### OpenX: Ad zone HTML append

Assuming that you do not have access to the host website(s) to add the Snowplow header script, you will need to add the Snowplow header script and Snowplow `trackImpression()` call as an append to each ad zone in your ad server.

Here's what the zone append functionality looks like in the OpenX adserver (OnRamp edition): 

![zoneappend] [zoneappend]

You will need to populate the ad zone append field with Snowplow tags for **every ad zone/unit** which you use to serve ads across your site or network. Read on for the Snowplow HTML code to use for OpenX. 

### OpenX: Snowplow impression tracking using magic macros

Because OpenX has a feature called [magic macros] [magicmacros], it is relatively straightforward to pass the banner, campaign and user ID arguments into the call to `trackImpression()` (advertiser ID is not available through magic macros).

The full HTML code to append, using asynchronous Snowplow invocation, looks like this:

```html
<!-- Snowplow starts plowing -->
<script type="text/javascript">
var _snaq = _snaq || [];

_snaq.push(['setCollectorCf', 'patldfvsg0d8w']); // Update to your CloudFront distribution subdomain
_snaq.push(['trackImpression', '{bannerid}', '{campaignid}', '', '{OAID}']); // OpenX magic macros. Leave this line as-is

(function() {
var sp = document.createElement('script'); sp.type = 'text/javascript'; sp.async = true; sp.defer = true;
sp.src = ('https:' == document.location.protocol ? 'https' : 'http') + '://d107t3sdgumbla.cloudfront.net/sp.js';
var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(sp, s);
})();
 </script>
<!-- Snowplow stops plowing -->
```

Once you have appended this code to all of your active ad zones, Snowplow should be collecting all of your ad impression data into Amazon S3.

<a name="clicksconvs"/>
## Tracking ad clicks and conversions

At the moment, Snowplow does not have built-in functionality to handle ad clicks or conversions. It may be possible to integrate Snowplow into your adserver for click and conversion tracking - please [contact us] [contactus] if you want to find out more.

An alternative approach is to ask your adserver provider to warehouse this data for you, and upload it to your Amazon S3 account.

[openx]: http://www.openx.com/publisher/enterprise-ad-server
[zoneappend]: /snowplow/snowplow/wiki/setup-guide/images/03a_zone_prepend_openx.png
[magicmacros]: http://www.openx.com/docs/whitepapers/magic-macros
[dmp]: http://www.adopsinsider.com/online-ad-measurement-tracking/data-management-platforms/what-are-data-management-platforms/ 
[contactus]: mailto:snowplow-ads@keplarllp.com