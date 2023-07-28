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

## Before you begin

This document covers the quick steps for getting started with the Userinfo API.
You must have already signed up as an organization with Vipps MobilePay and have
your test credentials from the merchant portal, as described in the
[Getting started guide](https://developer.vippsmobilepay.com/docs/vipps-developers/getting-started).

**Important:** The examples use standard example values that you must change to
use *your* values. This includes API keys, HTTP headers, reference, etc.

## Userinfo with ePayment API

This example is for ePayment, but can be applied to the Recurring and eCom APIs.

### Step 1 - Setup for ePayment API

<Tabs
defaultValue="curl"
groupId="sdk-choice"
values={[
{label: 'curl', value: 'curl'},
{label: 'Postman', value: 'postman'},
]}>
<TabItem value="postman">

Import the following files into Postman:

* [Userinfo API Postman collection](/tools/userinfo-api-postman-collection.json)
* [Global Postman environment](https://github.com/vippsas/vipps-developers/blob/master/tools/vipps-api-global-postman-environment.json)

In Postman, tweak the environment with your own values (see
[API keys](https://developer.vippsmobilepay.com/docs/vipps-developers/common-topics/api-keys/)):

* `client_id` - Merchant key required for getting the access token.
* `client_secret` - Merchant key required for getting the access token.
* `Ocp-Apim-Subscription-Key` - Merchant subscription key.
* `merchantSerialNumber` - Merchant ID.
* `internationalMobileNumber` - The MSISDN for the test app profile you have received or registered. This is your test mobile number *including* country code.
* `mobileNumber` - (For Recurring, eCom, and Login API) The MSISDN for the test app profile you have received or registered. This is your test mobile number *including* country code.

For help using Postman, see
[Quick start guides](https://developer.vippsmobilepay.com/docs/vipps-developers/quick-start-guides).

</TabItem>
<TabItem value="curl">

No setup needed :)

</TabItem>
</Tabs>

### Step 2 - Authentication for ePayment API

Get an `access_token` from the
[Access token API](https://developer.vippsmobilepay.com/docs/APIs/access-token-api):
[`POST:/accesstoken/get`][access-token-endpoint].
This provides you with access to the API.

<Tabs
defaultValue="postman"
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
-H "Merchant-Serial-Number: 123456" \
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

### Step 3 - A simple payment with profile flow

Provide the `scope` object in the [`POST:/payments`][create-payment-endpoint] call. This contains the information types that you want access to, separated by spaces (e.g., "name address email phoneNumber birthDate").

<Tabs
defaultValue="postman"
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
-H "Merchant-Serial-Number: 123456" \
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
    "reference": 2486791690535039148,
    "userFlow": "WEB_REDIRECT",
    "returnUrl": "https://example.com/redirect?reference=2486791690535039148",
    "paymentDescription": "Purchase of socks",
    "profile": {
        "scope": "name phoneNumber address birthDate"
    }
}'
```

</TabItem>
</Tabs>

### Step 4 - Complete the payment

Open the link that appears, and it will take you to a landing page where you can enter your phone number.
Open the Vipps app and complete the payment.

### Step 5 - Get the sub for the payment

The unique identifier `sub` can be retrieved in the payment details
when calling the
[`GET:/payments/{reference}`][get-payment-endpoint] endpoint.

<Tabs
defaultValue="postman"
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
-H "Merchant-Serial-Number: 123456" \
-H "Vipps-System-Name: acme" \
-H "Vipps-System-Version: 3.1.2" \
-H "Vipps-System-Plugin-Name: acme-webshop" \
-H "Vipps-System-Plugin-Version: 4.5.6" \
-H "Idempotency-Key: 49ca711a-acee-4d01-993b-9487112e1def" \
-X GET
```

</TabItem>
</Tabs>

### Step 6 - Get the user info

Send request `Get Userinfo`.
This uses [`GET:/vipps-userinfo-api/userinfo/{sub}`][userinfo-endpoint] with the `sub` variable from the previous call.

<Tabs
defaultValue="postman"
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
curl https://apitest.vipps.no/vipps-userinfo-api/userinfo/{sub} \
-H "Content-Type: application/json" \
-H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>" \
-H "Ocp-Apim-Subscription-Key: 0f14ebcab0ec4b29ae0cb90d91b4a84a" \
-H "Merchant-Serial-Number: 123456" \
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
        "formatted": "BOKS 6300, ETTERSTAD\n0603\nOSLO\nNO",
        "postal_code": "0603",
        "region": "OSLO",
        "street_address": "BOKS 6300, ETTERSTAD"
    },
    "birthdate": "1955-02-01",
    "family_name": "User",
    "given_name": "Test",
    "name": "Test User",
    "other_addresses": [],
    "phone_number": "4791234568",
    "sid": "687a31ad9ba1de74",
    "sub": "9fe5d0e3-4702-4113-a154-90bc68063325"
}
```


</div>
</details>

## Userinfo with Login API

### Step 1 - Setup for Login API

Same as [Setup for ePayment API](#step-1---setup-for-epayment-api) with addition of `redirect_uri`.

The `redirect_uri` must be exactly the same as the one specified your sales unit.
See [How to set up login on your sales unit](https://developer.vippsmobilepay.com/docs/vipps-developers/developer-resources/portal#how-to-setup-login-on-your-sales-unit).

### Step 2 - Get OIDC Well known

<Tabs
defaultValue="postman"
groupId="sdk-choice"
values={[
{label: 'curl', value: 'curl'},
{label: 'Postman', value: 'postman'},
]}>
<TabItem value="postman">

```bash
Send Get OIDC well-known
```

</TabItem>
<TabItem value="curl">

```bash
curl https://apitest.vipps.no/access-management-1.0/access/.well-known/openid-configuration \
-H "Merchant-Serial-Number: 123456" \
-H "Vipps-System-Name: acme" \
-H "Vipps-System-Version: 3.1.2" \
-H "Vipps-System-Plugin-Name: acme-webshop" \
-H "Vipps-System-Plugin-Version: 4.5.6" \
-X GET
```

</TabItem>
</Tabs>


### Step 3 - Log in

Run the URI that requests log-in and access to user information.

<Tabs
defaultValue="postman"
groupId="sdk-choice"
values={[
{label: 'curl', value: 'curl'},
{label: 'Postman', value: 'postman'},
]}>
<TabItem value="postman">

In your active Postman environment, copy the value of key `start_login_uri` and paste it into the address field of any browser.


</TabItem>
<TabItem value="curl">

Put together the URI in this format ([OAuth 2.0 Authorize](https://developer.vippsmobilepay.com/docs/APIs/login-api/api-guide/integration/#oauth-20-authorize)):

```http
https://apitest.vipps.no/access-management-1.0/access/oauth2/auth?client_id=YOUR-CLIENT-ID&response_type=code&scope=openid%20name%20phoneNumber%20address%20birthDate&state=8652682f-ba1d-4719-b1ec-8694ba97bde7&redirect_uri=http://localhost
```
Paste the URL into the address field of any browser.

</TabItem>
</Tabs>

Finish the login.
If you have not yet consented to sharing your user information, a new screen will be presented in the app requesting your consent.

If you have already completed this process and selected *Remember me in browser* earlier, this will take you straight to the redirect URL.

### Step 4 Get token

On the redirect URL page, copy the `code` value out from the address field in the URL.

<Tabs
defaultValue="postman"
groupId="sdk-choice"
values={[
{label: 'curl', value: 'curl'},
{label: 'Postman', value: 'postman'},
]}>
<TabItem value="postman">


Paste the code into the key `code` in the active Postman environment and then get the token.

```bash
Send request Get token
```

</TabItem>
<TabItem value="curl">

Use the `code` in the following command.
You will also need to generate client authorization.

The client credentials is a base 64 encoded string consisting of the client_id and secret issued by Vipps.

Example in JavaScript:

```javascript
var client_id = 123456-test-4a3d-a47c-412136fd0871
var client_secret = testdzlJbUZaM1lqODlnUUtrUHI=

var wordArrayAzp = CryptoJS.enc.Utf8.parse(client_id + ":" + client_secret);
var client_authorization = CryptoJS.enc.Base64.stringify(wordArrayAzp);
```


```bash
curl https://apitest.vipps.no/access-management-1.0/access/oauth2/token \
-H 'Content-Type: application/x-www-form-urlencoded' \
-H 'Authorization: Basic {client credentials}' \
-H "Merchant-Serial-Number: 123456" \
-H "Vipps-System-Name: acme" \
-H "Vipps-System-Version: 3.1.2" \
-H "Vipps-System-Plugin-Name: acme-webshop" \
-H "Vipps-System-Plugin-Version: 4.5.6" \
-X POST \
--data-urlencode 'grant_type=authorization_code' \
--data-urlencode 'code=THE CODE FROM THE URL' \
--data-urlencode 'redirect_uri=http://localhost'
```

Copy the access token from the response.

</TabItem>
</Tabs>


### Step 5 - Get user info

Send request `Get Userinfo`. This uses [`GET:/vipps-userinfo-api/userinfo/`][userinfo-endpoint-login].

Use the access token from the previous step.

<Tabs
defaultValue="postman"
groupId="sdk-choice"
values={[
{label: 'curl', value: 'curl'},
{label: 'Postman', value: 'postman'},
]}>
<TabItem value="postman">

```bash
Send request Get userinfo
```

</TabItem>
<TabItem value="curl">


```bash
curl https://apitest.vipps.no/vipps-userinfo-api/userinfo/ \
-H "Content-Type: application/json" \
-H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni <truncated>" \
-H "Ocp-Apim-Subscription-Key: 0f14ebcab0ec4b29ae0cb90d91b4a84a" \
-H "Merchant-Serial-Number: 123456" \
-H "Vipps-System-Name: acme" \
-H "Vipps-System-Version: 3.1.2" \
-H "Vipps-System-Plugin-Name: acme-webshop" \
-H "Vipps-System-Plugin-Version: 4.5.6" \
-H "Idempotency-Key: 49ca711a-acee-4d01-993b-9487112e1def" \
-X GET
```

</TabItem>
</Tabs>










[access-token-endpoint]: https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost
[create-payment-endpoint]: https://developer.vippsmobilepay.com/api/epayment#tag/CreatePayments/operation/createPayment
[get-payment-endpoint]: https://developer.vippsmobilepay.com/api/epayment#tag/QueryPayments/operation/getPayment
[userinfo-endpoint]: https://developer.vippsmobilepay.com/api/userinfo#operation/getUserinfo
[userinfo-endpoint-login]: https://developer.vippsmobilepay.com/api/userinfo/#operation/userinfoAuthorizationCode