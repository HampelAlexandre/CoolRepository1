### Overview

We recommend that Snowplow users create an AWS sub-account and sandbox all of their Snowplow operations into that account. 

This is particularly recommended for any users who are working with (or plan to work with) Snowplow Professional Services - as you can then assign Snowplow Professional Services liberal permissions on this sub-account, without impacting on your main account (or other sub-accounts) in any way.

### Setting up an AWS sub-account

#### 1. Setup Consolidated Billing

As a first step, log into your AWS account and click the "Sign up for Consolidated Billing" button:

https://portal.aws.amazon.com/gp/aws/developer/account?ie=UTF8...

#### 2. Create a new account

2. From a non-logged in browser, you will then want to sign up again to AWS again like this:

https://portal.aws.amazon.com/gp/aws/developer/registration/in...

Call this new account `snowplow-acme` (where Acme is your company name).

#### 3. Set up billing

Link this new account into your Consolidated Billing in your main account, and add billing information. This will allow us to spin up instances, create new S3 buckets etc.

#### 4. Enable the services that Snowplow requires

Next, you need to enable the services Snowplow will require in the new subaccount.

For the Snowplow batch (Hadoop) flow:

* CloudFront
* CloudFormation
* EC2
* Elastic MapReduce
* S3
* Redshift

For the Snowplow real-time (Kinesis) flow:

* As above, plus:
* Kinesis
* DynamoDB

### Next steps

You should then create the IAM user for us within this subaccount. That user should not have access to any of your existing AWS infrastructure.

For details on setting up the IAM user, see [[IAM setup]].
