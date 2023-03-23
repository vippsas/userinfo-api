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

* [Vipps API General FAQ](https://vippsas.github.io/vipps-developer-docs/docs/vipps-developers/faqs)


<!-- START_COMMENT -->

ℹ️ Please use the website:
[Vipps MobilePay Technical Documentation](https://vippsas.github.io/vipps-developer-docs/docs/APIs/userinfo-api).

<!-- END_COMMENT -->

## Which scopes can I use? Why do I get `Invalid_scope`?

If you get “Invalid_scope” this means that you have included one or more scopes
that you do not have access to or that is not supported.
See:
[Scopes](api-guide/core-concepts.md#scopes).

All merchants have access to these scopes:

* openid
* address
* birthdate
* email
* name
* phoneNumber

**Important:** You should never ask for more scopes than you need for your
application. The user will need to consent to sharing the information with you
so adding more scopes increases the chance that they will decline.

The scope `openid` is required and does not require user consent. It
provides the claim `sub` which is a unique id for the end user at that
particular merchant.

*Please note:** Different sales units will get different `sub`s for the
same end user.

Some merchants can get access to national identity number (NIN) with the `nin`
scope. Merchants need to request this separately. See
[Who can get access to NIN and how?](#who-can-get-access-to-nin-and-how).

You can find the list of scopes that your individual sales units have access to in
[portal.vipps.no](https://portal.vipps.no)
under the "Utvikler" section and the "Setup Vipps Login" panel.

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

## Who can get access to NIN and how?

Access to the `nin` scope and the National Identity Number (NIN) is a paid service.
Merchants will be billed for each successful login where they request a user's NIN.

Only merchants with legal requirements or other objective needs to use the Norwegian
National Identity Number (NIN) to achieve required user identification can get
access to NIN. We follow the
[guidance from Datatilsynet](https://www.datatilsynet.no/rettigheter-og-plikter/personopplysninger/fodselsnummer/).

According to the above NIN can only be used when it is legally required or when
there is a need for a secure identification of the person and the NIN is
required to achieve this. This applies e.g. when a company is required to
report to the tax authorities or within healthcare and credit.

Keeping a unique and consistent identifier for the user over time is not a
sufficient requirement. For this purpose Vipps offers a unique merchant specific
user identifier that are delivered as part of the registration/login. This is
the claim `sub` that is delivered based on the `openid` scope. This unique
identifier will allow you to keep a consistent user profile even if the user
changes contact information.

Beware that Vipps Login it not an electronic ID. Thus, the NIN can only be
used to simplify the customers processes by removing manual input or to look up
the customer in your own or external registers. This can be done as part of the
processes to become a customer or to link login with Vipps to an existing user.
If you need to store the NIN for new users we recommend that you use an
electronic ID.

Merchants need to apply for access to NIN by sending an email to
[accessuserinfo@vipps.no](mailto:accessuserinfo@vipps.no).
In the email you must specify:

* Organization number
* Merchant name
* Name and number of the sales unit from [portal.vipps.no](https://portal.vipps.no)
* Information on how you plan to use the NIN
* The legal requirement and/or the reason why you need to use NIN to achieve
  required user identification.

## How is GDPR handled with Vipps Login?

With regard to the processing of personal data and GDPR, the following applies
to Vipps Login:

1. Vipps Login gives a merchant the opportunity to ask a Vipps end user to share
   a selection of data from their profile in Vipps. This can include name, phone
   number, email, addresses, and date of birth. The merchant controls which of
   this data is requested. The user must consent to the sharing of data.
   The consent is the legal basis for the Vipps AS transfer of
   this information to the merchant.
2. Vipps AS is responsible for our processing of information related to Vipps
   end users and the personal information generated using the Vipps services.
   For the Vipps Login service, the merchant will be responsible for the
   processing of the profile information received, starting when the merchant
   receives the profile information from Vipps end user. The merchant will thus
   be an independent data processor for this data, and there is no need for a
   data processing agreement between Vipps and the merchant.
3. The merchant must therefore obtain a valid basis for further processing of
   the personal data (e.g agreement, terms or consent), to e.g. register the
   information in its customer register and start customer processing from there.
4. When such sharing from Vipps AS to the merchant has been made, a Vipps end
   user can later use Vipps to log in to the merchant, and the merchant will
   then have access to updated information on the data elements that the company
   has requested. A Vipps end user can go into Vipps (the app) and see which
   companies they have shared data with, which data has been shared and they
   can withdraw their consent to share. This means that new consent must be
   obtained before Vipps AS can share data again with the merchant.
5. When an end user uses Vipps Login at a merchant, Vipps AS stores, as part
   of our service to the Vipps end user and with Vipps AS as data processor,
   information about a) what information a user has agreed to share with a
   merchant and b) when a user has used Vipps log in to the relevant merchant.
6. Vipps AS does not receive any information from the merchant about Vipps end user.

See more information in our end user
[terms and condition](https://www.vipps.no/vilkar/vilkar-privat/),
[privacy policy](https://www.vipps.no/vilkar/cookie-og-personvern/)
and
[merchant agreement](https://www.vipps.no/vilkar/vilkar-bedrift/).


## How can we detect users' consent removal?

Or: *How can our system dynamically "know/find out" if the user has revoked the consent
for us to have access to his/her personal data in our system?*

Your system can dynamically detect when a user's consent has been revoked by using *consent webhooks*.
This is a system for notifying merchants when an end user revokes their consent.
See the
[Consent webhooks](api-guide/important-information.md#revoke-consent-webhook) section for more information.
