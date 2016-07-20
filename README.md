Traverse Email Retargeting publisher documentation
--------------------------------------------------

Contents
--------

  * [Overview](#overview)
  * [Getting started](#getting-started)
  * [Syncing your suppression list(s)](#syncing-your-suppression-lists)
  * [Syncing your subscribers](#syncing-your-subscribers)
  * [Setting up your template(s)](#setting-up-your-templates)
  * [Example](#example)
  * [Best practices](#best-practices)

Overview
--------

Traverse Email Retargeting allows marketers to send publisher-branded email advertisements to your subscribers.

Getting started
---------------

To get started with Traverse Email Retargeting:

 1. [Sync your suppression list(s)](#syncing-your-suppression-lists).
 2. [Sync your subscribers](#syncing-your-subscribers).
 3. [Set up your email template(s)](#setting-up-your-templates).

Syncing your suppression list(s)
--------------------------------

In order to sync a suppression list:

 1. [Create a suppression list](#creating-a-suppression-list).
 2. [Upload its contents](#updating-a-suppression-list).
 3. Automate repeating step 2 at least once per week.

Creating a suppression list
---------------------------

To create a suppression list, upload a [representation](#suppression-list-representation) without an `id` to:
```
POST https://retargeting.traversedlp.com/v1/blacklist
```

The response will be an updated [representation](#suppression-list-representation) including an `id`.

Notes:

 1. Do not include the `id` field. The system will assign one for you.
 2. This just creates an empty list. You still need to [upload its contents](#updating-a-suppression-list).
 3. Record the returned `id` so you can update the list.

Suppression list representation
-------------------------------

Suppression lists are represented by a JSON object with the following name/value pairs:

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

Updating a suppression list's recipients
----------------------------------------

To update a suppression list's recipients, upload a CSV:
```
PUT https://retargeting.traversedlp.com/v1/blacklist/{id}/hashes
```

The CSV should look as follows:

 1. Please format per <a href="https://tools.ietf.org/html/rfc4180">RFC 4180</a>.
 2. A header row, consisting of the column names, is required.
 3. At least one of the columns must be named `emailMdLower` or `emailSha1Lower`.

The CSV must contain the following columns: 

| Name          | Value                                | Required |
| ------------- |--------------------------------------|----------|
| `id`          | Traverse-generated unique identifier | No       |
| `name`        | Client-supplied name                 | No       |
| `description` | Client-supplied description          | No       |





`Subscriber` object
-------------------

In order to sync a suppression list, you first have to create a `blacklist` object.

Create a subscriber list by sending an HTTP POST to the following URL:

```
https://retargeting.traversedlp.com/v1/subscribers
```




Parameters:



Create a suppression list 


Syncing your subscribers
-------------------------------

At least once per week, send us your subscriber list via HTTP POST to:

```
https://retargeting.traversedlp.com/v1/subscribed
```

`foo`

Setting up your template(s)
---------------------------

The advertising emails 

To use Traverse in email, include our [Pixel Block](#pixel-block) in your messages.

Please see the [example](#example) and [best practices](#best-practices), below.

Pixel Block
-----------

The Pixel Block is a series of generic pixels that redirect to specific pixels when loaded. This way you won't need to update your integration as new partners are added.

Each pixel has the following form:
 
>\<img border="0" width="1" height="1" src="http://`domain`/v1/`clientId`/`index`.gif?`hashType`=`hashValue`"/\>

Hashes
------

Our match partners expect compute hashes using varying schemes. Most want the email address converted to lowercase before hashing; however, some want it hashed using MD5 whereas others want SHA-1.

The following `hashType`s are supported:

| Parameter    | Description | *Recommended* |
| ------------ |------------ | ------------- |
| `emailMd5Lower` | MD5 hash of lowercase email address. | Yes |
| `emailSha1Lower` | SHA1 hash of lowercase email address. | Yes |
| `emailMd5Upper` | MD5 hash of *uppercase* email address. | No |
| `emailSha1Upper` | SHA1 hash of *uppercase* email address. | No |

For example, if the email address is `foo@BAR.com`, then `emailMd5Upper` would be `6d881a14e8fb4886b2742f0b4aa10d30`.

To accommodate the greatest number of partners, you would include pixels for all four `identifiers`. However, at minimum, you should include pixels for both `emailMd5Lower` and `emailSha1Lower`.

Domain
------

Our pixels are hosted at `email.traversedlp.com`. However, in order to protect your deliverability, you should serve them from a domain you control, via a [CNAME record](https://en.wikipedia.org/wiki/CNAME_record).

For example, if your sending domain is `example.com`, you could create a CNAME record for `traverse.example.com` that resolves to `email.traversedlp.com`, and then use that subdomain in the [Pixel Block](#pixel-block).

Example
-------

Suppose you control `example.com`, have set up a [CNAME record](#domain) pointing `traverse.example.com` to `email.traversedlp.com`, and you're sending an email to `foo@BAR.com`.

Include at least 5 pixels with `emailMd5Lower`, and at least 5 more with `emailSha1Lower`, like so (*notice the changing `index`*):

```
<img border="0" width="1" height="1" src="http://traverse.example.com/v1/YOUR-CLIENT-ID-HERE/0.gif?emailMd5Lower=f3ada405ce890b6f8204094deb12d8a8"/>
<img border="0" width="1" height="1" src="http://traverse.example.com/v1/YOUR-CLIENT-ID-HERE/1.gif?emailMd5Lower=f3ada405ce890b6f8204094deb12d8a8"/>
<img border="0" width="1" height="1" src="http://traverse.example.com/v1/YOUR-CLIENT-ID-HERE/2.gif?emailMd5Lower=f3ada405ce890b6f8204094deb12d8a8"/>
<img border="0" width="1" height="1" src="http://traverse.example.com/v1/YOUR-CLIENT-ID-HERE/3.gif?emailMd5Lower=f3ada405ce890b6f8204094deb12d8a8"/>
<img border="0" width="1" height="1" src="http://traverse.example.com/v1/YOUR-CLIENT-ID-HERE/4.gif?emailMd5Lower=f3ada405ce890b6f8204094deb12d8a8"/>
<img border="0" width="1" height="1" src="http://traverse.example.com/v1/YOUR-CLIENT-ID-HERE/0.gif?emailSha1Lower=823776525776c8f23a87176c59d25759da7a52c4"/>
<img border="0" width="1" height="1" src="http://traverse.example.com/v1/YOUR-CLIENT-ID-HERE/1.gif?emailSha1Lower=823776525776c8f23a87176c59d25759da7a52c4"/>
<img border="0" width="1" height="1" src="http://traverse.example.com/v1/YOUR-CLIENT-ID-HERE/2.gif?emailSha1Lower=823776525776c8f23a87176c59d25759da7a52c4"/>
<img border="0" width="1" height="1" src="http://traverse.example.com/v1/YOUR-CLIENT-ID-HERE/3.gif?emailSha1Lower=823776525776c8f23a87176c59d25759da7a52c4"/>
<img border="0" width="1" height="1" src="http://traverse.example.com/v1/YOUR-CLIENT-ID-HERE/4.gif?emailSha1Lower=823776525776c8f23a87176c59d25759da7a52c4"/>
```

Best practices
--------------

1. Always serve the pixels from [a domain you control](#domain).
2. Always include at least 5 pixels with `emailMd5Lower`, and at least 5 more with `emailSha1Lower`.
3. Only include the pixels in HTML emails.
