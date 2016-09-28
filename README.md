# Traverse Email Retargeting Publisher Documentation

## Contents

  1. [Overview](#overview)
  2. [Getting started](#getting-started)
  3. [Best practices](#best-practices)
  4. [Security](#security)
  5. [Syncing your subscribers](#syncing-your-subscribers)
  6. [Receiving campaign requests](#receiving-campaign-requests)

## Overview

Traverse Email Retargeting allows marketers to trigger sending publisher-branded email advertisements to your subscribers.

## Getting started

To get started with Traverse Email Retargeting:

 1. [Review the best practices](#best-practices).
 2. [Get credentials](#security).
 3. [Sync your subscribers](#syncing-your-subscribers).
 4. [Consume campaign requests](#consume-campaign-requests).

## Security

All API calls are authorized via <a href="https://tools.ietf.org/html/rfc6750">OAuth 2.0 bearer tokens</a>.

Please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">contact us</a> for a token.

## Best practices

While we make every attempt to maintain the availability of our system, unexpected errors may occur:

 1. Always check whether the HTTP status code  is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#2xx_Success">2xx</a>* (success) before proceeding, and abort if not.
 2. If the status code is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#4xx_Client_Error">4xx</a>* (client error), your request is likely invalid. Review the documentation before retrying.
 3. If the status code is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#5xx_Server_Error">5xx</a>* (server error), there is likely a problem on our end. Please retry after 60 seconds.
 4. Otherwise, treat any unexpected response, status code, timeout or exception as a *5xx*.
 5. Log your requests and replies, and monitor for and review any failures.

## Syncing your subscribers

To sync your subscribers:

 1. [Create a subscriber list](#creating-a-subscriber-list).
 2. [Send us new subscribers in real time](#adding-individual-subscribers).
 2. [Upload the rest of your subscribers in batch](#adding-multiple-subscribers).

### Creating a subscriber list

*Creating subscriber lists programmatically is not yet supported.*

In the meantime, please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">contact us</a> and we will provide you a subscriber-list ID.

### Adding individual subscribers

To add a subscriber to a list, use the following endpoint:

```
POST https://retargeting.traversedlp.com/v1/list/{YOUR-SUBSCRIBER-LIST-ID-HERE}/subscriber
```

The message body should be a JSON object with the following properties:

| Property | Value |
|------|-------|
| `emailMd5Lower` | MD5 hash of trimmed, lowercased email address |
| `emailSha1Lower` | SHA-1 hash of trimmed, lowercased email address |

For example:

```javascript
{
  emailMd5Lower: "1105677c8d9decfa1e36a73ff5fb5531",
  emailSha1Lower: "ba9d46a037766855efca2730031bfc5db095c654"
}
```

## Adding multiple subscribers

To add multiple subscribers to a list, use the following endpoint:
```
POST https://retargeting.traversedlp.com/v1/list/{YOUR-SUBSCRIBER-LIST-ID-HERE}/subscribers
```

The message body should be a CSV:

 1. Commas, quotes and line terminators per <a href="https://tools.ietf.org/html/rfc4180">RFC 4180</a>.
 2. A  header row, consisting of the column names, is required.
 3. <a id="f1">At least one of the `emailMdLower` or `emailSha1Lower` columns is required.</a>

The CSV must include the following columns:

| Column | Description |
|------|-------|----------|
| `emailMd5Lower` | MD5 hash of trimmed, lowercased email address |
| `emailSha1Lower` | SHA-1 hash of trimmed, lowercased email address |

For example:
```
emailMd5Lower,emailSha1Lower
1105677c8d9decfa1e36a73ff5fb5531,ba9d46a037a766855efca2730031bfc5db095c654
380236b305e0e37b2bfae587966c34e2,52245908b2816145e7b101c4304982c6f33df9e44,
```

## Consuming campaign requests

We create a campaign request when an advertiser wants to send a campaign to one of your subscribers.

There are two ways to consume campaign requests:

 1. [Real time](#real-time-campaign-request-listener).
 2. [Batch](#batch-campaign-requests).
 
### Real-time campaign-request listener

To receive campaign requests in real-time, create an HTTPS listener and <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">let us know the URL</a>.

We will POST JSON objects with the following properties to the URL:

| Property | Description |
|----------|-------------|
| `campaignId` | Campaign ID |
| `emailMd5Lower` | MD5 hash of trimmed, lowercased email address |
| `emailSha1Lower` | MD5 hash of trimmed, lowercased email address |
| `advertiserProperties` | Custom advertiser properties (*advanced*) |

For example:

```javascript
{
  campaignId: "6a11644c-690d-4bf3-bb19-4c3efba5a5a5"
  emailMd5Lower: "1105677c8d9decfa1e36a73ff5fb5531",
  emailSha1Lower: "ba9d46a037766855efca2730031bfc5db095c654",
  advertiserProperties: {
    impressionId: "f53f6078-f802-4c98-90ca-e90aa56995ab"
  }
}
```

*Note:* If the listener does not promptly reply with an HTTP 2xx status code, we may resubmit the request, later.

### Batch campaign requests

*Batch campaign requests are not yet supported.*
