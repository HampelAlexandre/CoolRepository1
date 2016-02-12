[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) > [**Step 1: setup a Collector**](Setting-up-a-Collector) > [**Clojure collector setup**](setting-up-the-clojure-collector) > [[Additional configuration options]]

There are a environment configuration parameters that you may want to consider tailoring to your specific needs. All of them can be accessed via the **Configuration** page of the Elastic Beanstalk UI.

<a name="3bi" ></a>

#### 1. Setting the environment to develop or production

You can set an environment mode in the collector to "development" or "production". Click on the cogwheel-like icon next to **Software Configuration** box. 

[[/setup-guide/images/clojure-collector-setup-guide/22.jpg]]

Scroll down to *Environment Properties* section. Enter the property name either **PARAM1** or **SP_ENV** and your desired environment value in for *Property Value* and then click on **Apply** button:

[[/setup-guide/images/clojure-collector-setup-guide/23.jpg]]

Setting the environment to 'production' will hide the status of the collector on `http://{{COLLECTOR URL}}/status`. It is set to 'development' by default.

<a name="3bii" ></a>

#### 2. Setting the P3P policy header

This can be entered directly into the same dialogue box as the environment name (see [3b i](#3bi) above), but the parameter  should be named either **PARAM2** or **SP_P3P** rather than **PARAM1** or **SP_ENV**.

If it is not set, the P3P policy header defaults to:

	policyref="/w3c/p3p.xml", CP="NOI DSP COR NID PSA OUR IND COM NAV STA"

<a name="3biii" ></a>

#### 3. Setting the domain name

The domain name can be entered directly into the same dialogue box as the [environment name](#3bi) and [P3P policy header](#3bii) by giving the *Property Name* either **PARAM3** or **SP_DOMAIN**.

Setting the domain name can be useful if you want to make the cookie accessible to other applications on your domain. If we, for example, set up the collector on `collector.snplow.com` and did not set a domain name, the cookie will default to this domain. However, if we set it to `.snplow.com` in the *Property Value* against **SP_DOMAIN** property, that cookie will be accessible to other applications running on `*.snplow.com`.

Please, refer to [RFC 6265](https://tools.ietf.org/html/rfc6265#section-5.1.3) for the domain matching rules.

<a name="3biv" ></a>

#### 4. Setting the cookie duration

This can be entered into the same dialogue box as the [environment name](#3bi), [P3P policy header](#3bii) and [domain name](#3biii), by entering the value for the *Property Name* either **PARAM4** or **SP_DURATION**. The value entered should be an integer representing cookie duration measured in days.

If no value is provided, cookies set default to expiring after one year (i.e. 365 days).

If you set the value to `0`, the cookie will not be set at all.

Below is the summary of what parameter names are to be used and their purpose.

Parameter Name | Alternative Name | Parameter Use
:---:|:---:|:---
SP_ENV | PARAM1 | Setting the environment type of your application (ex. development)
SP_P3P | PARAM2 | Setting the P3P policy header
SP_DOMAIN | PARAM3 | Setting the cookie domain name matching
SP_DURATION | PARAM4 | Setting the cookie duration

#### 5. Auto scaling

Elastic Beanstalk can scale up the number of web servers running the collector to handle spikes in traffic.

Basic settings (minimum and maximum numbers of servers) can be set in the configuration dialogue box, go to **Configuration** and then **Scaling**. 

Note if initially you set your Elastic Beanstalk to run in the single instance mode you would have to switch it to auto scaling first. Once changes have been applied, you can set the number range of instances in your cluster.

[[/setup-guide/images/clojure-collector-setup-guide/24.jpg]]

You can tell Amazon in what circumstances to launch new instances by setting 'triggers'. More details on tuning Elastic Beanstalk can be found [here](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.managing.as.html).

## All done?

You have setup the Clojure collector! You are now ready to [setup a tracker][tracker-setup].

Return to the [setup guide][setup-guide].

[setup-guide]: Setting-up-Snowplow
[tracker-setup]: Setting-up-Snowplow#wiki-step2
