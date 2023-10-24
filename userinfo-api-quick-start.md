---
title: Userinfo Quick start
sidebar_label: Quick start
id: quick-start
sidebar_position: 10
description: Quick steps for getting started with the Userinfo API.
toc_min_heading_level: 2
toc_max_heading_level: 5
pagination_next: null
pagination_prev: null
---

import ApiSchema from '@theme/ApiSchema';
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Quick start

This example is for the [payment flow](userinfo-api-guide.md#userinfo-through-payment-apis)
using the ePayment API, but it can be applied to the Recurring and eCom APIs.

To see a quick start for the login flow, see the
[Login API Quick start guide](https://developer.vippsmobilepay.com/docs/APIs/login-api/vipps-login-api-quick-start/).

## Before you begin

The provided example values in this guide must be changed with the values for your sales unit and user.
This applies for API keys, HTTP headers, reference, phone number, etc.
Note that any currency amount must be an Integer value minimum 100 in øre.

## Your first user info

### Step 1 - Setup

You must have already signed up as an organization with Vipps MobilePay and have
your test credentials from the merchant portal.

You will need the following values, as described in the
[Getting started guide](https://developer.vippsmobilepay.com/docs/getting-started):

* `client_id` - Client_id for a test sales unit.
* `client_secret` - Client_id for a test sales unit.
* `Ocp-Apim-Subscription-Key` - Subscription key for a test sales unit.
* `merchantSerialNumber` - The unique ID for a test sales unit.
* `internationalMobileNumber` - The MSISDN for the test app profile you have received or registered. This is your test mobile number *including* country code.

<Tabs
defaultValue="curl"
groupId="sdk-choice"
values={[
{label: 'curl', value: 'curl'},
{label: 'Postman', value: 'postman'},
]}>
<TabItem value="postman">

In Postman, import the following files:

* [Userinfo API Postman collection](/tools/userinfo-api-postman-collection.json)
* [Global Postman environment](https://github.com/vippsas/vipps-developers/blob/master/tools/vipps-api-global-postman-environment.json)

🔥 **To reduce risk of exposure, never store production keys in Postman or any similar tools.** 🔥

Update the *Current Value* field in your Postman environment with your **Merchant Test** keys.
Use *Current Value* field for added security, as these values are not synced to the cloud.

</TabItem>
<TabItem value="curl">

No additional setup needed :)

</TabItem>
</Tabs>

### Step 2 - Authentication

Get an `access_token` from the
[Access token API](https://developer.vippsmobilepay.com/docs/APIs/access-token-api):
[`POST:/accesstoken/get`][access-token-endpoint].
This provides you with access to the API.

<Tabs
defaultValue="curl"
groupId="sdk-choice"
values={[
{label: 'curl', value: 'curl'},
{label: 'Postman', value: 'postman'},
]}>
<TabItem value="postman">

```bash
Send request Get Access Token
```

</TabItem>
<TabItem value="curl">

```bash
curl https://apitest.vipps.no/accessToken/get \
-H "client_id: YOUR-CLIENT-ID" \
-H "client_secret: YOUR-CLIENT-SECRET" \
-H "Ocp-Apim-Subscription-Key: YOUR-SUBSCRIPTION-KEY" \
-H "Merchant-Serial-Number: YOUR-MSN" \
-H "Vipps-System-Name: acme" \
-H "Vipps-System-Version: 3.1.2" \
-H "Vipps-System-Plugin-Name: acme-webshop" \
-H "Vipps-System-Plugin-Version: 4.5.6" \
-X POST \
--data ''
```

</TabItem>
</Tabs>

The property `access_token` should be used for all other API requests in the `Authorization` header as the Bearer token.

### Step 3 - Request a simple payment with profile flow

Provide the `scope` object in the [`POST:/epayment/v1/payments`][create-payment-endpoint] call. This contains the information types that you want access to, separated by spaces (e.g., "name address email phoneNumber birthDate").

<Tabs
defaultValue="curl"
groupId="sdk-choice"
values={[
{label: 'curl', value: 'curl'},
{label: 'Postman', value: 'postman'},
]}>
<TabItem value="postman">

```bash
Send request Create Payment
```

</TabItem>
<TabItem value="curl">

```bash
curl https://apitest.vipps.no/epayment/v1/payments \
-H "Content-Type: application/json" \
-H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>" \
-H "Ocp-Apim-Subscription-Key: 0f14ebcab0ec4b29ae0cb90d91b4a84a" \
-H "Merchant-Serial-Number: YOUR-MSN" \
-H "Vipps-System-Name: acme" \
-H "Vipps-System-Version: 3.1.2" \
-H "Vipps-System-Plugin-Name: acme-webshop" \
-H "Vipps-System-Plugin-Version: 4.5.6" \
-H "Idempotency-Key: 49ca711a-acee-4d01-993b-9487112e1def" \
-X POST \
-d '{
    "amount": {
        "value": 49900,
        "currency": "NOK"
    },
    "paymentMethod": {
        "type": "WALLET"
    },
    "customer": {
        "phoneNumber": 4791234567
    },
    "reference": UNIQUE-PAYMENT-REFERENCE,
    "userFlow": "WEB_REDIRECT",
    "returnUrl": "https://example.com/redirect?reference=UNIQUE-PAYMENT-REFERENCE",
    "paymentDescription": "Purchase of socks",
    "profile": {
        "scope": "name phoneNumber address birthDate"
    }
}'
```

</TabItem>
</Tabs>

### Step 4 - Complete the payment

Open the `redirectUrl` link that is returned, and it will take you to the
[landing page](https://developer.vippsmobilepay.com/docs/common-topics/landing-page/).
The phone number of your test user should already be filled in, so you only have to click *Next*.

You will be presented with the payment in the app, where you can complete the payment.

### Step 5 - Get the sub for the payment

Call the [`GET:/epayment/v1/payments/{reference}`][get-payment-endpoint] endpoint.
The unique identifier, `sub`, can be retrieved in the payment details under `profile`.


<Tabs
defaultValue="curl"
groupId="sdk-choice"
values={[
{label: 'curl', value: 'curl'},
{label: 'Postman', value: 'postman'},
]}>
<TabItem value="postman">

```bash
Send request Get payment details
```

</TabItem>
<TabItem value="curl">

```bash
curl https://apitest.vipps.no/epayment/v1/payments/UNIQUE-PAYMENT-REFERENCE \
-H "Content-Type: application/json" \
-H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>" \
-H "Ocp-Apim-Subscription-Key: 0f14ebcab0ec4b29ae0cb90d91b4a84a" \
-H "Merchant-Serial-Number: YOUR-MSN" \
-H "Vipps-System-Name: acme" \
-H "Vipps-System-Version: 3.1.2" \
-H "Vipps-System-Plugin-Name: acme-webshop" \
-H "Vipps-System-Plugin-Version: 4.5.6" \
-X GET
```

</TabItem>
</Tabs>

### Step 6 - Get the user info

Send request [`GET:/vipps-userinfo-api/userinfo/{sub}`][userinfo-endpoint] with the `sub` variable from the previous call.

<Tabs
defaultValue="curl"
groupId="sdk-choice"
values={[
{label: 'curl', value: 'curl'},
{label: 'Postman', value: 'postman'},
]}>
<TabItem value="postman">

```bash
Send request Get Userinfo
```

</TabItem>
<TabItem value="curl">

```bash
curl https://apitest.vipps.no/vipps-userinfo-api/userinfo/{sub} \
-H "Content-Type: application/json" \
-H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>" \
-H "Ocp-Apim-Subscription-Key: 0f14ebcab0ec4b29ae0cb90d91b4a84a" \
-H "Merchant-Serial-Number: YOUR-MSN" \
-H "Vipps-System-Name: acme" \
-H "Vipps-System-Version: 3.1.2" \
-H "Vipps-System-Plugin-Name: acme-webshop" \
-H "Vipps-System-Plugin-Version: 4.5.6" \
-H "Idempotency-Key: 49ca711a-acee-4d01-993b-9487112e1def" \
-X GET
```

</TabItem>
</Tabs>


<details>
<summary>Example response</summary>
<div>

```json
{
    "address": {
        "address_type": "home",
        "country": "NO",
        "formatted": "Robert Levins gate 5, 0154\nOSLO\nNO",
        "postal_code": "0154",
        "region": "OSLO",
        "street_address": "Robert Levins gate 5"
    },
    "birthdate": "2000-02-01",
    "family_name": "User",
    "given_name": "Test",
    "name": "Test User",
    "other_addresses": [],
    "phone_number": "4791234567",
    "sid": "687a31ad9ba1de74",
    "sub": "9fe5d0e3-4702-4113-a154-90bc68063325"
}
```

</div>
</details>


## Next steps

To learn more about this API, visit the [Userinfo API guide](./README.md).



[access-token-endpoint]: https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost
[create-payment-endpoint]: https://developer.vippsmobilepay.com/api/epayment#tag/CreatePayments/operation/createPayment
[get-payment-endpoint]: https://developer.vippsmobilepay.com/api/epayment#tag/QueryPayments/operation/getPayment
[userinfo-endpoint]: https://developer.vippsmobilepay.com/api/userinfo#operation/getUserinfo
