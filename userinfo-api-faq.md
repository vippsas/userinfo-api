<!-- START_METADATA
---
title: Userinfo API frequently asked questions
sidebar_label: FAQ
sidebar_position: 50
description: Userinfo API frequently asked questions
pagination_next: null
pagination_prev: null
---
END_METADATA -->

# Frequently asked questions

Here are the Userinfo API Frequently Asked Questions (FAQ).
See the [Vipps Userinfo API](README.md) for all the technical details.

For more common Vipps questions, see:

* [Vipps API General FAQ](https://developer.vippsmobilepay.com/docs/vipps-developers/faqs)


<!-- START_COMMENT -->

ℹ️ Please use the website:
[Vipps MobilePay Technical Documentation](https://developer.vippsmobilepay.com/docs/APIs/userinfo-api).

<!-- END_COMMENT -->

## What is the `sub`?

The `sub` is a unique user identifier for a Vipps user, related to that user's consent
to share information with a specific MSN (sales unit).

The `sub` is _based on_ the user's national identity number ("fødselsnummer" in Norway),
but  is not a replacement for NIN (National Identity Number) or any other unique identifier
for the user. 

The `sub` is unique for each MSN (sales unit).
User may have many different `sub`s for the same merchant: One for each of
the merchant's MSNs (sales units).
You can not use the `sub` for one MSN with the API keys for a different MSN.

The `sub` will not change if a user removes their consents, and logs in again and re-consents.

**Please note:** There are some special cases where the `sub` will change for a user:
- If a user deletes the Vipps Profile and creates a new one.
- If a user changes the phone number (in practice: Creates a new Vipps user)

## Why can I get userinfo after the user has revoked consent?

During a login or a payment session the user consent to share information if
it's requested by the merchant. The users information is then available for
the merchant from the userinfo endpoint. For login sessions, user information
is available for the ongoing login session.

To better support merchants that
do not handle online fetching and processing of the user info as part of a
payment session, we keep this information accessible for the merchant for the
next 168 hours, even though the user revokes the consent in this period.
Revoking consents will immediately affect future login and payment sessions.

## How can I get updated information, like addresses, for a user?

When the user consents to sharing information with the merchant, the merchant
has 168 hours to retrieve the information 
The merchant must save this information and handle everything according to GDPR.  

If the merchant needs an updated address for the user, the merchant must ask the
user for a new consent. 

With the
[Vipps Login API](https://developer.vippsmobilepay.com/docs/APIs/login-api/)
the merchant can retrieve updated infomation every time the user logs in.

## How can we detect users' consent removal?

Or: *How can our system dynamically "know/find out" if the user has revoked the consent
for us to have access to his/her personal data in our system?*

Your system can dynamically detect when a user's consent has been revoked by using *consent webhooks*.
This is a system for notifying merchants when an end user revokes their consent.

For the Login API, see
[Login API guide: Consent webhooks](https://developer.vippsmobilepay.com/docs/APIs/login-api/api-guide/important-information#revoke-consent-webhook).
