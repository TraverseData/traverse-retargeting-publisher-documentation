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
 2. [Connect us to a feed of your new subscribers](#adding-subscribers).
 2. [Upload the rest of your subscribers in batch](#adding-subscribers).

### Creating a subscriber list

*Creating subscriber lists programmatically is not yet supported.*

In the meantime, please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">contact us</a> and we will provide you a subscriber-list ID.

### Adding subscribers

To add subscribers to a list, use the following endpoint:

```
POST https://retargeting.traversedlp.com/v1/lists/{YOUR-SUBSCRIBER-LIST-ID-HERE}/recipients
```

The message body should be an array of JSON objects, each with the following properties:

| Property | Value |
|------|-------|
| `emailMd5Lower` | MD5 hash of trimmed, lowercased email address |
| `emailSha1Lower` | SHA-1 hash of trimmed, lowercased email address |

For example:

```javascript
[{
  emailMd5Lower: "1105677c8d9decfa1e36a73ff5fb5531",
  emailSha1Lower: "ba9d46a037766855efca2730031bfc5db095c654"
}, {
  emailMd5Lower: "9e3669d19b675bd57058fd4664205d2a",
  emailSha1Lower: "e9d71f5ee7c92d6dc9e92ffdad17b8bd49418f98"
}]
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
  campaignId: "6a11644c-690d-4bf3-bb19-4c3efba5a5a5",
  emailMd5Lower: "1105677c8d9decfa1e36a73ff5fb5531",
  emailSha1Lower: "ba9d46a037766855efca2730031bfc5db095c654",
  listIds: ["772823bd-b7be-4d23-bd78-96a577d02765"],
  advertiserProperties: {
    impressionId: "f53f6078-f802-4c98-90ca-e90aa56995ab",
    foo: "bar"
  }
}
```

*Note:* If the listener does not promptly reply with an HTTP 2xx status code, we may resubmit the request, later.

### Batch campaign requests

*Batch campaign requests are not yet supported.*
