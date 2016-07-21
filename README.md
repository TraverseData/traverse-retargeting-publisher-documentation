# Traverse Email Retargeting Publisher API

## Contents

  1. [Overview](#overview)
  2. [Getting started](#getting-started)
  3. [Authentication](#authentication)
  4. [Error handling](#error-handling)
  5. [Syncing your suppression list(s)](#syncing-your-suppression-lists)
    1. [Creating a suppression list](#creating-a-suppression-list)
    2. [Suppression-list representation](#suppression-list-representation)
    3. [Updating a suppression list](#updating-a-suppression-list)
    4. [Listing your suppression lists](#listing-your-suppression-lists)
  6. [Syncing your subscribers](#syncing-your-subscribers)
    1. [Creating a subscriber list](#creating-a-subscriber-list)
    2. [Subscriber-list representation](#subscriber-list-representation)
    3. [Updating a subscriber list](#updating-a-subscriber-list)
    4. [Listing your subscriber lists](#listing-your-subscriber-lists)
  7. [Setting up your template(s)](#setting-up-your-templates)
  8. [Best practices](#best-practices)

## Overview

Traverse Email Retargeting allows marketers to send publisher-branded email advertisements to your newsletter subscribers.

## Getting started

To get started with Traverse Email Retargeting:

 1. [Get credentials](#authentication).
 2. [Review the error-handling guidance](#error-handling).
 3. [Sync your suppression list(s)](#syncing-your-suppression-lists).
 4. [Sync your subscribers](#syncing-your-subscribers).
 5. [Set up your template(s)](#setting-up-your-templates).

## Authentication

**Important!** *All API calls are authenticated via <a href="https://en.wikipedia.org/wiki/Basic_access_authentication">basic auth</a>.*

Please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">contact Traverse</a> for credentials.

## Error handling

While we make every attempt to maintain the availability of our system, unexpected errors may occur:

 1. Always check whether the HTTP status code  is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#2xx_Success">2xx</a>* (success) before proceeding, and abort if not.
 2. If the status code is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#4xx_Client_Error">4xx</a>* (client error), your request is likely invalid. Review the documentation before retrying.
 3. If the status code is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#5xx_Server_Error">5xx</a>* (server error), there is likely a problem on our end. Please retry after 60 seconds.
 4. Otherwise, treat any unexpected response, status code, timeout or exception as a *5xx*.
 5. Log your requests and replies, and monitor for and review any failures.

## Syncing your suppression list(s)

In order to sync a suppression list:

 1. [Create the list](#creating-a-suppression-list).
 2. [Upload its contents](#updating-a-suppression-list).
 3. Repeat (automate) step 2 at least once per week.

### Creating a suppression list

To create a suppression list, upload a [representation](#suppression-list-representation), without an `id`, to:
```
POST https://retargeting.traversedlp.com/v1/blacklist
```

The response will contain an updated [representation](#suppression-list-representation), with an `id`.

Notes:

 1. Do not include the `id`. The system will assign one for you.
 2. This just creates an empty list. You still need to [upload its contents](#updating-a-suppression-list).
 3. Make note of the returned `id` so that you can update the list.

### Suppression-list representation

Suppression lists are represented by a JSON object with the following fields::

| Name          | Value                                | Required |
| ------------- |--------------------------------------|----------|
| `id`          | Traverse-generated unique identifier | No       |
| `name`        | Client-supplied name                 | No       |
| `description` | Client-supplied description          | No       |

For example:

```javascript
{
  id: "401b1f0c-0307-4efa-aab0-49f57f930572",
  name: "Master suppression list",
  description: "Unsubscribes, complaints, bounces"
}
```

### Updating a suppression list

To update a suppression list's recipients, upload a CSV:
```
PUT https://retargeting.traversedlp.com/v1/blacklists/{id}/hashes
```

Format:

 1. Commas, quotes and line terminators per <a href="https://tools.ietf.org/html/rfc4180">RFC 4180</a>.
 2. A  header row, consisting of the column names, is required.
 3. <a id="f1">At least one of the `emailMdLower` or `emailSha1Lower` columns is required.</a>
 4. Additional columns should not be included, and will be ignored.

Columns:

| Name             | Value                                           | Required                |
|------------------|-------------------------------------------------|-------------------------|
| `emailMd5Lower`  | MD5 hash of trimmed, lowercased email address   | No<sup id="a1">[3](#f1) |
| `emailSha1Lower` | SHA-1 hash of trimmed, lowercased email address | No<sup id="a1">[3](#f1) |

For example:
```
emailMd5Lower,emailSha256Lower
ba9d46a037766855efca2730031bfc5db095c654,1105677c8d9decfa1e36a73ff5fb5531
52245908b2816145e7b101c4304982c6f33df9e4,380236b305e0e37b2bfae587966c34e2
```

A successful response will have a 2xx status code.

### Listing your suppression lists

To list your suppression lists:
```
GET https://retargeting.traversedlp.com/v1/blacklists
```

The response will include a JSON array of [representations](#suppression-list-representation).

## Syncing your subscriber list(s)

In order to sync a subscriber list:

 1. [Create the list](#creating-a-subscriber-list).
 2. [Upload its contents](#updating-a-subscriber-list).
 3. Repeat (automate) step 2 at least once per month.

### Creating a subscriber list

To create a subscriber list, upload a [representation](#subscriber-list-representation), without an `id`, to:
```
POST https://retargeting.traversedlp.com/v1/list
```

The response will contain an updated [representation](#subscriber-list-representation), with an `id`.

Notes:

 1. Do not include the `id`. The system will assign one for you.
 2. This just creates an empty list. You still need to [upload its contents](#updating-a-subscriber-list).
 3. Make note of the returned `id` so that you can update the list.

### Subscriber-list representation

Subscriber lists are represented by a JSON object with the following fields::

| Name          | Value                                | Required |
| ------------- |--------------------------------------|----------|
| `id`          | Traverse-generated unique identifier | No       |
| `name`        | Client-supplied name                 | No       |
| `description` | Client-supplied description          | No       |

For example:

```javascript
{
  id: "92afb81d-260e-49f0-b918-9960de0dd056",
  name: "openers",
  description: "Subscribers who have opened in the past 60 days"
}
```

### Updating a subscriber list

To update a subscriber list's recipients, upload a CSV:
```
PUT https://retargeting.traversedlp.com/v1/lists/{id}/hashes
```

Format:

 1. Commas, quotes and line terminators per <a href="https://tools.ietf.org/html/rfc4180">RFC 4180</a>.
 2. A  header row, consisting of the column names, is required.
 3. Any columns not listed below should not be included, and will be ignored.

Columns:

| Name             | Value                              | Required |
|------------------|------------------------------------|----------|
| `email`          | Email address                      | Yes      |
| `ip`             | Opt-in IP address                  | Yes      |
| `source`         | Opt-in source (URL or domain name) | Yes      |
| `timestamp`      | Opt-in timestamp (<a href="https://en.wikipedia.org/wiki/ISO_8601">ISO-8601</a> date/timestamp, nominally UTC) | Yes |

For example:
```
email,ip,source,timestamp
somebody@example.com,1.2.3.4,example.com,2016-07-20Z
operations@traversedlp.com,5.6.7.8,example.com,2015-01-23T20:21:26Z
```

A successful response will have a 2xx status code.

### Listing your subscriber lists

To list your subscriber lists:
```
GET https://retargeting.traversedlp.com/v1/lists
```

The response will include a JSON array of [representations](#subscriber-list-representation).

## Setting up your templates

Traverse Email Retargeting uses your branded template for all advertising email sent to your subscribers.

Before we can start sending mail, please send us your HTML header and footer.

Please also indicate which subscriber and suppression list(s) to use for each template.
