<a name="top" />

[**HOME**](Home) > [**SNOWPLOW TECHNICAL DOCUMENTATION**](Snowplow technical documentation) > [**Trackers**](trackers) > [**JavaScript Tracker**](Javascript-Tracker) > Specific event tracking

This page refers to version 2 of the Snowplow JavaScript Tracker. Click [here] [specific-events-v1] for the corresponding documentation for version 1.

<a name="tracking-specific-events" />
## 3. Tracking specific events

Snowplow has been built to enable users to track a wide range of events that occur when consumers interact with their websites and webapps. We are constantly growing the range of functions available in order to capture that data more richly.

  - 3.1 [Pageviews](#page)  
    - 3.1.1 [`trackPageView`](#trackPageView)  
  - 3.2 [Pagepings](#pagepings)  
    - 3.2.1 [`enableActivityTracking`](#enableActivityTracking)  
  - 3.3 [Ecommerce transaction tracking](#ecommerce)  
    - 3.3.1 [`addTrans`](#addTrans)  
    - 3.3.2 [`addItem`](#addItem)  
    - 3.3.3 [`trackTrans`](#trackTrans)  
    - 3.3.4 [Pulling it all together: an example](#ecomm-example)
  - 3.4 [Social tracking](#social) 
    - 3.4.1 [`trackSocial`](#trackSocial) 
  - 3.5 [Campaign tracking](#campaign)  
    - 3.5.1 [Identifying paid sources](#identifying-paid-sources)  
    - 3.5.2 [Anatomy of the query parameter](#anatomy-of-the-query-parameter)
  - 3.6 [Ad impression tracking](#adimps) 
    - 3.6.1 [`trackImpression`](#trackImpression)
  - 3.7 [Tracking custom structured events](#custom-structured-events)  
    - 3.7.1 [`trackStructEvent`](#trackStructEvent)
  - 3.8 [Tracking custom unstructured events](#custom-unstructured-events)
    - 3.8.1 [`trackUnstructEvent`](#trackUnstructEvent)   
  - 3.9 [Link click tracking](#link-click-track)
    - 3.9.1 [`enableLinkTracking`](#enableLinkTracking)
  - 3.10 [Custom contexts](#custom-contexts)

<a name="tracking-specific-events" />
## 3. Tracking specific events

Snowplow has been built to enable you to track a wide range of events that occur when users interact with your websites and webapps. We are constantly growing the range of functions available in order to capture that data more richly.

<a name="page" />
### 3.1 Pageviews

Page views are tracked using the `trackPageView` method. This is generally part of the first Snowplow tag to fire on a particular web page. As a result, the `trackPageView` method is usually deployed with "global" method like `setAppId` and `setCollectorCf` in a single tag that also invokes the Snowplow JavaScript (sp.js) e.g.

```javascript
<!-- Snowplow starts plowing -->
<script type="text/javascript">
window._snaq = window._snaq || [];

window._snaq.push(['setCollectorCf', '{{CLOUDFRONT-DOMAIN}}']);
window._snaq.push(['setAppId', '{{MY-SITE-ID}}']);
window._snaq.push(['trackPageView']);

(function() {
var sp = document.createElement('script'); sp.type = 'text/javascript'; sp.async = true; sp.defer = true;
sp.src = ('https:' == document.location.protocol ? 'https' : 'http') + '://d1fc8wv8zag5ca.cloudfront.net/0.10.0/sp.js';
var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(sp, s);
})();
 </script>
<!-- Snowplow stops plowing -->
```

<a name="trackPageView" />
#### 3.1.1 `trackPageView`

Track pageview is called using the simple:

```javascript
snowplow_name_here('trackPageView');
```

This method automatically captures the URL, referrer and page title (inferred from the `<title>` tag.

If you wish, you can override the title with a custom value:

```javascript
snowplow_name_here('trackPageView', 'my custom page title');
```

Note: going forwards we plan to extend this method to also capture page category.

[Back to top](#top)  
[Back to JavaScript technical documentation contents][contents]

<a name="pagepings" />
### 3.2 Track engagement with a web page over time: page pings

As well as tracking page views, we can monitor whether a user continues to engage with a page over time, and record how he / she digests content on the page over time.

That is accomplished using 'page ping' events. If activity tracking is enabled, the web page is monitored to see if a user is engaging with it. (E.g. is the tab in focus, does the mouse move over the page, does the user scroll etc.) If any of these things occur in a set period of time, a page ping event fires, and records the maximum scroll left / right and up / down in the last ping period. If there is no activity in the page (e.g. because the user is on a different tab in his / her browser), no page ping fires.

<a name="enableActivityTracking" />
#### 3.2.1 `enableActivityTracking`

Page pings are enabled by:

```javascript
snowplow_name_here('enableActivityTracking', minimumVisitLength, heartBeat);
```

where `minimumVisitLength` is the time period from page load before the first page ping occurs, in seconds. Heartbeat is the number of seconds between each page ping, once they have started. So, if you executed:

```javascript
snowplow_name_here('enableActivityTracking', 30, 10);
snowplow_name_here('trackPageView');
```

The first ping would occur after 30 seconds, and subsequent pings every 10 seconds as long as the user continued to browse the page actively.

Notes: 

* In general this is executed as part of the main Snowplow tracking tag. As a result, you can elect to enable this on specific pages.
* The `enableActivityTracking` method **must** be called *before* the `trackPageView` method.

[Back to top](#top)  
[Back to JavaScript technical documentation contents][contents]

<a name="ecommerce" />
### 3.3 Ecommerce tracking

Modelled on Google Analytics ecommerce tracking capability, Snowplow uses three methods that have to be used together track online transactions:

1. **Create a transaction object**. Use `addTrans()` method to initialize a transaction object. This will be the object that is loaded with all the data relevant to the specific transaction that is being tracked including all the items in the order, the prices of the items, the price of shipping and the `order_id`.
2. **Add items to the transaction.** Use the `addItem()` method to add data about each individual item to the transaction object.
3. **Submit the transaction to Snowplow** using the trackTrans() method, once all the relevant data has been loaded into the object.

<a name="addTrans" >
#### 3.3.1 `addTrans`

The `addTrans` method creates a transaction object. It takes seven possible parameters, two of which are required:

| **Parameter**                  | **Required?** | **Example value** | 
|:-------------------------------|:--------------|:------------------|
| `order ID`                     | Yes           | '1234'            |
| `affiliation or store name`    | No            | 'Womens Apparel'  |
| `total spend`                  | Yes           | '19.99'           |
| `shipping cost`                | No            | '2.99'            |
| `city`                         | No            | 'San Jose'        | 
| `state or province`            | No            | 'California'      |
| `country`                      | No            | 'USA'             |

For example: 

```javascript
snowplow_name_here('addTrans',
    '1234',           // order ID - required
    'Acme Clothing',  // affiliation or store name
    '11.99',          // total - required
    '1.29',           // tax
    '5',              // shipping
    'San Jose',       // city
    'California',     // state or province
    'USA'             // country
  );
```

[Back to top](#top)  
[Back to JavaScript technical documentation contents][contents]

<a name="addItem" />
#### 3.3.2 `addItem`

The `addItem` method is used to capture the details of each product item included in the transaction. It should therefore be called once for each item.

There are six potential parameters that can be passed with each call, four of which are required:

| **Parameter**                  | **Required?**                                     | **Example value** |
|:-------------------------------|:--------------------------------------------------|:------------------|
| `order ID`                     | Yes (in order to associate item with transaction) | '1234'            |
| `SKU / product code`           | Yes                                               | 'pbz0001234'      |
| `product name`                 | No, but advisable (to make interpreting SKU easier) | 'Black Tarot'   |
| `category or variation`        | No                                                | 'Large'           |
| `unit price`                   | Yes                                               | '9.99'            |
| `quantity`                     | Yes                                               | '1'               |

For example:

```javascript
snowplow_name_here('addItem',
    '1234',           // order ID - required
    'DD44',           // SKU/code - required
    'T-Shirt',        // product name
    'Green Medium',   // category or variation
    '11.99',          // unit price - required
    '1'               // quantity - required
  );
```

<a name="trackTrans" />
#### 3.3.3 `trackTrans`

Once the transaction object has been created (using `addTrans`) and the relevant item data added to it using the `addItem` method, we are ready to send the data to the collector. This is initiated using the `trackTrans` method:

```javascript
snowplow_name_here(['trackTrans']);
```

<a name="ecomm-example" />
#### 3.3.4 Putting the three methods together: a complete example

```html
<html>
<head>
<title>Receipt for your clothing purchase from Acme Clothing</title>
<script type="text/javascript">

  ;(function(p,l,o,w,i,n,g){if(!p[i]){p.GlobalSnowplowNamespace=p.GlobalSnowplowNamespace||[];
  p.GlobalSnowplowNamespace.push(i);p[i]=function(){(p[i].q=p[i].q||[]).push(arguments)
  };n=l.createElement(o);g=l.getElementsByTagName(o)[0];n.async=1;
  n.src=w;g.parentNode.insertBefore(n,g)}}(window,document,'script','../../dist/snowplow.js','snowplow_name_here'));

  snowplow_name_here('newTracker', 'cf', 'd3rkrsqld9gmqf.cloudfront.net');
  snowplow_name_here('trackPageView');
  snowplow_name_here('enableLinkClickTracking');

  snowplow_name_here('addTrans',
    '1234',           // order ID - required
    'Acme Clothing',  // affiliation or store name
    '11.99',          // total - required
    '1.29',           // tax
    '5',              // shipping
    'San Jose',       // city
    'California',     // state or province
    'USA'             // country
  );

   // add item might be called for every item in the shopping cart
   // where your ecommerce engine loops through each item in the cart and
   // prints out _addItem for each
  snowplow_name_here('addItem',
    '1234',           // order ID - required
    'DD44',           // SKU/code - required
    'T-Shirt',        // product name
    'Green Medium',   // category or variation
    '11.99',          // unit price - required
    '1'               // quantity - required
  );

  // trackTrans sends the transaction to Snowplow tracking servers.
  // Must be called last to commit the transaction.
  snowplow_name_here(['trackTrans']); //submits transaction to the collector

</script>
</head>
<body>

  Thank you for your order.  You will receive an email containing all your order details.

</body>
</html>
```

[Back to top](#top)  
[Back to JavaScript technical documentation contents][contents]

<a name="social" />
### 3.4 Social tracking

Social tracking has not been implemented yet. However, the intention is to use a similar tracking function to that employed by Google Analytics.

Social tracking will be used to track the way users interact with Facebook, Twitter and Google + widgets, e.g. to capture "like this" or "tweet this" events.

<a name="trackSocial" />
#### 3.4.1 `trackSocial`

The `trackSocial` method takes four parameters:

| **Parameter** | **Description** | **Required?** | **Example value**     | 
|:--------------|:----------------|:--------------|:----------------------|
| `network`     | Social network  | Yes           | 'facebook', 'twitter' |
| `socialAction`| Social action performed | Yes           | 'like', 'retweet'     |
| `opt_target`  | Object social action is performed on e.g. page ID, product ID | No            | '19.99'               |
| `opt_pagePath`| Page path of URL on which social action was carried out | No            | '2.99'                |

The method is executed in as:

```javascript
snowplow_name_here('trackSocial', network, socialAction, opt_target, opt_pagePath);
```

For example:

```javascript
snowplow_name_here('trackSocial', 'facebook', 'like', 'pbz00123', '/products/tarot/123-original-rider-waite');
```

Or if the optional parameters were left off:

```javascript
snowplow_name_here('trackSocial', 'facebook', 'like');
```
[Back to top](#top)  
[Back to JavaScript technical documentation contents][contents]

<a name="campaign" />
### 3.5 Campaign tracking

Campaign tracking is used to identify the source of traffic coming to a website.

At the highest level, we can distinguish **paid** traffic (that derives from ad spend) with **non paid** traffic: visitors who come to the website by entering the URL directly, clicking on a link from a referrer site or clicking on an organic link returned in a search results, for example.

In order to identify **paid** traffic, Snowplow users need to set five query parameters on the links used in ads. Snowplow checks for the presence of these query parameters on the web pages that users load: if it finds them, it knows that that user came from a paid source, and stores the values of those parameters so that it is possible to identify the paid source of traffic exactly.

If the query parameters are not present, Snowplow reasons that the user is from a **non paid** source of traffic. It then checks the page referrer (the url of the web page the user was on before visiting our website), and uses that to deduce the source of traffic:

1. If the URL is identified as a search engine, the traffic medium is set to "organic" and Snowplow tries to derive the search engine name from the referrer URL domain and the keywords from the  query string.
2. If the URL is a non-search 3rd party website, the medium is set to "referrer". Snowplow derives the source from the referrer URL domain.

<a name="identifying-paid-sources" />
#### 3.5.1 Identifying paid sources

Your different ad campaigns (PPC campaigns, display ads, email marketing messages, Facebook campaigns etc.) will include one or more links to your website e.g.:

```html
<a href="http://mysite.com/myproduct.html">Visit website</a>
```

We want to be able to identify people who've clicked on ads e.g. in a marketing email as having come to the site having clicked on a link in that particular marketing email. To do that, we modify the link in the marketing email with query parameters, like so:

```html
<a href="http://mysite.com/myproduct.html?utm_source=newsletter-october&utm_medium=email&utm_campaign=cn0201">Visit website</a>
```

For the prospective customer clicking on the link, adding the query parameters does not change the user experience. (The user is still directed to the webpage at http://mysite.com/myproduct.html.) But Snowplow then has access to the fields given in the query string, and uses them to identify this user as originating from the October Newsletter, an email marketing campaign with campaign id = cn0201.

<a name="anatomy-of-the-query-parameters" />
#### 3.5.2 Anatomy of the query parameters

Snowplow uses the same query parameters used by Google Analytics. Because of this, Snowplow users who are also using GA do not need to do any additional work to make their campaigns trackable in Snowplow as well as GA. Those parameters are:

| **Parameter**        | **Name**              | **Description**                                     |
|:--------------------:|:---------------------:|:---------------------------------------------------:|
| `utm_source`         | Campaign source       | Identify the advertiser driving traffic to your site e.g. Google, Facebook, autumn-newsletter etc.  |
| `utm_medium`         | Campaign medium       | The advertising / marketing medium e.g. cpc, banner, email newsletter, in-app ad, cpa |
| `utm_campaign`       | Campaign id           | A unique campaign id. This can be a descriptive name or a number / string that is then looked up against a campaign table as part of the analysis |
| `utm_term  `         | Campaign term(s)      | Used for search marketing in particular, this field is used to identify the search terms that triggered the ad being displayed in the search results. |
| `utm_content`        | Campaign content      | Used either to differentiate similar content or two links in the same ad. (So that it is possible to identify which is generating more traffic.) |

The parameters are descibed in the [Google Analytics help page] [gahelppage]. Google also provides a [urlbuilder] [gaurlbuilder] which can be used to construct the URL incl. query parameters to use in your campaigns.


[Back to top](#top)  
[Back to JavaScript technical documentation contents][contents]

<a name="adimps" />
### 3.6 Ad impression tracking

Snowplow tracking code can be included in ad tags in order to track impressions. This is used by e.g. ad networks to identify which sites and web pages users visit across a network, so that they can be segmented, for example.

Impression tracking is accomplished using the `trackImpression` method.

<a name="trackImpression" />
#### 3.6.1 `trackImpression`

**Note**: Although this feature is implemented in the JavaScript Tracker, it is **not** currently supported by the ETL, storage and analytics stages of the Snowplow data pipeline. As a result, if you implement this feature, you will successfully track impression data to your collector logs, but this data will not be extracted and loaded into e.g. Redshift for analysis. 

Adding support for this event type to the ETL, storage and analytics stages of the data pipeline is on the Snowplow roadmap. Until it is delivered, we recommend using the [custom structured event tracking] [custom-structured-events] to track impressions.

The method takes four parameters:

| **Name**       | **Required?** | **Description**                                                                              |
|---------------:|:--------------|:---------------------------------------------------------------------------------------------|
|     `BannerID` | Yes           | Adserver identifier for the ad banner (creative) being displayed                             |
|   `CampaignID` | No            | Adserver identifier for the ad campaign which the banner belongs to                          |
| `AdvertiserID` | No            | Adserver identifier for the advertiser which the campaign belongs to                         |
|       `UserID` | No            | Adserver identifier for the web user. Not to be confused with Snowplow's own user identifier |

You will want to set these arguments programmatically, across all of your ad zones/slots - for guidelines on how to achieve this with the [OpenX adserver] [openx], please see the following sub-sections.

#### 3.6.2 Example: implementing impression tracking with Snowplow and OpenX

Most ad servers enable you to append custom code to your ad tags. Here's what the zone append functionality looks like in the OpenX adserver (OnRamp edition): 

![zoneappend] [zoneappend]

You will need to populate the ad zone append field with Snowplow tags for **every ad zone/unit** which you use to serve ads across your site or network. Read on for the Snowplow HTML code to use for OpenX. 

#### OpenX: Snowplow impression tracking using magic macros

Because OpenX has a feature called [magic macros] [magicmacros], it is relatively straightforward to pass the banner, campaign and user ID arguments into the call to `trackImpression()` (advertiser ID is not available through magic macros).

The full HTML code to append, using asynchronous Snowplow invocation, looks like this:

```html
<!-- Snowplow starts plowing -->
<script type="text/javascript">
window._snaq = window._snaq || [];

window._snaq.push(['setCollectorCf', 'patldfvsg0d8w']); // Update to your CloudFront distribution subdomain
window._snaq.push(['trackImpression', '{bannerid}', '{campaignid}', '', '{OAID}']); // OpenX magic macros. Leave this line as-is

(function() {
var sp = document.createElement('script'); sp.type = 'text/javascript'; sp.async = true; sp.defer = true;
sp.src = ('https:' == document.location.protocol ? 'https' : 'http') + '://d107t3sdgumbla.cloudfront.net/sp.js';
var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(sp, s);
})();
 </script>
<!-- Snowplow stops plowing -->
```

Once you have appended this code to all of your active ad zones, Snowplow should be collecting all of your ad impression data.

[Back to top](#top)

<a name="custom-structured-events" />
### 3.7 Tracking custom structured events

There are likely to be a large number of AJAX events that can occur on your site, for which a specific tracking method is part of Snowplow. Examples include:

* Playing a video
* Adding an item to basket
* Submitting a lead form

Our philosophy in creating Snowplow is that users should capture "every" consumer interaction and work out later how to use this data. This is different from traditional web analytics and business intelligence, that argues that you should first work out what you need, and only then start capturing the data. 

As part of a Snowplow implementation, therefore, we recommend that you identify every type of AJAX interaction that a user might have with your site: each one of these is an event that will not be captured as part of the standard page view tracking. All of them are candidates to track using `trackStructEvent`, if none of the other event-specific methods outlined above are appropriate.

#### 3.7.1 `trackStructEvent`

There are five parameters can be associated with each structured event. Of them, only the first two are required:

| **Name**    | **Required?** | **Description**                                                                          |
|------------:|:--------------|:-----------------------------------------------------------------------------------------|
|  `Category` | Yes           | The name you supply for the group of objects you want to track e.g. 'media', 'ecomm'     |
|    `Action` | Yes           | A string which defines the type of user interaction for the web object e.g. 'play-video', 'add-to-basket' |
|     `Label` | No            | An optional string which identifies the specific object being actioned e.g. ID of the video being played, or the SKU or the product added-to-basket |
|  `Property` | No            | An optional string describing the object or the action performed on it. This might be the quantity of an item added to basket |
|     `Value` | No            | An optional float to quantify or further describe the user action. This might be the price of an item added-to-basket, or the starting time of the video where play was just pressed |

The async specification for the `trackStructEvent` method is:

```javascript
snowplow_name_here('trackStructEvent', 'category','action','label','property','value');
```

An example of tracking a user listening to a music mix:

```javascript
snowplow_name_here('trackStructEvent', 'Mixes', 'Play', 'MrC/fabric-0503-mix', '', '0.0');
```

Note that in the above example no value is set for the `event property`.

[Back to top](#top)  
[Back to JavaScript technical documentation contents][contents]

<a name="custom-unstructured-events" />
### 3.8 Tracking custom unstructured events

**Note**: this feature is implemented in the JavaScript Tracker, but it is **not** currently supported in the ETL, storage or analytics stages in the Snowplow data pipeline. As a result, if you use this feature, you will log unstructured events to your collector logs, but these will not be parsed and loaded into e.g. Redshift to analyse. (Adding this capability is on the roadmap.) Until the ETL and storage steps are upgraded to support unstructured events, anyone using them will have to write their own custom jobs to extract the events from the collector logs and analyse them.

There are certain events that you may want to track on your website or application, which are not directly supported by Snowplow, and are not suitable for being captured using the [structured event tracking] (#custom-structured-events). There are two use cases:

1. Where you want to track event types which are proprietary/specific to your business, and the type of data associated with each visit does not fit into the [structured event tracking] (#custom-structured-events), either because you want to capture data of a specific type (e.g. geographical coordinates or arrays), or you want to capture more data than the five structured event fields offer allow.
2. Where you want to track events which have unpredictable or frequently changing properties, so that it is not possible to specify the fields in advance.

When you track a custom unstructured event, you track the event name and a set of associated "properties" enclosed in a JSON envelope. Because you can add as many name/value properties to the JSON as you'd like, and a wide range of data types are supported (see below), this is a very flexible way of tracking events.  A custom unstructured event conforms to the primary format of events captured by analytics tools like [Mixpanel] [mixpanel], [Kissmetrics] [kissmetrics] and [Keen.io] [keen.io].

<a name="trackUnstructEvent" />
#### 3.8.1 `trackUnstructEvent`

To track an unstructured event, you make use the `trackUnstructEvent` method:

```javascript
snowplow_name_here('trackUnstructEvent', <<EVENT NAME>>, <<EVENT PROPERTIES JSON>>);
```

For example:

```javascript
snowplow_name_here('trackUnstructEvent', 'Viewed Product',
                {
                    product_id: 'ASO01043',
                    category: 'Dresses',
                    brand: 'ACME',
                    returning: true,
                    price: 49.95,
                    sizes: ['xs', 's', 'l', 'xl', 'xxl'],
                    available_since$dt: new Date(2013,3,7)
                }
            );
```

Notes regarding the `properties` JSON:

* The `properties` JSON consists of a set of individual name: value pairs
* The structure **must** be flat: properties **cannot** be nested

##### 3.8.1.1 Supported datatypes

Snowplow unstructured events support a relatively rich set of datatypes. Because these datatypes do not always map directly onto JavaScript datatypes, we have introduced some "type suffixes" for the JavaScript property names, so that Snowplow knows what Snowplow data types the JavaScript data types map onto:

| Snowplow datatype | Description                  | JavaScript datatype  | Type suffix(es)      | Supports array? |
|:------------------|:-----------------------------|:---------------------|:---------------------|:----------------|
| Null              | Absence of a value           | Null                 | -                    | No              |
| String            | String of characters         | String               | -                    | Yes             |
| Boolean           | True or false                | Boolean              | -                    | Yes             |
| Integer           | Number without decimal       | Number               | `$int`               | Yes             |
| Floating point    | Number with decimal          | Number               | `$flt`               | Yes             |
| Geo-coordinates   | Longitude and latitude       | \[Number, Number\]   | `$geo`               | Yes             |
| Date              | Date and time (ms precision) | Number               | `$dt`, `$ts`, `$tms` | Yes             |
| Array             | Array of values              | \[x, y, z\]          | -                    | -               |

Let's go through each of these in turn, providing some examples as we go:

###### 3.8.1.1.1 Null

Tracking a Null value for a given field is straightforward:

```javascript
{
    returns_id: null
}
```

###### 3.8.1.1.2 String

Tracking a String is easy:

```javascript
{
    product_id: 'ASO01043' // Or "ASO01043"
}
```

###### 3.8.1.1.3 Boolean

Tracking a Boolean is also straightforward:

```javascript
{
    trial: true
}
```

###### 3.8.1.1.4 Integer

To track an Integer, use a JavaScript Number but add a type suffix like so:

```javascript
{
    in_stock$int: 23
}
```

**Warning:** if you do not add the `$int` type suffix, Snowplow will assume you are tracking a Floating point number.

###### 3.8.1.1.5 Floating point

To track a Floating point number, use a JavaScript Number; adding a type suffix is optional:

```javascript
{
    price$flt: 4.99, 
    sales_tax: 49.99 // Same as $sales_tax:$flt
}
```

###### 3.8.1.1.5 Geo-coordinates

Tracking a pair of Geographic coordinates is done like so:

```javascript
{
    check_in$geo: [40.11041, -88.21337] // Lat, long
}
```

Please note that the datatype takes the format **latitude** followed by **longitude**. That is the same order used by services such as Google Maps.

**Warning:** if you do not add the `$geo` type suffix, then the value will be incorrectly interpreted by Snowplow as an Array of Floating points.

###### 3.8.1.1.6 Date

Snowplow Dates include the date _and_ the time, with milliseconds precision. There are three type suffixes supported for tracking a Date:

* `$dt` - the Number of days since the epoch
* `$ts` - the Number of seconds since the epoch
* `$tms` - the Number of milliseconds since the epoch. This is the default for JavaScript Dates if no type suffix supplied

You can track a date by adding either a JavaScript Number _or_ JavaScript Date to your `properties` object. The following are all valid dates:

```javascript
{
    birthday$dt: new Date(1980,11,10), // Sent to Snowplow as birthday$dt: 3996
    birthday2$dt: 3996, // ^ Same as above
    
    registered$ts: new Date(2013,05,13,14,20,10), // Sent to Snowplow as registered$ts: 1371129610
    registered2$ts: 1371129610, // Same as above
    
    last_action$tms: 1368454114215, // Accurate to milliseconds
    last_action2: new Date() // Sent to Snowplow as last_action2$tms: 1368454114215
}
```

Note that the type prefix only indicates how the JavaScript Number sent to Snowplow is interpreted - all Snowplow Dates are stored to milliseconds precision (whether or not they include that level of precision).

**Two warnings:**

1. If you specify a JavaScript Number but do not add a valid Date suffix (`$dt`, `$ts` or `$tms`), then the value will be incorrectly interpreted by Snowplow as a Number, not a Date
2. If you specify a JavaScript Number but add the wrong Date suffix, then the Date will be incorrectly interpreted by Snowplow, for example:

```javascript
{
    last_ping$dt: 1371129610 // Should have been $ts. Snowplow will interpret this as the year 3756521449
}
```

###### 3.8.1.1.7 Arrays

You can track an Array of values of any data type other than Null.

Arrays must be homogeneous - in other words, all values within the Array must be of the same datatype. This means that the following is **not** allowed:

```javascript
{
    sizes: ['xs', 28, 'l', 38, 'xxl'] // NOT allowed
}
```

By contrast, the following are all allowed:

```javascript
{
    sizes: ['xs', 's', 'l', 'xl', 'xxl'],
    session_starts$ts: [1371129610, 1064329730, 1341127611],
    check_ins$geo: [[-88.21337, 40.11041], [-78.81557, 30.22047]]
}
```

[Back to top](#top)  
[Back to JavaScript technical documentation contents][contents]

<a name="link-click-tracking" />
### 3.9 Link click tracking

This feature is on the roadmap: it has not been developed yet.

<a name="enableLinkTracking" />
#### 3.9.1 `enableLinkTracking`

This feature is on the roadmap: it has not been developed yet.

[Back to top](#top)  
[Back to JavaScript technical documentation contents][contents]

<a name="custom-contexts" />
### 3.10 Custom contexts

Custom contexts can be used to augment any standard Snowplow event type, including unstructured events, with additional data.

Custom contexts can be added as an extra argument to any of Snowplow's `track..()` methods and to `addItem` and `addTrans`. 

If set, the context argument must be a JSON of the form:

```javascript
{
  "context1_name": {
    ...
  },
  "context2_name": {
    ...
  }

}
```

where each inner context JSON follows the same rules as the event properties JSON argument for an [unstructured event](#trackUnstructEvent). In particular, they must have a flat structure, so this movie poster context is allowed:

```javascript
{
  "movie_poster": {
    "movie_name": "Solaris",
    "poster_country": "JP",
    "poster_year": new Date(1978, 1, 1)
  }
}
```

But this one isn't:

```javascript
{
  "movie_poster": {
    "movie_name": "Solaris",
    "nested_poster_data": {
      "poster_country": "JP",
      "poster_year": new Date(1978, 1, 1)
    }
  }
}
```

How to track a pageview event with this custom context:

```javascript
snowplow_name_here(['trackPageView', '', { 
  "movie_poster": {
    "movie_name": "Solaris",
    "poster_country": "JP",
    "poster_year": new Date(1978, 1, 1)
  }
}]);
```

In this case an empty string has been provided to the optional `customTitle` argument in order to reach the `context` argument.

For more information on custom contexts, see [this][contexts] blog post.

[Back to top](#top)  
[Back to JavaScript technical documentation contents][contents]

[contents]: Javascript-Tracker
[specific-events-v1]: https://github.com/snowplow/snowplow/wiki/2-Specific-event-tracking-with-the-Javascript-tracker-v1





[openx]: http://www.openx.com/publisher/enterprise-ad-server
[zoneappend]: /snowplow/snowplow/wiki/setup-guide/images/03a_zone_prepend_openx.png
[magicmacros]: http://www.openx.com/docs/whitepapers/magic-macros
[dmp]: http://www.adopsinsider.com/online-ad-measurement-tracking/data-management-platforms/what-are-data-management-platforms/ 
[contactus]: mailto:snowplow-ads@keplarllp.com
[mixpanel]: https://mixpanel.com/
[kissmetrics]: https://www.kissmetrics.com/
[keen.io]: https://keen.io/
[contexts]: http://snowplowanalytics.com/blog/2014/01/27/snowplow-custom-contexts-guide/
[specific-events-v1]: https://github.com/snowplow/snowplow/wiki/2-Specific-event-tracking-with-the-Javascript-tracker-v1
