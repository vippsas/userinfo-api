<!-- START_METADATA
---
title: Userinfo API guide
sidebar_label: API guide
sidebar_position: 1
description: Find technical details about integrating with the Userinfo API.
pagination_prev: Null
pagination_next: Null
---
END_METADATA -->

# Userinfo API

The Userinfo API allows merchants to request the user's profile information as part of the payment flow.
This is also called "Profile Sharing".

The API follows the
[OIDC Standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo).

To get access to user profile information, the merchant adds a `scope`
parameter and initiates [specific API calls](#userinfo-call-by-call-guide).

The user will be asked to consent to sharing the information from Vipps to the
merchant as specified in the `scope`. If the user has not already consented to
sharing all the specified information, he/she will be asked for any remaining
consents before completing the payment flow.

In the Vipps app, they will be presented with a consent card.
For example, Android (left) and iOS (right):

![Consent card](images/share-user-info.png)

The consent card must be accepted before approving the payment or agreement in Vipps.
It is not possible to complete the flow without consenting.

Once the flow is completed, the merchant can get the profile
information from the
[`GET:/userinfo/{sub}`](https://developer.vippsmobilepay.com/api/userinfo#operation/getUserinfo).

A user's consent to share information with a merchant applies across all Vipps services.
This is helpful, for example, if the merchant implements the
[Login API](https://developer.vippsmobilepay.com/docs/APIs/login-api)
as part of the payment flow, they can use Vipps to log the user in without the
need for additional consents.

## Scope

The `scope` determines what information the user is asked to share.

| Scope            | Description | User consent required |
|:-----------------|:------------|:----------------------|
| `address`        | A list containing the user's addresses. The list always contains the home address from the National Population Register and can also include work address and other addresses added by the user in Vipps. | yes |
| `birthDate`      | Birth date. Verified with BankID. ISO 8601 format (2022-12-31) | yes |
| `email`          | Email address. The flag `email_verified : true` (or `false`) in the response indicates whether the email address is verified. | yes |
| `name`           | First, middle and given name. Verified with the National Population Register. | yes |
| `phoneNumber`    | Phone number. Verified when creating the Vipps account. MSISDN format (4791234567).| yes |
| `nin`            | Norwegian national identity number. Verified with BankID. **NB:** Merchants need to apply for access to NIN. See: [Who can get access to NIN and how?](https://developer.vippsmobilepay.com/docs/APIs/login-api/vipps-login-api-faq.md#who-can-get-access-to-nin-and-how)    | yes |

The `scope` can include any of the values above, separated by a space. Examples:

* `phoneNumber`
* `email`
* `name address email`
* `name phoneNumber`
* `name birthDate`
* `name email nin`

## Userinfo flow

This is an illustration of a consent card and a payment screen.
The user must complete both screens before the merchant can gain access to their profile information.
It is not possible for the user to complete the payment without these steps.

![Userinfo flow](images/userinfo-flow.png)

Here follows a sequence diagram to illustrate the flow of steps.

``` mermaid
sequenceDiagram
    actor User
    actor UserVippsApp
    participant Merchant
    participant Vipps

    User ->> Merchant: Initiate transaction
    Merchant ->> Vipps: Initiate transaction
    Note right of Merchant: Example scopes: name, address, email
    Vipps -->> Merchant : Session link
    Merchant ->> UserVippsApp : Trigger transaction session
    UserVippsApp ->> Vipps : Approve payment and detail sharing
    Vipps ->> Vipps : Save Consent and generate sub
    Vipps ->> Vipps : Perform Payment
    Vipps -->> UserVippsApp : Response
    UserVippsApp ->> Merchant : Response
    Merchant ->> Vipps : Retrieve details
    Vipps -->> Merchant : Return details, sub included
    Merchant ->> Vipps : Get on userinfo/{sub}
    Vipps -->> Merchant : Return userinfo
    Merchant ->> Merchant : Handle session result
```

## Userinfo call-by-call guide

Scenario: Complete a payment and get the user's name and phone number.

See
[HTTP headers](https://developer.vippsmobilepay.com/docs/vipps-developers/common-topics/http-headers)
for the standard headers that should be included.

1. Retrieve the access token with
   [`POST:/accesstoken/get`](https://developer.vippsmobilepay.com/api/access-token#tag/Authorization-Service/operation/fetchAuthorizationTokenUsingPost).

   See:
   [Access token API guide](https://developer.vippsmobilepay.com/docs/APIs/access-token-api).

2. Specify the `scope` for your access request.

   Example from the
   [Invoicing through ePayment](https://developer.vippsmobilepay.com/docs/vipps-solutions/invoice-through-epayments)
   solution, which uses the
   [`POST:/payments`](https://developer.vippsmobilepay.com/api/epayment#tag/CreatePayments)
   endpoint from the
   [ePayment API](https://developer.vippsmobilepay.com/docs/APIs/epayment-api):

    ```json
    {
    "amount":{
        "currency":"NOK",
        "value":2000
    },
    "customer":{
        "phoneNumber":4791234567
    },
    "paymentMethod":{
        "type":"wallet"
    },
    "profile":{
        "scope":"phoneNumber"
    },
    "reference":"acme-shop-123-order123abc",
    "returnUrl":"https://example.com/redirect?orderId=1512202",
    "userFlow":"WEB_REDIRECT"
    }
    ```

    **Please note:** If the e-mail address that is delivered has the flag
    `email_verified : false`, this address should not be used to link the user
    to an existing account without further authentication. Such authentication
    could be to prompt the user to log in to the original account or to confirm
    the account linking by providing a confirmation link sent to the email address.

3. Make the API request that initiates the Vipps payment or agreement. Include the `scope` in the body:

   * ePayment API:
     [`POST:/epayment/v1/payments`](https://developer.vippsmobilepay.com/api/epayment#tag/CreatePayments)
   * Recurring API:
     [`POST:/recurring/agreements`](https://developer.vippsmobilepay.com/api/recurring#tag/Agreement-v3-endpoints/operation/DraftAgreementV3)
   * eCom API:
     [`POST:/ecomm/v2/payments`](https://developer.vippsmobilepay.com/api/ecom#tag/Vipps-eCom-API/operation/initiatePaymentV3UsingPOST)

4. The user consents to the information sharing and completes the payment in Vipps.

5. Retrieve the `sub`, which identifies the user. The `sub` is a link between
   the merchant and the user and can be used to retrieve the user's details from
   [`GET:/userinfo/{sub}`](https://developer.vippsmobilepay.com/api/userinfo#operation/getUserinfo).
   The `sub` is based on the user's national identity number (NIN) and does not
   change (except in _very_ special cases).

   The format of the `sub` is:

   ```json
   "sub": "c06c4afe-d9e1-4c5d-939a-177d752a0944",
   ```

   Retrieve the `sub` by calling the API endpoint that provides information about the payment or agreement. For example:

   * ePayment API:
     [`GET:/epayment/v1/payments/{reference}`](https://developer.vippsmobilepay.com/api/epayment#tag/QueryPayments/operation/getPayment)
   * Recurring API:
     [`GET:/recurring/agreements/{agreementId}`](https://developer.vippsmobilepay.com/api/recurring#tag/Agreement-v3-endpoints/operation/FetchAgreementV3)
   * eCom API:
     [`GET:/ecomm/v2/payments/{orderId}/details`](https://developer.vippsmobilepay.com/api/ecom#tag/Vipps-eCom-API/operation/integrationTestApprovePayment)

6. To retrieve the user's information, call
   [`GET:/vipps-userinfo-api/userinfo/{sub}`](https://developer.vippsmobilepay.com/api/userinfo#operation/getUserinfo)
   with the `sub` that was retrieved earlier.

   This endpoint returns the payload with the information that the user has consented to share. See below on how to construct the call.

   **Please note:** It is recommended to get the user's information directly
   after completing the transaction. There is a *time limit of 168 hours* (one week)
   to retrieve the consented profile data from
   [`GET:/userinfo/{sub}`](https://developer.vippsmobilepay.com/api/userinfo#operation/getUserinfo).

   This is to better support merchants that depend on manual steps/checks in their
   process of fetching the profile data. The merchant will get the information that
   is in the user profile at the time when they actually fetch the information.
   This means that the information might have changed from the time the user
   completed the transaction and the fetching of the profile data.

   **Please note:** The `sub` is added asynchronously, so if the API request in (5)
   above is made within (milli)seconds of the payment approval in the app, it may
   not be available. If that happens, simply make another request. See
   [Polling guidelines](https://developer.vippsmobilepay.com/docs/vipps-developers/common-topics/polling-guidelines)
   for more recommendations.

## Example Userinfo request

This is an example based on the [Recurring API](https://developer.vippsmobilepay.com/docs/APIs/recurring-api/vipps-recurring-api#userinfo).

### Request

See [HTTP headers](https://developer.vippsmobilepay.com/docs/vipps-developers/common-topics/http-headers) for additional standard headers that should be included.

To request the `scope`, add the scope to the initial
[`POST:/recurring/agreements`](https://developer.vippsmobilepay.com/api/recurring#tag/Agreement-v3-endpoints/operation/DraftAgreementV3)
request.

For example:

```json
{
  "phoneNumber":"91234567",
  "interval": {
    "unit": "MONTH",
    "count": 1
  },
  "merchantRedirectUrl": "https://example.com/confirmation",
  "merchantAgreementUrl": "https://example.com/my-customer-agreement",
  "pricing": {
    "type": "LEGACY",
    "amount": 49900,
    "currency": "NOK"
  },
  "productDescription": "Access to all games of English top football",
  "productName": "Premier League subscription",
  "scope": "address name email birthDate phoneNumber"
}
```

**Example response from a successful request:**

```json
{
  "sub": "c06c4afe-d9e1-4c5d-939a-177d752a0944",
  "birthdate": "1968-07-16",
  "email": "user@example.com",
  "email_verified": true,
  "nin": "10121550047",
  "name": "Ada Lovelace",
  "given_name": "Ada",
  "family_name": "Lovelace",
  "sid": "f26d25af56909b55",
  "phone_number": "4791234567",
  "address": {
    "street_address": "Suburbia 23",
    "postal_code": "2101",
    "region": "OSLO",
    "country": "NO",
    "formatted": "Suburbia 23\\n2101 OSLO\\nNO",
    "address_type": "home"
  },
  "other_addresses": [
    {
      "street_address": "Robert Levins gate 5",
      "postal_code": "0154",
      "region": "OSLO",
      "country": "NO",
      "formatted": "Robert Levins gate 5\\n0154 OSLO\\nNO",
      "address_type": "work"
    },
    {
      "street_address": "Summer House Lane 14",
      "postal_code": "1452",
      "region": "OSLO",
      "country": "NO",
      "formatted": "Summer House Lane 14\\n1452 OSLO\\nNO",
      "address_type": "other"
    }
  ],
  "accounts": [
    {
      "account_name": "My savings",
      "account_number": "12064590675",
      "bank_name": "My bank"
    }
  ]
}
```

## Time limit

It is recommended to get the user's information directly after
completing the transaction.

There is a *time limit of 168 hours* (one week) to retrieve the consented
profile data from the `/userinfo` endpoint.

This is to better support merchants that depend on manual steps/checks in their process of
fetching the profile data. The merchant will get the information that is in the
user profile at the time when they actually fetch the information. This means
that the information might have changed from the time the user completed the
transaction and the fetching of the profile data.

If you attempt to retrieve the user's information after 168 hours, you will
get an error.

If you use the
[eCom API](https://developer.vippsmobilepay.com/docs/APIs/ecom-api)
and
[`GET:/ecomm/v2/payments/{orderId}/details`](https://developer.vippsmobilepay.com/api/ecom#tag/Vipps-eCom-API/operation/getPaymentDetailsUsingGET)
after 168 hours, the response will not contain the user's information, but instead:

```json
"userDetails": {
  "bankIdVerified": 0,
  "email": "[Expired]",
  "firstName": "[Expired]",
  "lastName": "[Expired]",
  "mobileNumber": "[Expired]",
  "userId": "[Expired]"
}
```
