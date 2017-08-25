#### Traverse Email Retargeting
# Publisher Documentation v1.1



## Contents

  1. [Overview](#overview)
  2. [Getting started](#getting-started)
  3. [API Access](#api-access)
  4. [Syncing Lists](#syncing-your-lists)
  5. [Configuring DNS](#configuring-dns)
  6. [Publisher Header/Footer](#publisher-header-and-footer)
  7. [Best practices](#best-practices)


<br />

## Overview

Traverse Email Retargeting allows marketers to trigger sending publisher-branded email advertisements to your subscribers. Traverse sends the email on your behalf and tracks engagement while managing complaints and unsubscribes.


<br />

## Getting started

To get started with Traverse Email Retargeting:

 1. [Get credentials](#api-access).
 2. [Sync your lists](#syncing-your-lists).
 3. [Configure your DNS](#configuring-dns).
 4. [Provide your branded header/footer](#publisher-header-and-footer).


<br />

## API Access

All API calls are authorized via <a href="https://tools.ietf.org/html/rfc6750">OAuth 2.0 bearer tokens</a>. To authenticate a request, the access token must be passed with the `Authentication` header, like so: `Authentication: Bearer {your-bearer-token-here}`


Please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">contact us</a> for a token.


<br />

## Syncing Your Lists

Most campaigns will have two associated lists--a subscription list for individuals you would like to mail, and a suppression list for those who should be excluded. The subsription list is hosted by Traverse and periodically updated by the publisher. There are a number of different ways to set up the suppression list, and your capabilities here will determine specifics about the rest of the integration.

To sync your lists:

 1. [Determine your integration type.](#integration-types)
 2. [Configure your suppression list.](#configuring-your-suppression-list)
 2. [Create a subscriber list.](#creating-a-list)
 2. [Upload your current subscribers in batch.](#adding-subscribers-batch)
 3. [Connect us to a feed of your new subscribers.](#adding-subscribers-Individual)

### Types of Integrations

Traverse supports working with suppression lists in a number of different ways. A publisher's integration type will depend on which method they are able to support.

- __Subscription Lookup:__

    This is the preferred method of integration. The suppression list is hosted and maintained by the publisher, who provides Traverse with a [subscription lookup endpoint](subscription-lookup-endpoint). The publisher provides Traverse with an inclusion list of hashed emails (both MD5 and SHA1), and uses Traverse's [recipient endpoint](#adding-subscribers-individual) to include more recipients after the initial bulk upload.

- __Traverse-Hosted:__

    Traverse hosts the suppression list and the publisher updates this list programmatically. This requires the publisher to upload the initial subscriber list and make updates with plaintext emails rather than hashes. The supression list and updates can be either plaintext emails or email hashes.

- __3rd Party:__

    Publisher provides Traverse access to a 3rd party unsubscribe service such as UnsubCentral or the publisher's existing ESP. The recipients on your subscription list may need to be in plaintext depending on how this 3rd party service is implemented. Please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">reach out</a> to Traverse operations to discuss the specifics of your 3rd party service before putting together the initial subscriber list.


### Configuring Your Suppression List

For an integration that will use a __Traverse-Hosted__ suppression list, follow the steps in the ["Creating a List"](#creating-a-list) section below twice (once each for the subscription and suppression lists). Publishers using a __Subscription Lookup__ integration will follow the directions for building out a [Suppression Lookup Endpoint](#suppression-lookup-endpoint), and then continue on to [create a list](#creating-a-list)

#### Suppression Lookup Endpoint

A subscription lookup endpoint should allow Traverse to provide an email hash in an HTTP request, and should respond with the subscription status of the corresponding recipient. When Traverse wants to dispatch a message to a potential recipient, we use this endpoint to look up their details, and then dispatch the message.

The HTTP endpoint should allow for an email hash to be provided like so. Ideally, the endpoint could look up an email based on either SHA1 or MD5 hash.

`https://www.publisher.com/subscription/lookup?md5={md5-hashed-email}`

OR

`https://www.publisher.com/subscription/lookup?sha1={sha1-hashed-email}`

This endpoint should return at minimum the plaintext email address, first name, which lists they're currently mailable under, if any, and the CAN-SPAM information (IP, timestamp) for each. The response should be in JSON, and consist of an Array of Objects, each describing a subscription:

```
[
  { // First subscription
    email: 'foo@bar.com',
    firstName: 'John',
    listId: 'lifescript_list_id_1',
    ip: '1.2.3.4'
    timestamp: '2017-07-07T21:27:02+00:00' // ISO-8601 is great.
  },
  { // Another subscription
    email: 'foo@bar.com',
    firstName: 'Jonathan', // Maybe they provided a different name for this list.
    listId: 'lifescript_list_id_2',
    ip: '2.3.4.5',
    timestamp: '2015-03-02T12:18:01+00:00'
  }
]
```
If the recipient is not subscribed to any list, or if the publisher doesn't have a match for the hash:

```
[] // (No subscriptions)
```


### Creating a List

*Creating subscriber lists programmatically is not yet supported.*

In the meantime, please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">contact us</a> and we will provide you a subscriber-list ID.


### Adding Subscribers (Batch)

The fastest way to get your subscribers to Traverse is by uploading them in bulk, either via Amazon S3 or SFTP. The file should be in CSV or TSV format. While S3 is preferred, SFTP access is provided by request. Please <a href="mailto:Traverse Operations <operations@traversedlp.com&gt">reach out</a> when your subscribers are ready to be imported.

### Adding Subscribers (Individual)

To add a subscriber to a list, use the following endpoint:

```
POST https://retargeting.traversedlp.com/v1/lists/{YOUR-SUBSCRIBER-LIST-ID-HERE}/recipients
```

The message body should be an array containing a single JSON object with the following properties:

| Property | Value |
|------|-------|
| `emailMd5Lower` | MD5 hash of trimmed, lowercased email address |
| `emailSha1Lower` | SHA-1 hash of trimmed, lowercased email address |

For example:

```javascript
[{
  emailMd5Lower: "1105677c8d9decfa1e36a73ff5fb5531",
  emailSha1Lower: "ba9d46a037766855efca2730031bfc5db095c654"
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

## Publisher Header and Footer

Publisher provides a header/footer which will contain dynamic advertiser content. Travese needs:

- html creative, all images
- unsubscribe link
- physical address

<br />

## Best practices

While we make every attempt to maintain the availability of our system, unexpected errors may occur:

 1. Always check whether the HTTP status code  is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#2xx_Success">2xx</a>* (success) before proceeding, and abort if not.
 2. If the status code is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#4xx_Client_Error">4xx</a>* (client error), your request is likely invalid. Review the documentation before retrying.
 3. If the status code is *<a href="https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#5xx_Server_Error">5xx</a>* (server error), there is likely a problem on our end. Please retry after 60 seconds.
 4. Otherwise, treat any unexpected response, status code, timeout or exception as a *5xx*.
 5. Log your requests and replies, and monitor for and review any failures.
