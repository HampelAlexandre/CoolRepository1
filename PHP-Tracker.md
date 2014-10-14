<a name="top" />

## Work in Progress
See current release wiki [here][version-0.1.0].

## Contents

- 1. [Overview](#overview)
- 2. [Initialize](#init)
  - 2.1 [Create a Tracker](#create-tracker)
    - 2.1.1 [`emitters`](#emitters)
    - 2.1.2 [`subject`](#subject)
    - 2.1.3 [`namespace`](#namespace)
    - 2.1.4 [`app_id`](#app-id)
    - 2.1.5 [`encode_base64`](#base64)
- 3. [Subjects](#subject-class)
  - 3.1 [`setPlatform`](#set-platform)
  - 3.2 [`setUserId`](#set-user-id)
  - 3.3 [`setScreenResolution`](#set-screen-res)
  - 3.4 [`setViewport`](#set-viewport)
  - 3.5 [`setColorDepth`](#set-color-depth)
  - 3.6 [`setTimezone`](#set-timezone)
  - 3.7 [`setLanguage`](#set-lang)
  - 3.8 [`setIpAddress`](#set-ip-address)
  - 3.9 [`setUseragent`](#set-user-agent)
- 4. [Emitters](#emitter-class)
  - 4.1 [Sync](#sync-emitter)
  - 4.2 [Socket](#socket-emitter)
  - 4.3 [Curl](#curl-emitter)
    - 4.3.1 [Defaults](#curl-emitter-defaults)
  - 4.4 [File](#file-emitter)
  - 4.5 [Debug Mode](#emitter-debug)
    - 4.5.1 [Non-logged Information](#non-logged-info)
- 5. [Tracking an Event](#track-an-event)
  - 5.1 [Optional Tracking Arguments](#tracking-options)
    - 5.1.1 [Custom Context](#custom-context)
    - 5.1.2 [Timestamp](#timestamp)
  - 5.2 [Event Tracking Methods](#tracking-methods)
    - 5.2.1 [`trackPageView`](#page-view)
    - 5.2.2 [`trackEcommerceTransaction`](#ecommerce-transaction)
      - 5.2.2.1 [`trackEcommerceTransactionItem`](#ecommerce-item)
    - 5.2.3 [`trackScreenView`](#screen-view) 
    - 5.2.4 [`trackStructEvent`](#struct-event)  
    - 5.2.5 [`trackUnstructEvent`](#unstruct-event)
  - 5.3 [Extra Functions](#extra-functions)
    - 5.3.1 [`flushEmitters`](#flush-emitters)
    - 5.3.2 [`setNetworkUserId`](#set-network-user-id)

<a name="overview" />
## 1. Overview

The [Snowplow PHP Tracker](https://github.com/snowplow/snowplow-php-tracker) allows you to track Snowplow events from your PHP apps and code.

There are three basic types of object you will create when using the Snowplow PHP Tracker: subjects, emitters, and trackers.

A subject represents a user whose events are tracked. A tracker constructs events and sends them to one or more emitters. Each emitter then sends the event to the endpoint you configure, a Snowplow Collector.

<a name="init" />
## 2. Initialize

To instantiate a new Tracker instance we need to make sure the Snowplow Tracker classes are available.

Include these class aliases in your project.

```PHP
use Snowplow\Tracker\Tracker;
use Snowplow\Tracker\Emitter;
use Snowplow\Tracker\Subject;
```

We can now create our Emitter, Subject and Tracker objects.

<a name="create-tracker" />
### 2.1 Creating a Tracker

The most basic Tracker instance will only require you to provide the type of emitter and the URI of the collector to which the Tracker will log events.

```PHP
$emitter = new Emitter("sync", array(
                "uri" => $collector_uri,
                "type" => NULL, # Defaults to POST
                "protocol" => NULL, # Defaults to HTTP
                "buffer" => NULL # Defaults to 50
            )
        );
$subject = new Subject();
$tracker = new Tracker($emitter, $subject);
```

Other Tracker arguments:

| **Argument Name** | **Description**                      | **Required?** | **Default**        |
|-------------:|:-------------------------------------|:--------------|:------------------------|
| `emitters`      | The emitter to which events are sent          | Yes         | `None`        |
| `subject`       | The user being tracked                        | Yes         | `Subject()`   |
| `namespace`     | The name of the tracker instance              | No          | `None`        |
| `app_id`        | The application ID                            | No          | `None`        |
| `encode_base64` | Whether to enable [base 64 encoding] [base64] | No          | `True`        |

Another example using all allowed arguments:

```PHP
$tracker = new Tracker($emitter, $subject, "cf", "cf29ea", "1");
```

The `encode_base64` argument is always a string:
- "1" for True
- "0" for False

The default setting is True.

<a name="emitters" />
#### 2.1.1 `emitters`

This can be either a single emitter or an array of emitters. The tracker will send events to all of these emitters, which will in turn send them on to a collector.

```PHP
$emitter1 = new Emitter("sync", array(
                "uri" => $collector_uri,
                "type" => NULL,
                "protocol" => NULL,
                "buffer" => NULL
            )
        );
$emitter2 = new Emitter("curl", array(
                "uri" => $collector_uri,
                "ssl" => NULL,
                "type" => "POST",
                "buffer" => NULL
            )
        );

$emitter_array = array($emitter1, $emitter2);

// Tracker Init
$subject = new Subject();
$tracker1 = ($emitter1, $subject); # Single Emitter
$tracker2 = ($emitter_array, $subject); # Array of Emitters
```

For more information go to [emitters](#emitter-class).

<a name="subject" />
#### 2.1.2 `subject`

The user which the Tracker will track. This will give your events user-specific data such as timezone and language. You change the subject of your tracker at any time by calling `updateSubject($new_subject_object)`.
All events sent from this Tracker will now have the new subject information appended.

For more information go to [subjects](#subject-class).

<a name="namespace" />
#### 2.1.3 `namespace`

If provided, the `namespace` argument will be attached to every event fired by the new tracker. This allows you to later identify which tracker fired which event if you have multiple trackers running.

<a name="app-id" />
#### 2.1.4 `app_id`

The `app_id` argument lets you set the application ID to any string.

<a name="base64" />
#### 2.1.5 `encode_base64`

By default, unstructured events and custom contexts are encoded into Base64 to ensure that no data is lost or corrupted. You can turn encoding on or off using the `encode_base64` argument.

<a name="subject-class" />
## 3. Subjects

For any additional information about your application's environment, current user and so on, which you want to send to Snowplow with each event we have the subject object.

To create a new subject:

```PHP
$subject = new Subject();
```

By default the subject has one pair of information in it already, platform ["plat" => "srv"].

The Subject class contains a variety of 'set' methods to attach extra data to your event.

* [`setPlatform`](#set-platform)
* [`setUserId`](#set-user-id)
* [`setScreenRes`](#set-screen-res)
* [`setViewport`](#set-viewport)
* [`setColorDepth`](#set-color-depth)
* [`setTimezone`](#set-timezone)
* [`setLang`](#set-lang)

These set methods can be called either directly onto a subject object:

```PHP
$subject = new Subject();
$subject->setPlatform("tv");
```

Or they can be called through the tracker object:

```PHP
$tracker->subject->setPlatform("tv");
```

<a name="set-platform" />
### 3.1 `setPlatform`

The default platform is "srv". You can change the platform of the subject by calling:

```PHP
$subject->setPlatform($platform);
```

For example:

```PHP
$subject->setPlatform("tv") # Running on a Connected TV
```

For a full list of supported platforms, please see the [[Snowplow Tracker Protocol]].

<a name="set-user-id" />
### 3.2 `setUserId`

You can set the user ID to any string:

```PHP
$subject->setUserId($id);
```

Example:

```PHP
$subject->setUserId("jbeem");
```

<a name="set-screen-res" />
### 3.3 `setScreenResolution`

If your PHP code has access to the device's screen resolution, then you can pass this in to Snowplow too:

```PHP
$subject->setScreenResolution($width, $height);
```

Both numbers should be positive integers; note the order is width followed by height. Example:

```PHP
$subject->setScreenResolution(1366, 768);
```

<a name="set-viewport" />
### 3.4 `setViewport`

If your PHP code has access to the viewport dimensions, then you can pass this in to Snowplow too:

```PHP
$subject->setViewport($width, $height);
```

Both numbers should be positive integers; note the order is width followed by height. Example:

```PHP
$subject->setViewport(300, 200);
```

<a name="set-color-depth" />
### 3.5 `setColorDepth`

If your PHP code has access to the bit depth of the device's color palette for displaying images, then you can pass this in to Snowplow too:

```PHP
$subject->setColorDepth($depth);
```

The number should be a positive integer, in bits per pixel. Example:

```PHP
$subject->setColorDepth(32);
```

<a name="set-timezone" />
### 3.6 `setTimezone`

This method lets you pass a user's timezone in to Snowplow:

```PHP
$subject->setTimezone($timezone);
```

The timezone should be a string:

```PHP
$subject->setTimezone("Europe/London");
```

<a name="set-lang" />
### 3.7 `setLanguage`

This method lets you pass a user's language in to Snowplow:

```PHP
$subject->setLanguage($language);
```

The language should be a string:

```PHP
$subject->setLanguage('en');
```

<a name="set-ip-address" />
### 3.8 `setIpAddress`

This method lets you pass a user's IP Address in to Snowplow:

```PHP
$subject->setIpAddress($ip);
```

The IP Address should be a string:

```PHP
$subject->setIpAddress('127.0.0.1');
```

<a name="set-user-agent" />
### 3.9 `setUseragent`

This method lets you pass a useragent in to Snowplow:

```PHP
$subject->setUseragent($useragent);
```

The useragent should be a string:

```PHP
$subject->setUseragent('Agent Smith');
```

<a name="emitter-class" />
## 4. Emitters

We now support four different emitters; sync, socket, curl and an out of band file emitter.
The most basic emitter only requires you to specify the type of emitter to be used and the collectors URI as parameters.

All emitters support both <b>GET</b> and <b>POST</b> collectors.  However for the sake of speed we recommend using <b>POST</b>.

Constructor:
```PHP
public function __construct($emitter_type, $emitter_options, $debug = false)
```

Arguments:

| **Argument** | **Description**                     | **Required?** | **Validation**          |
|-------------:|:------------------------------------|:--------------|:------------------------|
| `$emitter_type`    | Type of Emitter to use        | Yes           | Non-empty string  |
| `$emitter_options` | Emitter Options and Settings  | Yes           | Array             |
| `$debug`           | Whether or not to log errors  | No            | Boolean           |

<a name="sync-emitter" />
### 4.1 Sync

The Sync Emitter is a very basic synchronous emitter which supports both GET and POST Request types.

By default this emitter uses the Request type POST, HTTP and a buffer size of 50.  GET defaults to a buffer size of 1.

Example Emitter creation:
```PHP
$emitter = new Emitter("sync", array(
                "uri" => $collector_uri,
                "type" => "POST",
                "protocol" => NULL, # Defaults to HTTP
                "buffer" => NULL # Defaults to 50
            )
        );
```

Whilst you can force the buffer size to be greater than 1 for a GET Request; it will not yield any performance changes as we can still only send 1 event at a time.

Constructor:
```PHP
public function __construct($uri, $protocol = NULL, $type = NULL, $debug = NULL)
```

Arguments:

| **Argument** | **Description**                     | **Required?** | **Validation**          |
|-------------:|:------------------------------------|:--------------|:------------------------|
| `$uri`          | Collector URI                          | Yes           | Non-empty string  |
| `$protocol`     | Collector Protocol (HTTP or HTTPS)     | No            | String            |
| `$type`         | Request Type (POST or GET)             | No            | String            |
| `$buffer`       | Amount of events to store before flush | No            | Int               |
| `$debug`        | Whether or not to log errors           | No            | Boolean           |

<a name="socket-emitter" />
### 4.2 Socket

The Socket Emitter allows for much faster transmission of Requests to the collector by allowing us to write data directly to the HTTP socket.  However this solution is still in essence a synchronous process and will block the execution of the main script.

Example Emitter creation:
```PHP
$emitter = new Emitter("socket", array(
                "uri" => $collector_uri,
                "ssl" => NULL, # Defaults to False
                "type" => "POST",
                "timeout" => NULL, # Defaults to 30 seconds
                "buffer" => NULL # Defaults to 50
            )
        );
```

Whilst you can force the buffer size to be greater than 1 for a GET Request; it will not yield any performance changes as we can still only send 1 event at a time.

Constructor:
```PHP
public function __construct($uri, $ssl = NULL, $timeout = NULL, $debug = NULL)
```

Arguments:

| **Argument** | **Description**                     | **Required?** | **Validation**          |
|-------------:|:------------------------------------|:--------------|:------------------------|
| `$uri`          | Collector URI                          | Yes           | Non-empty string  |
| `$ssl`          | Whether to use SSL encryption          | No            | Boolean           |
| `$type`         | Request Type (POST or GET)             | No            | String            |
| `$timeout`      | Socket Timeout Limit                   | No            | Int or Float      |
| `$debug`        | Whether or not to log errors           | No            | Boolean           |

<a name="curl-emitter" />
### 4.3 Curl

The Curl Emitter allows us to have the closest thing to native asynchronous Requests in PHP.  The curl emitter uses the `curl_multi_init` resource which allows us to send any amount of Requests asynchronously. This garners quite a performance gain over the sync and socket emitters as we can now send more than one Request at a time.

On top of this we are also using a modified version of this **[Rolling Curl library][rolling-curl]** for the actual sending of the curl requests.  This allows for a more efficient implementation of asynchronous curl requests as we can now not only have multiple requests sending at the same time, but as soon as one is done a new request is started.

**IMPORTANT**

As the curl emitter has a secondary buffer within it; we need to ensure that we force send the emitter for cases where we have not reached the threshold.  More information can be found [here](#flush-emitters).

```PHP
$tracker->flushEmitters(true); # This will force flush all emitters associated with the Tracker instance.
```

Example Emitter creation:
```PHP
$emitter = new Emitter("curl", array(
                "uri" => $collector_uri,
                "ssl" => NULL, # Defaults to False
                "type" => "POST",
                "buffer" => NULL # Defaults to 50
            )
        );
```

Whilst you can force the buffer size to be greater than 1 for a GET Request; it will not yield any performance changes as we can still only send 1 event at a time.

Constructor:
```PHP
public function __construct($uri, $ssl = NULL, $debug = NULL)
```

Arguments:

| **Argument** | **Description**                     | **Required?** | **Validation**          |
|-------------:|:------------------------------------|:--------------|:------------------------|
| `$uri`          | Collector URI                          | Yes           | Non-empty string  |
| `$ssl`          | Whether to use SSL encryption          | No            | Boolean           |
| `$type`         | Request Type (POST or GET)             | No            | String            |
| `$debug`        | Whether or not to log errors           | No            | Boolean           |

<a name="curl-emitter-defaults">
#### 4.3.1 Curl Default Settings

The internal emitter default settings are as follows:

- Rolling Window (Amount of concurrent requests)
  - POST: 10
  - GET: 30
- Curl Buffer (Amount of times we need to hit the emitters buffer size)
  - POST: 50
  - GET: 250

These settings are currently not editable from the constructor; however the values are stored at the top of the `CurlEmitter.php` file.

<a name="file-emitter" />
### 4.4 File

The File Emitter is the only true non-blocking solution.  The File Emitter works via spawning workers which grab created event files from a local temporary folder.  The workers then load the events using the same asynchronous curl properties from the above emitter.

All of the worker processes are created as background processes so none of them will delay the execution of the main script.  Currently they are configured to look for files until they hit their `timeout` limit, at which point the process will kill itself.

If the worker for any reason fails to successfully send a curl request it will rename the file to `failed` and leave it in the `/temp/failed-logs/` folder.

Example Emitter creation:
```PHP
$emitter = new Emitter("file", array(
                "uri" => $collector_uri,
                "ssl" => NULL, # Defaults to False
                "type" => "POST",
                "workers" => NULL, # Defaults to 1
                "timeout" => NULL, # Defaults to 15
                "buffer" => NULL # Defaults to 250
            )
        );
```

The buffer for the file emitter works a bit differently to the other emitters in that here it is referring to the amount of events needed before an `events-random.log` is produced for a worker.  If you are anticipating it taking a long time to reach the buffer be aware that the worker will kill itself after 15 seconds by default.  Adjust your timeout accordingly.

Constructor:
```PHP
public function __construct($uri, $ssl = false, $workers = NULL, $timeout = NULL)
```

Arguments:

| **Argument** | **Description**                     | **Required?** | **Validation**          |
|-------------:|:------------------------------------|:--------------|:------------------------|
| `$uri`          | Collector URI                          | Yes           | Non-empty string  |
| `$ssl`          | Whether to use SSL encryption          | No            | Boolean           |
| `$type`         | Request Type (POST or GET)             | No            | String            |
| `$workers`      | Amount of background workers           | No            | Int               |
| `$timeout`      | Worker Timeout                         | No            | Int or Float      |

<a name="emitter-debug">
### 4.5 Emitter Debug Mode

Currently only Sync, Socket and Curl have any level of debugging available to them.  To enable debug mode for these emitters append a boolean to the end of the emitter construction argument like so:

```PHP
$emitter = new Emitter("sync", array(
                "uri" => $collector_uri,
                "type" => "POST",
                "protocol" => NULL,
                "buffer" => NULL
            ),
            true # Simply add the debug truth here! Will otherwise default to false.
        );
```

The debug mode will do two things.  Firstly it will create a new directory called `/debug/` in the root of the Trackers directory.  It will then create a log file with the following structure; `sync-events-log-random.log`.  Essentially the type of emitter and a randomized number to prevent it from being incorrectly overwritten.

Every time the events buffer is flushed we can now check and log if the sending was a success or if there was an error.  In the case of an error it records the entire event payload we were trying to send along with the error code.

Due to the nature of the File Emitter being a background process it is harder to access what is happening and log it accordingly.  For the moment it simply dumps the `events.log` file into a failed folder.  However as the File Emitter uses the same emitter structure as the curl emitter, if one works the other will.

<a name="non-logged-info" />
#### 4.5.1 Non-logged Information

Debug Mode if enabled will also have the emitter begin storing information internally.  It will store the HTTP Response code and the payload for every Request made by the emitter.

```PHP
array(
    "code" => 200,
    "data" => "{"e":"pv","url":"www.example.com","page":"example","refr":"www.referrer.com"}"
)
```

The `data` is stored as a json encoded string.  To locally test whether or not your emitters are successfully sending we can retrieve this information with the following commands:

```PHP
$emitters = $tracker->returnEmitters(); # Will store all of the emitters as an array.
$emitter = $emitters[0]; # Get the first emitter stored by the tracker
$real_emitter = $emitter->returnEmitter(); # Get the real emitter; aka sync or socket.
$results = $real_emitter->returnRequestResults();  # Return the stored results.

# Now that we have results we can work with...
print("Code: ".$results[0]["code"]);
print("Data: ".$results[0]["data"]);
```

As this does store quite a lot of information and can become quite heavy overtime we can end debug mode whenever we wish.

```PHP
$tracker->turnOfDebug();
```

This will stop all logging activity; both to the files and to the local arrays.  We can go one step further though and pass a `true` boolean to the function.  This will delete all of the associated debug log files aswell as emptying the local arrays.

```PHP
$tracker->turnOfDebug(true);
```

<a name="track-an-event" />
## 5. Tracking an Event

Snowplow has been built to enable you to track a wide range of events that occur when users interact with your websites and apps. We are constantly growing the range of functions available so as to capture that data more richly.

Tracking methods supported by the PHP Tracker:

| **Function**                                  | **Description**                                        |
|----------------------------------------------:|:-------------------------------------------------------|
| [`trackPageView`](#page-view)             | Track and record views of web pages.                   |
| [`trackEcommerceTransaction`](#ecommerce-transaction)   | Track an ecommerce transaction           |
| [`trackScreenView`](#screen-view)         | Track the user viewing a screen within the application |
| [`trackStructEvent`](#struct-event)       | Track a Snowplow custom structured event               |
| [`trackUnstructEvent`](#unstruct-event)   | Track a Snowplow custom unstructured event             |

<a name="tracking-options">
### 5.1 Optional Tracking Arguments

<a name="custom-context" />
#### 5.1.1 Custom Context

Custom contexts let you add additional information about any circumstances surrounding an event in the form of a PHP Array of name-value pairs. Each tracking method accepts an additional optional contexts parameter after all the parameters specific to that method:

```PHP
public function trackPageView($page_url, $page_title = NULL, $referrer = NULL, $context = NULL, $tstamp = NULL)
```

An example of a Context Array Structure:

```PHP
array(
    "schema" => "iglu:com.acme_company/movie_poster/jsonschema/2.1.1",
    "data" => array(
        "movie_name" => "Solaris", 
        "poster_country" => "JP"
    )
)
```

This is how to fire a page view event with the above custom context:

```PHP
$tracker->trackPageView(
    "http://www.films.com", 
    "Homepage", 
    NULL,
    array(
        "schema" => "iglu:com.acme_company/movie_poster/jsonschema/2.1.1",
        "data" => array(
            "movie_name" => "Solaris", 
            "poster_country" => "JP"
        )
    )
);
```

<a name="timestamp" />
#### 5.1.2 Timestamp

Each tracking method supports an optional timestamp as its final argument; this allows you to manually override the timestamp attached to this event. The timestamp should be in <b>milliseconds</b> since the Unix epoch.

If you do not pass this timestamp in as an argument, then the PHP Tracker will use the current time to be the timestamp for the event.

Here is an example tracking a structured event and supplying the optional timestamp argument. We can explicitly supply a `NULL` for the intervening arguments which are empty:

```PHP
$tracker->trackStructEvent("some cat", "save action", NULL, NULL, NULL, 1368725287000);
```

<a name="tracking-methods" />
### 5.2 Event Tracking Methods

<a name="page-view" />
#### 5.2.1 `trackPageView`

Track a user viewing a page within your app.

Function:
```PHP
public function trackPageView($page_url, $page_title = NULL, $referrer = NULL, $context = NULL, $tstamp = NULL)
```

Arguments:

| **Argument** | **Description**                     | **Required?** | **Validation**          |
|-------------:|:------------------------------------|:--------------|:------------------------|
| `$page_url`   | The URL of the page                 | Yes           | Non-empty string        |
| `$page_title` | The title of the page               | No            | String                  |
| `$referrer`   | The address which linked to the page| No            | String                  |
| `$context`    | Custom context for the event        | No            | Array                   |
| `$tstamp`     | When the pageview occurred          | No            | Positive integer        |

Example Usage:

```PHP
$tracker->trackPageView("www.example.com", NULL, NULL, NULL, 123123132132);
```

<a name="ecommerce-transaction" />
#### 5.2.2 `trackEcommerceTransaction`

Track an ecommerce transaction.

Function:

```PHP
public function trackEcommerceTransaction($order_id, $total_value, $currency = NULL, $affiliation = NULL,
                                          $tax_value = NULL, $shipping = NULL, $city = NULL, $state = NULL,
                                          $country = NULL, $items, $context = NULL, $tstamp = NULL)
```

Arguments:

| **Argument**     | **Description**                   | **Required?** | **Validation**    |
|-----------------:|:----------------------------------|:--------------|:------------------|
| `$order_id`    | ID of the eCommerce transaction      | Yes           | Non-empty string  |
| `$total_value` | Total transaction value              | Yes           | Int or Float      |
| `$currency`    | Transaction currency                 | No            | String            |
| `$affiliation` | Transaction affiliation              | No            | String            |
| `$tax_value`   | Transaction tax value                | No            | Int or Float      |
| `$shipping`    | Delivery cost charged                | No            | Int or Float      |
| `$city`        | Delivery address city                | No            | String            |
| `$state`       | Delivery address state               | No            | String            |
| `$country`     | Delivery address country             | No            | String            | 
| `$items`       | Items in the transaction             | Yes           | Array             |
| `$context`     | Custom context for the event         | No            | Array             |
| `$tstamp`      | When the transaction event occurred  | No            | Positive integer  |

Example Usage:

```PHP
$tracker->trackEcommerceTransaction(
    "test_order_id_1", 200, "GBP", "affiliation_1", "tax_value_1","shipping_1", "city_1", "state_1", "country_1",
    array(
        array("name" => "name_1","category" => "category_1",
            "price" => 100,"sku" => "sku_1","quantity" => 1),
        array("name" => "name_2","category" => "category_2",
            "price" => 100,"sku" => "sku_2","quantity" => 1)
    )
);
```

The above example contains an order with two order items.

<a name="ecommerce-item" />
##### 5.2.2.1 `trackEcommerceTransactionItem`

This is a private function that is called from within `trackEcommerceTransaction`.  It is important to note that for an item to be added successfully you need to include the following fields in the array; even if the value is `NULL`.

Arguments:

| **Argument**    | **Description**                     | **Required?** | **Validation**           |
|----------------:|:------------------------------------|:--------------|:-------------------------|
| `"sku"`         | Item SKU                            | Yes           | Non-empty string         |
| `"price"`       | Item price                          | Yes           | Int or Float             |
| `"quantity"`    | Item quantity                       | Yes           | Int                      |
| `"name"`        | Item name                           | No            | String                   |
| `"category"`    | Item category                       | No            | String                   |

Example Item:

```PHP
array(
    array("name" => NULL,
          "category" => NULL,
          "price" => 100,
          "sku" => "sku_1",
          "quantity" => 1)
)
```

If any of these fields are missing the item event will not be created.  However the order of these fields is not important.

<a name="screen-view" />
#### 5.2.3 `trackScreenView`

Track a user viewing a screen (or equivalent) within your app.

Function:

```PHP
public function trackScreenView($name = NULL, $id = NULL, $context = NULL, $tstamp = NULL)
```

Arguments:

| **Argument** | **Description**                     | **Required?** | **Validation**          |
|-------------:|:------------------------------------|:--------------|:------------------------|
| `$name`      | Human-readable name for this screen | No            | Non-empty string        |
| `$id`        | Unique identifier for this screen   | No            | String                  |
| `$context`   | Custom context for the event        | No            | Array                   |
| `$tstamp`    | When the screen was viewed          | No            | Positive integer        |

Although `$name` and `$id` are not individually required, at least one must be provided or the event will fail validation.

Example:

```PHP
$tracker->trackScreenView("HUD > Save Game", NULL, NULL, 1368725287000);
```

<a name="struct-event" />
#### 5.2.4 `trackStructEvent`

Track a custom event happening in your app which fits the Google Analytics-style structure of having up to five fields (with only the first two required).

Function:
```PHP
public function trackStructEvent($category, $action, $label = NULL, $property = NULL, $value = NULL, 
                                 $context = NULL, $tstamp = NULL)
```

Arguments:

| **Argument** | **Description**                                                  | **Required?** | **Validation**           |
|-------------:|:---------------------------------------------------------------  |:--------------|:-------------------------|
| `$category`   | The grouping of structured events which this `action` belongs to | Yes           | Non-empty string         |
| `$action`     | Defines the type of user interaction which this event involves   | Yes           | Non-empty string         |
| `$label`      | A string to provide additional dimensions to the event data      | No            | String                   |
| `$property`   | A string describing the object or the action performed on it     | No            | String                   |
| `$value`      | A value to provide numerical data about the event                | No            | Int or Float             |
| `$context`    | Custom context for the event                                     | No            | Array                      |
| `$tstamp`     | When the structured event occurred                               | No            | Positive integer         |

Example:

```PHP
$tracker->trackStructEvent("shop", "add-to-basket", NULL, "pcs", 2);
```

<a name="unstruct-event" />
#### 5.2.5 `trackUnstructEvent`

Track a custom event which consists of a name and an unstructured set of properties. This is useful when:

* You want to track event types which are proprietary/specific to your business (i.e. not already part of Snowplow), or
* You want to track events which have unpredictable or frequently changing properties

Function:

```PHP
public function trackUnstructEvent($event_json, $context = NULL, $tstamp = NULL)
```

Arguments:

| **Argument**   | **Description**                      | **Required?** | **Validation**          |
|---------------:|:-------------------------------------|:--------------|:------------------------|
| `$event_json`   | The properties of the event          | Yes          | Array                   |
| `$context`      | Custom context for the event         | No           | Array                   |
| `$tstamp`       | When the unstructured event occurred | No           | Positive integer        |

Example:

```PHP
$tracker->trackUnstructEvent(
    array(
        "schema" => "com.example_company/save-game/jsonschema/1.0.2",
        "data" => array(
            "save_id" => "4321",
            "level" => 23,
            "difficultyLevel" => "HARD",
            "dl_content" => true 
        )
    ),
    NULL,
    132184654684
);
```

The `$event_json` must be an array with two fields: `schema` and `data`. `data` is a flat array containing the properties of the unstructured event. `schema` identifies the JSON schema against which `data` should be validated.

<a name="extra-functions" />
### 5.3 Extra Tracker Functions

<a name="flush-emitters" />
#### 5.3.1 Tracker `flushEmitters`

The `flushEmitters` function can be called after you have successfully created a Tracker with the following function call:

```PHP
$tracker->flushEmitters();
```

This will tell the tracker to send any remaining events that are left in the buffer to the collector(s).  This is useful if you have set your buffer size too high and you actually have not sent any events.  Or if you just want to be sure that you are sending all of your events.

This function has a second form whereby we can `force' flush the events buffer regardless of if there are events or not.  This is useful only for the asynchronous [curl emitter](#curl-emitter) which has a secondary buffer; the curl buffer.  If the curl buffer limit is not reached no events will be sent.  Therefore this function must be passed to ensure that they are sent.

```PHP
$tracker->flushEmitters(true);
```

Regardless of how sure you are that all of your events have sent pass this command to ensure that everything gets sent!

<a name="set-network-user-id" />
#### 5.3.2 `setNetworkUserId`

On Tracker creation we can now also set a Network User ID.  This will be passed along with every event that is sent to the collectors.

The function expects a string to be passed.

```PHP
$tracker->setNetworkUserId("nuid");
```

[base64]: https://en.wikipedia.org/wiki/Base64
[rolling-curl]: https://github.com/joshfraser/rolling-curl
[version-0.1.0]: https://github.com/snowplow/snowplow/wiki/PHP-Tracker-v0.1.0