#### Traverse Email Retargeting
# Publisher Documentation v1.1



## Contents

  1. [Overview](#overview)
  2. [Getting started](#getting-started)
  3. [API Access](#api-access)
  4. [Syncing your subscribers](#syncing-your-subscribers)
  5. [Configuring your DNS](#configuring-dns)
  6. [Best practices](#best-practices)


<br />

## Overview

Traverse Email Retargeting allows marketers to trigger sending publisher-branded email advertisements to your subscribers. Traverse sends the email on your behalf and tracks engagement while managing complaints and unsubscribes.


<br />

## Getting started

To get started with Traverse Email Retargeting:

 1. [Get credentials](#api-access).
 3. [Sync your subscribers](#syncing-your-subscribers).
 4. [Configure your DNS](#configuring-dns).


<br />

## API Access

All API calls are authorized via <a href="https://tools.ietf.org/html/rfc6750">OAuth 2.0 bearer tokens</a>. To authenticate a request, the access token must be passed with the `Authentication` header, like so: `Authentication: Bearer {your-bearer-token-here}`


Please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">contact us</a> for a token.


<br />

## Syncing your subscribers

To sync your subscribers:

 1. [Create a subscriber list](#creating-a-subscriber-list).
 2. [Upload the rest of your subscribers in batch](#adding-subscribers).
 3. [Connect us to a feed of your new subscribers](#adding-subscribers).


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


<br />

## Configuring DNS

Traverse uses Amazon SES to send email. You will need to make a few minor DNS updates on your end to get things started.

1. __Choose a subdomain to mail from.__

    This can be any subdomain you are not currently using (i.e. `{subdomain}.{publisher-domain}.com`).

2. __Configure the subdomain to receive email.__

    In order to legally send email, Traverse must be able to receive incoming messages to this subdomain. To facilitate this, set up a CNAME record which points the sending subdomain domain to our receiving domain. The record should look like this:

    | Record Name | Record Type | Record Value |
    ------|-------|-------|
    | `{subdomain}.{publisher-domain}.com` | CNAME | srvrt.com |

3. __Configure the subdomain to send email.__

    The final step is to configure your subdomain to demonstrate ownership and configure DKIM. This is handled by configuring a number of TXT records for the domain. These settings are generated
    on a per-domain process, so <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">contact us</a> when you are ready to make the changes.


<br />

## Best practices

While we make every attempt to maintain the availability of our system, unexpected errors may occur:

 1. Always check whether the HTTP status code  is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#2xx_Success">2xx</a>* (success) before proceeding, and abort if not.
 2. If the status code is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#4xx_Client_Error">4xx</a>* (client error), your request is likely invalid. Review the documentation before retrying.
 3. If the status code is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#5xx_Server_Error">5xx</a>* (server error), there is likely a problem on our end. Please retry after 60 seconds.
 4. Otherwise, treat any unexpected response, status code, timeout or exception as a *5xx*.
 5. Log your requests and replies, and monitor for and review any failures.
