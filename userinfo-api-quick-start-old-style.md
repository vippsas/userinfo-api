<!-- START_METADATA
---
title: Quick start for the eCom API
sidebar_label: Quick start
sidebar_position: 10
description: Quick start guide for the using the eCom API with Postman.
pagination_next: null
pagination_prev: null
---
END_METADATA -->

# Quick start

<!-- START_COMMENT -->
💥 Please use the documentation pages here: <https://developer.vippsmobilepay.com/docs/APIs/ecom-api>. 💥
<!-- END_COMMENT -->

This is a quick start guide for the using the eCom API with Postman.

Use the eCom API to initiate payments, get payment details, capture payments, and refund payments.
You can also perform express checkout payments and get access to user info.

## Postman

See
[Quick start guides](https://developer.vippsmobilepay.com/docs/vipps-developers/quick-start-guides)
and
[The Vipps Test Environment (MT)](https://developer.vippsmobilepay.com/docs/vipps-developers/test-environment).

### Import Postman files and set up the environment

[Import](https://learning.postman.com/docs/getting-started/importing-and-exporting-data/)
the Postman files:

* [eCom API Postman collection](/tools/vipps-ecom-api-postman-collection.json)
* [API Global Postman environment](https://raw.githubusercontent.com/vippsas/vipps-developers/master/tools/vipps-api-global-postman-environment.json)

[Specify the variables](https://learning.postman.com/docs/sending-requests/managing-environments/#editing-environment-variables)
required for using the eCom API
(see [Get credentials](https://developer.vippsmobilepay.com/docs/vipps-developers/common-topics/api-keys)):

* `client_id` - The "username" for making API requests
* `client_secret` - The "password" for making API requests
* `Ocp-Apim-Subscription-Key` - The subscription key for making API requests
* `merchantSerialNumber` - The unique ID for your sales unit.
* `mobileNumber` - The phone number for your
   [test user](https://developer.vippsmobilepay.com/docs/vipps-developers/test-environment#test-users).

## Make API requests

For all API requests you must first
[Get an access token](https://developer.vippsmobilepay.com/docs/APIs/access-token-api#get-an-access-token):
Send request `Get Access Token`.

### Userinfo

1. Send request `Initiate Payment - Profile flow`.
2. Open the landing page URL from the response in a browser and specify your phone number.
3. Confirm the payment in Vipps.
4. Send request `Get Payment Details`.
   The response contains the user identifier: `sub`.
5. Send request `Get Userinfo`, which uses the `sub` from the previous call.

See
[Userinfo](vipps-ecom-api.md#userinfo)
for more information.

