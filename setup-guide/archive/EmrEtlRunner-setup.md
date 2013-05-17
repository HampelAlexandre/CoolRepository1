[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](Snowplow setup guide) > [**ETL**](choosing-an-etl-module) > [**Hive ETL Setup**](hive-etl-setup)

## Table of Contents

1. [Introduction](#intro)
2. [Installation](#install)
 1. [Assumptions](#assumptions)
 2. [Dependencies](#dependencies)
 3. [Installation](#installation)
 4. [Configuration](#configuration)
3. [Usage](#usage)
 1. [Overview](#usage-overview)
 2. [Command-line options](#cli-options)
 3. [Running in each mode](#running)
4. [Scheduling](#scheduling)
 1. [Overview](#scheduling-overview)
 2. [cron](#cron)
 3. [Jenkins](#jenkins)
 4. [Windows Task Scheduler](#windows)

<a name="intro"/>
## Introduction

To make it easier to run Snowplow's Hadoop-based ETL (extract, transform, load) process on Amazon Elastic MapReduce, we have written the [Snowplow::EmrEtlRunner] [emr-etl-runner] Ruby application.

EmrEtlRunner is a command-line tool which runs and monitors our [Hadoop-based ETL process] [hadoop-etl] on EMR, as well as performing the required housekeeping tasks (such as archiving the raw event logs).

This guide will take you through installing and configuring EmrEtlRunner on your own server.

<a name="install"/>
## Installation

<a name="assumptions"/>
### Assumptions

This guide assumes that you have already integrated one or more
[Snowplow trackers][trackers] into your website or app, and have deployed a
[Snowplow collector] [collectors] which is logging raw Snowplow events to Amazon S3.

If you have not completed these steps yet, then please consult the [Getting started] [getting-started] page on the Snowplow Analytics website.

This guide also assumes that you have administrator access to a Unix-based server (e.g. Ubuntu, OS X, Fedora) on which you can install EmrEtlRunner and schedule a regular cronjob.

_In theory EmrEtlRunner can be deployed onto a Windows-based server, using the Windows Task Scheduler instead of cron, but this has not been tested or documented._

<a name="dependencies"/>
### Dependencies

#### Software

To install EmrEtlRunner, first make sure that your server has **all** of the following installed:

1. **Git** - see the [Git Installation Guide] [git-install]
2. **Ruby and RVM** - see our [Ruby and RVM setup guide](Ruby-and-RVM-setup)

#### EC2 key

You will also need an **EC2 key pair** setup in your Amazon EMR account.

For details on how to do this, please see the section "Configuring the client" in the
[[Setting up EMR command line tools]] wiki page. Make sure that you setup the EC2 key pair inside the region
in which you will be running your ETL jobs.

<a name="s3-buckets"/>
#### S3 buckets

EmrEtlRunner moves the Snowplow event data through four distinct buckets during
the ETL process. These buckets are as follows:

1. **In Bucket** - contains the raw Snowplow event logs to process
2. **Processing Bucket** - where EmrEtlRunner moves the raw event
   logs for processing
3. **Out Bucket** - where EmrEtlRunner stores the processed
   Snowplow-format event files
4. **Archive Bucket** - where EmrEtlRunner moves the raw Snowplow
   event logs after successful processing

You will have already setup the In Bucket when you were configuring your Snowplow
collector - but the other three buckets do not exist yet.

So, create the other three buckets in the same AWS region as your In Bucket. Take
a note of the buckets' names as you will need to use these buckets shortly.

Done? Right, now we can install EmrEtlRunner.

<a name="installation"/>
### Installation

First, checkout the Snowplow repository and navigate to the EmrEtlRunner root:

    $ git clone git://github.com/snowplow/snowplow.git
    $ cd snowplow/3-enrich/emr-etl-runner
    
If RVM asks you if you want to trust the `.rvmrc` file, type `y`:

    ==============================================================================
    = NOTICE                                                                     =
    ==============================================================================
    = RVM has encountered a new or modified .rvmrc file in the current directory =
    = This is a shell script and therefore may contain any shell commands.       =
    =                                                                            =
    = Examine the contents of this file carefully to be sure the contents are    =
    = safe before trusting it! ( Choose v[iew] below to view the contents )      =
    ==============================================================================
    Do you wish to trust this .rvmrc file? (/home/admin/apps/snowplow/3-enrich/emr-etl-runner/.rvmrc)
    y[es], n[o], v[iew], c[ancel]> y 
    Using /home/admin/.rvm/gems/ruby-1.9.3-p374

Next you are ready to install the application on your system:

    $ bundle install --deployment

Check it worked okay:

    $ bundle exec bin/snowplow-emr-etl-runner --version
    snowplow-emr-etl-runner 0.0.8

If you have any problems installing, please double-check that you have successfully 
completed our [Ruby and RVM setup guide](Ruby-and-RVM-setup).

<a name="configuration"/>
### Configuration

EmrEtlRunner requires a YAML format configuration file to run. There is a configuration
file template available in the Snowplow GitHub repository at 
[`/3-enrich/emr-etl-runner/config/config.yml`] [config-yml]. The template looks like this:

```yaml
:aws:
  :access_key_id: ADD HERE
  :secret_access_key: ADD HERE
:s3:
  :region: ADD HERE
  :buckets:
    # Update assets if you want to host the serde and HiveQL yourself
    :assets: s3://snowplow-emr-assets
    :log: ADD HERE
    :in: ADD HERE
    :processing: ADD HERE
    :out: ADD HERE WITH SUB-FOLDER
    :archive: ADD HERE
:emr:
  # Can bump the below as EMR upgrades Hadoop
  :hadoop_version: 1.0.3
  :placement: ADD HERE
  :ec2_key_name: ADD HERE
  # Adjust your Hive cluster below
  :jobflow:
    :instance_count: 2
    :master_instance_type: m1.small
    :slave_instance_type: m1.small
:etl:
  :collector_format: cloudfront # No other formats supported yet
  :continue_on_unexpected_error: false # You can switch to 'true' if you really don't want the serde throwing exceptions
  :storage_format: non-hive # Or switch to 'hive' if you're only using Hive for analysis
# Can bump the below as Snowplow releases new versions
:snowplow:
  :serde_version: 0.5.3
  :hive_hiveql_version: 0.5.4
  :non_hive_hiveql_version: 0.0.5
```

To take each section in turn:

#### aws

The `aws` variables should be self-explanatory - enter your AWS access
key and secret here.

#### s3

The `region` variable should hold the AWS region in which your four data
buckets (In Bucket, Processing Bucket etc) are located, e.g. "us-east-1"
or "eu-west-1".

Within the `s3` section, the `buckets` variables are as follows:

* `assets` holds the ETL job's static assets (HiveQL script plus Hive
  deserializer). You can leave this as-is (pointing to Snowplow
  Analytics' [own public bucket containing these assets](Hosted-assets))
  or replace this with your own private bucket containing the assets
* `log` is the bucket in which Amazon EMR will record processing
  information for this job run, including logging any errors  
* `in` is where you specify your In Bucket
* `processing` is where you specify your Processing Bucket
* `out` is where you specify your Out Bucket - **always include a
  sub-folder on this variable (see below for why)**
* `archive` is where you specify your Archive Bucket

Each of the bucket variables must start with an S3 protocol - either
`s3://` or `s3n://`. Each variable can include a sub-folder within the
bucket as required, and a trailing slash is optional.

**Important 1:** there is a bug in Hive on Amazon EMR where Hive dies if 
you attempt to write data to the root of an S3 bucket. **Therefore
always specify a sub-folder (e.g. `/events/`) for the `out` bucket
variable.**

**Important 2:** do not put your Processing Bucket location inside your In Bucket, or your Out Bucket inside your Processing Bucket, or you will create circular references which EmrEtlRunner cannot resolve when moving files.

The following are all valid bucket settings:

    :buckets:
      :assets: s3://my-public-snowplow-assets
      :in: s3n://my-cloudfront-logs/
      :processing: s3n://my-cloudfront-logs/processing
      :out: s3n://my-snowplow-data/events

Please note that all buckets must exist prior to running EmrEtlRunner.

#### emr

This is where we configure the operation of EMR. The variables with
defaults can typically be left as-is, but you will need to set:

1. `placement`, which is the Amazon EC2 region and availability zone
   in which the job should run, e.g. "us-east-1a" or "eu-west-1b"
2. `ec2_key_name`, which is the name of the Amazon EC2 key that you
   set up in the [Dependencies](#dependencies) above

Make sure that placement and the EC2 key you specify both belong to the
same region, or else EMR won't be able to find the key.

#### etl

This section is where we configure exactly how we want our ETL process to operate:

1. `collector_format`, what format is our collector saving data in? Currently the only supported format is "cloudfront", for the format saved by our CloudFront collector
2. `continue_on_unexpected_error`, continue processing even on unexpected row-level errors, e.g. an input file not matching the expected CloudFront format. Off ("false") by default
3. `storage_format`, can be "hive" or "non-hive". We discuss this further below

`storage_format` is an important setting. If you choose "hive", then the Snowplow event format outputted by EmrEtlRunner will be optimised to only work with Hive - you will **not** be able to load those event files into other database systems, such as Infobright (or eventually, Postgres, Google BigQuery, SkyDB et al). We believe that most people will want to load their Snowplow events into other systems, so the default setting here is "non-hive".

#### snowplow

This section allows you to update the versions of the Hive deserializer
(`serde`) and HiveQL scripts (`hive_hiveql` and `non_hive_hiveql`) run
by EmrEtlRunner. These variables let you upgrade the ETL process without
having to update the EmrEtlRunner application itself.

<a name="usage"/>
## Usage

<a name="usage-overview"/>
### Overview

There are two usage modes for EmrEtlRunner:

1. **Rolling mode** where EmrEtlRunner processes whatever raw Snowplow
   event logs it finds in the In Bucket
2. **Timespan mode** where EmrEtlRunner only processes those raw
   Snowplow event logs whose timestamp is within a timespan specified
   on the command-line

Timespan mode can be useful if you have a large backlog of raw Snowplow
event logs and you want to start by processing just a small subset of
those logs.

<a name="cli-options"/>
### Command-line options

Invoke EmrEtlRunner using Bundler's `bundle exec` syntax:

    $ bundle exec bin/snowplow-emr-etl-runner
    
Note the `bin/` sub-folder, and that the `bundle exec` command will
only work when you are inside the `emr-etl-runner` folder.

The command-line options for EmrEtlRunner look like this:

    Usage: snowplow-emr-etl-runner [options]

    Specific options:
        -c, --config CONFIG              configuration file
        -s, --start YYYY-MM-DD           optional start date *
        -e, --end YYYY-MM-DD             optional end date *
        -s, --skip staging,emr,archive   skip work step(s)
        -b, --process-bucket BUCKET      run emr only on specified bucket. Implies --skip staging,archive

    * filters the raw event logs processed by EmrEtlRunner by their timestamp

    Common options:
        -h, --help                       Show this message
        -v, --version                    Show version

A note on the `--skip` option: this takes a list of individual steps to skip.
So for example you could run **only** the Hive job with the command-line option:

    $ bundle exec snowplow-emr-etl-runner --skip staging,archive --config ./config.yml

<a name="running"/>
### Running in each mode

#### Rolling mode

Invoking EmrEtlRunner with just the `--config` option puts it into rolling
mode, processing all the raw Snowplow event logs it can find in your In
Bucket:

    $ bundle exec bin/snowplow-emr-etl-runner --config my-config.yml

#### Timespan mode

To run EmrEtlRunner in timespan mode, you need to specify the `--start`
and/or `--end` dates as well as the `--config` option, like so:

    $ bundle exec bin/snowplow-emr-etl-runner \
      --config my-config.yml \
      --start 2012-06-20 \
      --end 2012-06-24 

This will run EmrEtlRunner on log files which have timestamps in the period
20 June 2012 to 24 June 2012 inclusive.

Note that you do not have to specify both the start and end dates:

1. Specify `--start` only and the timespan will run from your start date
   up to today, inclusive
2. Specify `--end` only and the timespan will run from the beginning of
   time up to your end date, inclusive

If your raw Snowplow logs are generated by the Amazon CloudFront collector,
please note that CloudFront timestamps in **UTC**.

<a name="scheduling"/>
## Scheduling

<a name="scheduling-overview"/>
### Overview

Once you have the ETL process working smoothly, you can schedule a daily
(or more frequent) task to automate the daily ETL process.

We run our daily ETL jobs at 3am UTC, so that we are sure that we have
processed all of the events from the day before (CloudFront logs can
take some time to arrive).

To consider your different scheduling options in turn:

<a name="cron"/>
### cron

The recommended way of scheduling the ETL process is as a daily cronjob using the 
shell script available in the Snowplow GitHub repository at 
[`/3-enrich/emr-etl-runner/bin/snowplow-emr-etl-runner.sh`] [bash-script].

You need to edit this script and update the three variables:

    rvm_path=/path/to/.rvm # Typically in the $HOME of the user who installed RVM
    RUNNER_PATH=/path/to/snowplow/3-enrich/snowplow-emr-etl-runner
    RUNNER_CONFIG=/path/to/your-config.yml

So for example if you installed RVM as the `admin` user, then you would set:

    rvm_path=/home/admin/.rvm

Now, assuming you're using the excellent [cronic] [cronic] as a wrapper for 
your cronjobs, and that both cronic and Bundler are on your path, you can 
configure your cronjob like so:

    0 4   * * *   root    cronic /path/to/snowplow/3-enrich/bin/snowplow-emr-etl-runner.sh

This will run the ETL job daily at 4am, emailing any failures to you via cronic.

<a name="jenkins"/>
### Jenkins

Some developers use the [Jenkins] [jenkins] continuous integration server (or
[Hudson] [hudson], which is very similar) to schedule their Hadoop and Hive jobs.

Describing how to do this is out of scope for this guide, but the blog post
[Lowtech Monitoring with Jenkins] [jenkins-tutorial] is a great tutorial on using
Jenkins for non-CI-related tasks, and could be easily adapted to schedule
EmrEtlRunner.

<a name="windows"/>
### Windows Task Scheduler

For Windows servers, in theory it should be possible to use a Windows PowerShell
script plus [Windows Task Scheduler] [windows-task-scheduler] instead of bash and cron. However, this has not been tested or documented.

If you get this working, please let us know!

[emr-etl-runner]: https://github.com/snowplow/snowplow/tree/master/3-enrich/emr-etl-runner
[hadoop-etl]: https://github.com/snowplow/snowplow/tree/master/3-enrich/hadoop-etl
[trackers]: https://github.com/snowplow/snowplow/tree/master/1-trackers
[collectors]: https://github.com/snowplow/snowplow/tree/master/2-collectors
[getting-started]: http://snowplowanalytics.com/product/get-started.html

[git-install]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[ruby-install]: http://www.ruby-lang.org/en/downloads/
[nokogiri-install]: http://nokogiri.org/tutorials/installing_nokogiri.html
[rubygems-install]: http://docs.rubygems.org/read/chapter/3

[config-yml]: https://github.com/snowplow/snowplow/blob/master/3-enrich/emr-etl-runner/config/config.yml
[bash-script]: https://github.com/snowplow/snowplow/blob/master/3-enrich/emr-etl-runner/bin/snowplow-emr-etl-runner.sh

[cronic]: http://habilis.net/cronic/
[jenkins]: http://jenkins-ci.org/
[hudson]: http://hudson-ci.org/
[jenkins-tutorial]: http://blog.lusis.org/blog/2012/01/23/lowtech-monitoring-with-jenkins/
[windows-task-scheduler]: http://en.wikipedia.org/wiki/Windows_Task_Scheduler#Task_Scheduler_2.0