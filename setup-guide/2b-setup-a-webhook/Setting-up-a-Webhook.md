<a name="top" />

[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) > [**Step 2b: Setup a Webhook**](Setting-up-a-webhook)

[[https://d3i6fms1cm1j0i.cloudfront.net/github-wiki/images/snowplow-architecture-1b-webhooks.png]]

Snowplow allows you to collect events via the [webhooks][webhooks-defn] of supported third-party software.

Webhooks allow this third-party software to send their own internal event streams to [Snowplow Collectors](Setting-up-a-Collector) for further processing. Webhooks are sometimes referred to as "streaming APIs" or "HTTP response APIs".

1. [Choose and configure a Webhook](#choose-configure)

<a name="choose-configure" />

## 1. Choose and configure a Webhook

**If you are interested in sponsoring a new webhook integration for Snowplow, please [talk to us](Talk-to-us).**

The following Webhooks are currently available for setup:

| **Webhook** (click for setup)                  | **Description**                                                                          | **Support in Snowplow**     |
|:-----------------------------------------------|:-----------------------------------------------------------------------------------------|:----------------------------|
| **[Iglu](Iglu-webhook-setup)** | For tracking [Iglu][iglu]-compatible self-describing events | [0.9.11][snowplow-0.9.11]+ |
| **[CallRail](CallRail-webhook-setup)**         | For tracking completed telephone calls logged by [CallRail][callrail-website]           | [0.9.11][snowplow-0.9.11]+ |
| **[MailChimp](MailChimp-webhook-setup)**       | For tracking email and email-related events delivered by [MailChimp][mailchimp-website] | [0.9.11][snowplow-0.9.11]+ |
| **[Mandrill](Mandrill-webhook-setup)**       | For tracking email and email-related events delivered by [Mandrill][mandrill-website] | [0.9.14][snowplow-0.9.14]+ |
| **[PagerDuty](PagerDuty-webhook-setup)**       | For tracking incidents reported to [PagerDuty][pagerduty-website] | [0.9.14][snowplow-0.9.14]+ |
| **[Pingdom](Pingdom-webhook-setup)**       | For tracking incidents detected by [Pingdom][pingdom-website] | [0.9.14][snowplow-0.9.14]+ |
| **[SendGrid](SendGrid-webhook-setup)**       | For tracking email and email-related events delivered by [SendGrid][sendgrid-website] | [Release 75][r75]+ |
| **[Urban Airship Connect](Urban-Airship-Connect-webhook-setup)** | For tracking mobile notification events generated by [Urban Airship Connect][urban-airship-connect-website] | [Release 75][r75]+ |
| **[Zendesk](Zendesk-webhook-setup)** | For tracking tickets handling in [Zendesk Support][urban-airship-connect-website] | [0.9.11][snowplow-0.9.11]+ (via [Iglu webhook](Iglu-webhook-setup)) |
| **[Mailgun](Mailgun-webhook-setup)** | For tracking [Mailgun][mailgun] events related to email delivery | [Release 97][r97]+ |
| **[Olark](Olark-webhook-setup)** | For tracking [Olark][olark] transcripts and off-line messages | [Release 97][r97]+ |
| **[Unbounce](Unbounce-webhook-setup)** | For tracking [Unbounce][unbounce] form submission events | [Release 97][r97]+ |
| **[StatusGator](StatusGator-webhook-setup)** | For tracking [StatusGator][statusgator] cloud provider availability events | [Release 97][r97]+ |
| **[Marketo](Marketo-webhook-setup)** | For tracking events related to [Marketo][marketo] | [Release 107][r107]+ |
| **[Vero](Vero-webhook-setup)** | For tracking events emitted by [Vero][vero] webhooks | [Release 107][r107]+ |

**If you are interested in sponsoring a new webhook integration for Snowplow, please [talk to us](Talk-to-us).**

Back to [Snowplow setup](Setting-up-Snowplow).

[webhooks-defn]: http://en.wikipedia.org/wiki/Webhook

[iglu]: https://github.com/snowplow/iglu
[callrail-website]: http://www.callrail.com/
[mailchimp-website]: http://mailchimp.com/
[mandrill-website]: https://mandrill.com/
[pagerduty-website]: http://www.pagerduty.com/
[pingdom-website]: https://www.pingdom.com/
[sendgrid-website]: https://sendgrid.com/
[urban-airship-connect-website]: https://www.urbanairship.com/products/connect
[zendesk-website]: https://www.zendesk.com/support/
[mailgun]: https://www.mailgun.com
[olark]: https://www.olark.com/
[unbounce]: https://unbounce.com
[statusgator]: https://statusgator.com/
[marketo]: https://www.marketo.com/
[vero]: https://www.getvero.com/


[snowplow-0.9.11]: https://github.com/snowplow/snowplow/releases/tag/0.9.11
[snowplow-0.9.14]: https://github.com/snowplow/snowplow/releases/tag/0.9.14
[r75]: https://github.com/snowplow/snowplow/releases/tag/r75-long-legged-buzzard
[r97]: https://github.com/snowplow/snowplow/releases/tag/r97-knossos
[r107]: https://github.com/snowplow/snowplow/releases/tag/r107-trypillia
