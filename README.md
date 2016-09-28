# Traverse Email Retargeting Publisher Documentation

## Contents

  1. [Overview](#overview)
  2. [Getting started](#getting-started)
  3. [Best practices](#best-practices)
  4. [Security](#security)
  5. [Syncing your subscribers](#syncing-your-subscribers)
  6. [Setting up campaign templates](#setting-up-campaign-templates)
  6. [Receiving campaign requests](#receiving-campaign-requests)

## Overview

Traverse Email Retargeting allows marketers to send publisher-branded email advertisements to your subscribers.

## Getting started

To get started with Traverse Email Retargeting:

 1. [Review the best practices](#best-practices).
 2. [Get credentials](#security).
 3. [Sync your subscribers](#syncing-your-subscribers).
 4. [Set up a campaign template](#setting-up-campaign-templates).
 5. [Receive campaign requests](#receiving-campaign-requests).

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

*Creating exclusion lists programmatically is not yet supported.*

In the meantime, please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">contact us</a> and we will provide you a subscriber-list ID.

### Adding individual subscribers

To add a subscriber to a list, use the following endpoint:

```
POST https://retargeting.traversedlp.com/v1/list/{YOUR-SUBSCRIBER-LIST-ID-HERE}/subscriber
```

The message body should be a JSON object with one or more of the following properties:

| Property | Value |
|------|-------|
| `email` | Email address (*N.B.* we will hash before storing).
| `emailMd5Lower` | MD5 hash of trimmed, lowercased email address |
| `emailSha1Lower` | SHA-1 hash of trimmed, lowercased email address |

For example:

```javascript
{
  emailMd5Lower: "1105677c8d9decfa1e36a73ff5fb5531",
  emailSha1Lower: "ba9d46a037766855efca2730031bfc5db095c654"
}
```

## Adding multiple subscribers.

To add multiple subscribers, use the following endpoint:
```
POST https://retargeting.traversedlp.com/v1/list/{YOUR-SUBSCRIBER-LIST-ID-HERE}/subscribers
```

The message body should be a CSV:

 1. Commas, quotes and line terminators per <a href="https://tools.ietf.org/html/rfc4180">RFC 4180</a>.
 2. A  header row, consisting of the column names, is required.
 3. <a id="f1">At least one of the `emailMdLower` or `emailSha1Lower` columns is required.</a>

It must include or more of the following columns:

| Column | Description |
|------|-------|----------|
| `email` | Email address (*N.B.* we will hash before storing) |
| `emailMd5Lower` | MD5 hash of trimmed, lowercased email address |
| `emailSha1Lower` | SHA-1 hash of trimmed, lowercased email address |

For example:
```
emailMd5Lower,emailSha1Lower
ba9d46a037a766855efca2730031bfc5db095c654,1105677c8d9decfa1e36a73ff5fb5531
52245908b2816145e7b101c4304982c6f33df9e4,380236b305e0e37b2bfae587966c34e2
```

## Setting up campaign templates

*Managing campaign templates programmatically is not yet supported.*

In the meantime, please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">contact us</a> and we will provide you a campaign-template ID.

## Receiving campaign requests
