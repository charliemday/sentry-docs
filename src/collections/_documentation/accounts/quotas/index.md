---
title: 'Quotas & Events'
sidebar_order: 0
---

Events and quotas are interconnected in Sentry. At the most basic, when you [subscribe to Sentry](https://sentry.io/pricing/), you pay for the number of events to be tracked. This number of events is your quota. When an event occurs, it counts toward your quota.

Sentry’s flexibility means you can exercise fine-grained control over which events count toward your quota.

Let’s clarify a few terms to start:
-   Event - an event is one instance of you sending Sentry data. Generally, this data is an error. Every event has a unique set of characteristics, called its fingerprint.
-   Issue - an issue is a grouping of similar events, who all share the same fingerprint. For example, Sentry groups events together when they are triggered by the same part of your code. For more information, see [Grouping & Fingerprinting](https://docs.sentry.io/data-management/event-grouping/).
-   Quota - your quota is the monthly number of events you pay Sentry to track.

## What Counts Toward My Quota?

Sentry evaluates each event to ensure it includes a valid DSN and project as well as whether the event can be parsed and contains valid fingerprint information; if any of these items are missing or incorrect, the event is rejected. Each of the following then occurs to determine if an event counts toward your quota:

1. **SDK configuration.** 
      The SDK configuration either allows the event or filters the event out. For more information, see [Outbound Filters in our guide to Manage Your Event Stream](https://docs.sentry.io/guides/manage-event-stream/#outbound-filters) or [Filtering Events](https://docs.sentry.io/error-reporting/configuration/filtering/?platform=browser).

2. **SDK sample rate**

  If a sample rate is defined for the SDK, Sentry evaluates whether this event should be sent as a representative fraction of the sampled events. Setting a sample rate is documented for each SDK. For more information, see [Configuration, sampleRate](https://docs.sentry.io/error-reporting/configuration/?platform=browser#sample-rate).
3. **Quota availability**
  Events that exceed your quota are not sent. If you’ve exceeded your quota threshold, the server will respond with a 429 HTTP status code.
4. **Event repetition**
  -   If you have intervened to discard events with the same fingerprint, the event does not count toward your quota. For more information, see [Delete & Discard in our guide to Manage Your Event Stream](https://docs.sentry.io/guides/manage-event-stream/#delete--discard) or [Filter by Issue]( https://docs.sentry.io/accounts/quotas/#filter-by-issue).
  -   If the previous event was resolved, this event counts toward your quota because it may represent a regression in your code.
  -   If you have intervened to ignore alerts about events with the same fingerprint, this event counts toward your quota because the event is still occurring.
5. **Spike protection**
  Sentry’s spike protection prevents huge overages from consuming your event capacity. For more information, see [Spike Protection](https://docs.sentry.io/accounts/quotas/#spike-protection).

Finally, depending on your project’s configuration and the plan you subscribe to, Sentry may also check:

6. **Rate limit for the project**
  If the rate limit for the project has been exceeded, and your subscription allows, the event will not be counted. For more information, see [Rate Limiting in our guide to Manage Your Event Stream](https://docs.sentry.io/guides/manage-event-stream/#4-rate-limiting) or  [Rate Limiting Projects](https://docs.sentry.io/accounts/quotas/#id1).
7. **Inbound Filters**
  If any inbound filter is set for this type of event, and your subscription allows, the event will not be counted. For more information, see [Inbound Filters in our guide to Manage Your Event Stream](https://docs.sentry.io/guides/manage-event-stream/#inbound-data-filters) or [Inbound Data Filter](https://docs.sentry.io/accounts/quotas/#inbound-data-filters). These include:
  -   Common browser extension errors
  -   Events coming from localhost
  -   Known legacy browsers errors
  -   Custom filters to filter out errors:
  -   By their error message
  -   From specific release versions of your code
  -   From certain IP addresses
8. The origin of the event, if allowed domains are defined for the project. For more information, see [Preventing Abuse](https://docs.sentry.io/clients/javascript/usage/#preventing-abuse).

After these checks are processed, the event counts toward your quota. It is accepted into Sentry, where it persists and is stored.

A few important notes:
  - For mobile users, a storage fee is also charged if the Native SDK minidump is attached to the event in addition to the event counting toward your quota. For more information, see [Event Attachments (Preview)](https://docs.sentry.io/platforms/native/minidump/#event-attachments-preview)
  - If the event exceeds 200kb compressed for events and 20MB compressed for minidump uploads (all files combined), the event will be rejected.




## What Counts Towards My Quota (Table View)?

Not all events count towards your monthly event quota. In general, when events are processed or stored they will count towards your quota. Please see the table below to see which events count.


<table>
  <tbody>
    <tr>
      <td>
        <strong>Events which are...</strong>
      </td>
      <td>
        <strong>Count towards quota</strong>
      </td>
    </tr>
    <tr>
      <td>filtered by SDK configuration</td>
      <td>no</td>
    </tr>
    <tr>
      <td>dropped because of SDK sampling rate</td>
      <td>no</td>
    </tr>
    <tr>
      <td>filtered using inbound filters in project settings  </td>
      <td>no</td>
    </tr>
    <tr>
      <td>dropped because of spike protection</td>
      <td>no</td>
    </tr>
    <tr>
      <td>dropped after exceeding quota</td>
      <td>no</td>
    </tr>
    <tr>
      <td>in a resolved issue</td>
      <td>yes</td>
    </tr>
    <tr>
      <td>in a deleted issue</td>
      <td>yes</td>
    </tr>
    <tr>
      <td>in an ignored issue</td>
      <td>yes, even after the issue is ignored</td>
    </tr>
    <tr>
      <td>in a delete-and-discarded issue*</td>
      <td>only events before delete-and-discard is enabled</td>
    </tr>
    <tr>
      <td>over the per-project rate limit*</td>
      <td>no</td>
    </tr>
  </tbody>
</table>

{% include components/alert.html
    title="Note"
    content="Delete and discard and per-project rate limits are only available on Business and Enterprise plans"
    level="warning"
%}

## Increasing Quotas

You can add additional quota at any time during your billing period, either by upgrading to a higher tier or increasing your on-demand capacity. While the available plans will fit most individual and business needs, Sentry is designed to handle large throughput so if your team needs more, we’re happy to help. Reach out to our sales team at [sales@sentry.io](mailto:sales%40sentry.io) to learn more about increasing capacity.

## Rate Limiting Projects {#id1}

Per-key rate limits allow you to set the maximum volume of events a key will accept during a period of time.

For example, you may have a project in production that generates a lot of noise. With a rate limit you could set the maximum amount of data to “500 events per minute”. Additionally, you could create a second key for the same project for your staging environment which is unlimited, ensuring your QA process is still untouched.

To setup rate limits, navigate to the Project you wish to limit, go to **[Project] » Client Keys » Configure**. Select an individual key or create a new one, and you’ll be able to define a rate limit as well as see a breakdown of events received by that key.

{% capture __alert_content -%}
Per-key rate limiting is available only on Business and Enterprise Plans
{%- endcapture -%}
{%- include components/alert.html
  title="Note"
  content=__alert_content
  level="warning"
%}

## Inbound Filters {#inbound-data-filters}

In some cases, the data you’re receiving in Sentry is hard to filter, or you simply don’t have the ability to update the SDK’s configuration to apply the filters. Due to this Sentry provides several ways to filter data server-side, which will also apply before any rate limits are checked.

### Built-in Filters

Various built-in filters are available within Sentry. You can find these by going to **[Project] » Project Settings » Inbound Filters**. Each filter caters to specific situations, such as web crawlers or old browsers, and can be enabled as needed by the specific application.

### IP Blocklist

If you have a rogue client, you may find yourself simply wanting to block that IP from sending data. Sentry supports this by going to **[Project] » Project Settings » Inbound Filters** and adding the IP addresses (or subnets) under the **Filter errors from these IP addresses** section. For example, you can enter '127.0.0.1' or '10.0.0.0/8'.

### Filter by releases

In the case you have a problematic release that is causing an excessive amount of noise, you can ignore all events from that release. Sentry supports this by going to **[Project] » Project Settings » Inbound Filters** and adding the releases under the **Filter errors from these releases** section.

{% capture __alert_content -%}
Filter by releases is available only on Business and Enterprise Plans
{%- endcapture -%}
{%- include components/alert.html
  title="Note"
  content=__alert_content
  level="warning"
%}

### Filter by error message

You can ignore a specific or certain kind of error by going to **[Project] » Project Settings » Inbound Filters** and adding the error message under the **Filter errors by error message** section.

{% capture __alert_content -%}
Filter by error message is available only on Business and Enterprise Plans
{%- endcapture -%}
{%- include components/alert.html
  title="Note"
  content=__alert_content
  level="warning"
%}

### Filter by issue

If there is an issue that you are unable to take action on but that continues to occur, you can delete and discard it from the issue details page by clicking “delete and discard future events.” This will delete most data associated with an issue and filter out matching events before they ever reach your stream. Matching events will not count towards your quota.

{% capture __alert_content -%}
Discarding issues is available only on Business and Enterprise Plans
{%- endcapture -%}
{%- include components/alert.html
  title="Note"
  content=__alert_content
  level="warning"
%}

## Spike Protection

Spike protection helps prevent huge overages from consuming your event capacity. We use your historical event volume to implement a dynamic rate limit which discards events when you hit its threshold. This dynamic rate limit is designed to protect you from short-term spikes and the threshold will increase if your increased volume persists for many hours.

When spike protection is activated, we limit the number of events accepted in any minute to:

`maximum(20, 6 x average events per minute over the last 24 hours)`

_The 24 hour window ends at the beginning of the current hour, not at the current minute._

What this means is that if you experience a spike, we will temporarily protect you, but if the increase in volume is sustained, the spike protection limit will gradually increase until Sentry finally accepts all events.

### How does spike protection help? {#how-does-spike-protection-help}

Because Sentry bills on monthly event volume, spikes can consume your Sentry capacity for the rest of the month. If it really is a spike (**large** and **temporary** increase in event volume), spike protection drops events during the spike to try and conserve your capacity. We also send an email notification to the Owner when spike protection is activated.



### Controlling Volume

If your projects have a high volume of events, you can control how many errors Sentry receives in a few ways:

-   [Configure]({%- link _documentation/error-reporting/configuration/index.md -%}#common-options) the SDK to reduce the volume of data you’re sending
-   Turn on [Inbound Filters]({%- link _documentation/accounts/quotas/index.md -%}#inbound-data-filters) for legacy browsers, browser extensions, localhost, and web crawlers. Any filtered events will not count towards your quota
-   Set a [per-key rate limits]({%- link _documentation/accounts/quotas/index.md -%}#id1) for each DSN key in a project

## Attributes Limits

Sentry imposes hard limits on various components within an event. While the limits may change over time and vary between attributes most individual attributes are capped at 512 characters. Additionally, certain attributes also limit the maximum number of items.

For example, `extra` data is limited to around 256k characters, and each item is capped at around 16k characters.

Generic attributes like the event’s label also have limits but are more flexible depending on their case. For example, the message attribute is limited to 1024 characters.

The following limitations will be automatically enforced:

-   Events greater than 200KiB are immediately dropped (pre decompression).
-   Stack traces with large frame counts will be trimmed (the middle frames are dropped).
-   Collections exceeding the max items will be trimmed down to the maximum size.
-   Individual values exceeding the maximum length will be trimmed down to the maximum size.
