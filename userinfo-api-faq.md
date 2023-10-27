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

For general information and questions, please check in the
[Knowledge base](https://developer.vippsmobilepay.com/docs/common-topics/).

## What is the `sub`?

See [API Guide: Sub](userinfo-api-guide.md#sub).

## Why can I get userinfo after the user has revoked consent?

During a log-in or a payment session, the user can consent to share information if
it's requested by the merchant. The user's information is then available for
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
They must then save this information and handle everything according to GDPR.

If the merchant needs an updated address for the user, they must ask the
user for a new consent.

With the
[Login API](https://developer.vippsmobilepay.com/docs/APIs/login-api/),
the merchant can retrieve updated information every time the user logs in.

## How can we detect users' consent removal?

Or: *How can our system dynamically "know/find out" if the user has revoked the consent
for us to have access to his/her personal data in our system?*

Your system can dynamically detect when a user's consent has been revoked by using the
[Login API's Consent webhooks](https://developer.vippsmobilepay.com/docs/APIs/login-api/api-guide/important-information#revoke-consent-webhook).
