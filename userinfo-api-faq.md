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

## If a user removes their consents, is the `sub` still the same?

Yes, the `sub` provided will be the same when the user logs in again and re-consents.

## If a user deletes their Vipps Profile and create a new one, is the `sub` still the same?

No, upon deletion of the Vipps Profile the `sub` is also removed.

## If a user changes phone number, is the `sub` still the same?

No, in order to sign up with a new phone number a user will have to delete the
Vipps profile on the old phone number. When this Vipps profile is deleted, the
`sub` is also removed.

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

## How can we detect users' consent removal?

Or: *How can our system dynamically "know/find out" if the user has revoked the consent
for us to have access to his/her personal data in our system?*

Your system can dynamically detect when a user's consent has been revoked by using *consent webhooks*.
This is a system for notifying merchants when an end user revokes their consent.
See the
[Login API guide: Consent webhooks](https://developer.vippsmobilepay.com/docs/APIs/login-api/api-guide/important-information#revoke-consent-webhook)
section for more information.
