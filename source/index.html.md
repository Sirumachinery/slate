---
title: Siru Mobile API reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL
  - php: PHP

toc_footers:
  - <a href='https://sirumobile.com'>Siru Mobile</a>

search: true

code_clipboard: true

meta:
  - name: description
    content: Siru Payment Gateway API documentation
  - name: robots
    content: noindex
---


# Introduction

Siru Mobile API provides a way for a merchant to accept payments from a customer via the customer's mobile phone. Customer is billed on his mobile phone bill.


## Glossary

| Term                | Meaning                                                                                                                                                       |
|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Siru Mobile         | That’s us our payment gateway!                                                                                                                                |
| Merchant            | That’s your boss, probably. Someone who sells some product payable with our payment gateway                                                                   |
| Customer, End user  | That’s someone using your application, paying for his purchase with our payment gateway                                                                       |
| Variant             | Our payment gateway contains several methods of payment or sub-API’s, so to say. We call these “variants”. Don't worry, we will tell you what variant to use. |
| Purchase            | A purchase (transaction) in Siru Mobile payment gateway                                                                                                       |

## Get Started

Integrating to Siru Mobile is easy and quick, just follow these simple steps.

1.  Contact us so we can discuss where you will be using Siru Mobile payments.
2.  We provide you with sandbox credentials and details on testing and integration.
3.  Read this documentation from top to bottom.
4.  Code and test your application against our sandbox.
5.  When ready to go live, contact us for production environment credentials.

<aside class="notice">
    We will be available for questions all the way from integration to going live. You can join our developer chat anytime where we can provide help while you test and monitor your transactions.
</aside>

## SDK and integrations

We publish both ready-made libraries and a reference / example shop implementation at GitHub. It’s all free software and open source, so feel free to use them. Contributions and pull requests are appreciated!

-  [PHP SDK](https://github.com/Sirumachinery/siru-php-sdk)
-  [Example shop](https://github.com/Sirumachinery/example-shop-php)
-  Magento plugin. Contact us!
-  WooCommerce plugin. Contact us!

# Environment

For integration and testing we provide a sandbox environment that does not use actual money. Here you can test your integration all you want and when ready, switch to production environment. Urls are the same for both environments. Only the endpoint changes.

| Environment  | Endpoint                       |
|--------------|--------------------------------|
| Production   | https://payment.sirumobile.com |
| Sandbox      | https://staging.sirumobile.com |

<aside class="notice">
    We will provide you with separate credentials to production environment. You can not use sandbox credentials in production!
</aside>

<aside class="notice">
    The production environment imposes limits on how much the user can spend in a given time period to prevent misuse. The sandbox environment does not impose such limits.
</aside>

# Authentication

Requests and responses are authenticated by a signature created using request values and merchant secret we have provided.

Contact Siru Mobile to receive your credentials. We will first provide credentials to Sandbox environment and when you are ready to go live, we will provide you with another set of credentials for production. You may receive separate credentials for each country where you are integrating.







# Payment API

## Create payment

### DESCRIPTION

Creates a new payment at Siru Mobile. You post the required parameters as JSON object in the request body. The API responds with unique UUID for the purchase and a URL where the end-user should be redirected to complete the payment.

### REQUEST

`POST /payment.json`

```shell
curl --request POST \
    --url https://payment.sirumobile.com/payment.json \
    --header 'content-type: application/json' \
    --data "$JSON"
```

```php
<?
// Siru PHP-SDK provides helpers to sends requests
$signature = new \Siru\Signature($merchantId, $secret);
$api = new \Siru\API($signature);

try {

    $transaction = $api->getPaymentApi()
        ->set('variant', 'variant1')
        ->set('purchaseCountry', 'SE')
        ->set('basePrice', '5.00')
        ->set('redirectAfterSuccess', 'https://shop.example.com/success')
        ->set('redirectAfterFailure', 'https://shop.example.com/fail')
        ->set('redirectAfterCancel', 'https://shop.example.com/cancel')
        ->set('customerNumber', '0701234567')
        ->set('customerReference', 'c12345')
        ->set('customerEmail', 'john.doe@foo.bar')
        ->set('customerLocale', 'sv_SE')
        ->set('title', 'Game voucher')
        ->createPayment();

    header('location: ' . $transaction['redirect']);
    exit();

} catch(\Siru\Exception\InvalidResponseException $e) {
    // Connection to API failed
} catch(\Siru\Exception\ApiException $e) {
    // Error in request
    foreach($e->getErrorStack() as $error) {
        // Log error
    }
}
```

> Example of JSON payload

```json
{
    "merchantId":1234,
    "basePrice":"5.00",
    "purchaseCountry":"SE",
    "customerReference":"c12345",
    "customerNumber":"0701234567",
    "customerEmail":"john.doe@foo.bar",
    "customerLocale":"sv_SE",
    "variant":"variant1",
    "redirectAfterSuccess":"https://shop.example.com/success",
    "redirectAfterFailure":"https://shop.example.com/fail",
    "redirectAfterCancel":"https://shop.example.com/cancel",
    "signature":"..."
}
```

<aside class="notice">
    You can also use the API by constructing a HTML form with the required parameters as input fields. Form is sent to /payment.html endpoint using POST method which will redirect user to payment page.
    This method is not recommended as it exposes more data to the end-user and prevents you from catching errors in the request.
</aside>

### REQUEST PARAMETERS

Some fields are required and some are optional. To improve user experience, you should include even optional fields when available. Some fields are only required for some variants.

| Field                | Required                     | Format        | Description                                                                                                                                                                                                                                          |
|----------------------|------------------------------|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| variant              | yes                          | String        | The API variant. 'variant1', 'variant2', 'variant3' or 'variant4'.                                                                                                                                                                                   |
| merchantId           | yes                          | Integer       | Your merchant id provided by Siru.                                                                                                                                                                                                                   |
| submerchantReference |                              | String        | If same merchant id is used on multiple sites, you **must** use this field to identify the originating site. For example domain name or brand name.                                                                                                  |
| purchaseCountry      | yes                          | Country       | The country where you sell the item. Currently supported are 'FI', 'SE', 'NO', 'GB', 'AT' and 'DE'                                                                                                                                                   |
| purchaseReference    |                              | String        | Your internal ID for the purchase. These will be visible in the merchant's control panel.                                                                                                                                                            |
| customerReference    |                              | String        | Your internal ID for the customer. These will be visible in the merchant's control panel.                                                                                                                                                            |
| customerLastName     | DE                           | String        | Customer's last name, shown in the merchant panel.                                                                                                                                                                                                   |
| customerFirstName    | DE                           | String        | Customer's first name, shown in the merchant panel.                                                                                                                                                                                                  |
| customerPersonalId   | DE                           | Personal ID   | Customer's personal ID (national ID, social security number) or birthdate in Germany in YYYYMMDD syntax                                                                                                                                              |
| customerEmail        |                              | String        | Customer's email address, shown in the merchant panel                                                                                                                                                                                                |
| customerLocale       |                              | Locale        | Language of the payment page. Defaults to the default locale of purchaseCountry. Currently supported 'fi_FI', 'sv_SE', 'nn_NO', 'en_GB', 'de_AT' and 'de_DE'.                                                                                        |
| redirectAfterSuccess | yes                          | URL           | URL where the customer is redirected after a successful payment.                                                                                                                                                                                     |
| redirectAfterFailure | yes                          | URL           | URL where the customer is redirected after an unsuccessful payment.                                                                                                                                                                                  |
| redirectAfterCancel  | yes                          | URL           | URL where the customer is redirected if he cancels the payment himself, or if the system is down for maintenance.                                                                                                                                    |
| notifyAfterSuccess   |                              | URL           | Your callback URL to notify after a successful purchase.                                                                                                                                                                                             |
| notifyAfterFailure   |                              | URL           | Your callback URL to notify after a failed purchase.                                                                                                                                                                                                 |
| notifyAfterCancel    |                              | URL           | Your callback URL to notify after a canceled purchase.                                                                                                                                                                                               |
| basePrice            | yes                          | Money         | The price of the product or service. Siru's fees are added automatically. Price must include tax when applicable unless [variant2](#variant2) is used. Maximum price or available price points depend on country and your contract with Siru Mobile. |
| currency             |                              | String        | Currency code is selected automatically based on purchaseCountry but can be changed manually. Payments in currency other than the default currency must be first agreed with Siru Mobile                                                             |
| taxClass             | variant1, variant2, variant4 | Tax class     | Tax class number. Required only if purchaseCountry is listed in Tax class-table. Must be empty otherwise. See [Field types](#field-types).                                                                                                           |
| serviceGroup         | variant1, variant2, variant4 | Service group | Service group number. Required only if purchaseCountry is listed in Service group-table. Must be empty otherwise. See [Field types](#field-types).                                                                                                   |
| customerNumber       | variant1, variant4           | Phone number  | The customer’s phone number.                                                                                                                                                                                                                         |
| title                | variant4                     | String        | A short title of the purchased service or product.                                                                                                                                                                                                   |
| description          |                              | String        | A longer description of the purchased service or product.                                                                                                                                                                                            |
| interval             |                              | String        | Subscription interval. Valid values depend on your contract.                                                                                                                                                                                         |
| trialPeriod          |                              | String        | Subscription trial period. Valid values depend on your contract.                                                                                                                                                                                     |
| instantPay           | variant2                     | integer       | Deprecated. When using variant2, this value must be 1.                                                                                                                                                                                               |
| signature            | yes                          | Signature     | Calculated hash for the request. See [Signature calculation](#signature-calculation).                                                                                                                                                                |

<aside class="success">
    Even if the field is not required by selected variant, you can still include it. For example while customerNumber is not required when using variant2 or variant3, you can still send it when available. This is even recommended since it may allow Siru to improve the user experience.
</aside>

<aside class="notice">
    While customerEmail and customerReference fields are not marked as required, you should always send them if available. Your contract with Siru Mobile may even require this.
</aside>

> Example response

```json
{
    "success":true,
    "purchase": {
        "uuid":"f9503276-80bc-4f0e-a995-16c4c7e9d0f7",
        "redirect":"https://payment.sirumobile.com/payment/call/f9503276-80bc-4f0e-a995-16c4..."
    }
}
```

### RESPONSE

A JSON object with `success` boolean indicating if request was successful. If request was successful, object includes a unique id for the transaction and URL where user should be redirected.

If request was not successful, response includes errors array with all error messages. The errors should not be shown to end-user but instead logged for later.


## Variants and testing

Siru Mobile provides different methods of payment which we call "variants". What variants are available to you depend on your contract with Siru.

### Variant1

This variant is for older phones where payment is made by calling a premium rate number. You must call from a mobile phone subscription from selected country and you can not be roaming.

You can use your own mobile phone, or we can provide a Skype number for testing. In sandbox, no actual charges are made and only mobile operator call prices apply.

Countries | Testing in sandbox
--        | --
FI    | Available price points for basePrice are 5.00, 10.00, 15.00 and 25.00.
SE    | Available price points for basePrice are 2.50, 5.00 and 10.00.
<aside class="notice">
    We can provide you a Skype number for testing.
</aside>

### Variant2

This variant is only available in Finland. It allows a fast online payment when using mobile internet connection. The purchase is billed on users mobile subscription.

Countries | Testing in sandbox
--        | --
FI        | To simulate an error during payment, use basePrice 30.50 or 30.60.<br/><br/>In sandbox, you can use any internet connection to confirm payment. No actual charges are made.<br/><br/>[Feature detection API](#feature-detection-api) will always return true when in sandbox.

<aside class="warning">
    IMPORTANT: The basePrice value must be given without VAT. Correct tax is added to the price automatically based on taxClass value. Price can be anything between 0.10 and 60.00.
</aside>

<aside class="notice">
    Variant2 payments are only available when using mobile internet connection. You should use our Feature detection API to check if variant2 is possible for end user.
</aside>

### Variant3

This is a digital wallet where customer can deposit money with various means (Siru Mobile payment gateway, for example) and use it everywhere where the wallet is an accepted payment method. When using Siru Mobile gateway to topup Wallet, variant1 limitations also apply.

The basePrice can be anything between 0.10 and 500.00. 

Countries | Testing in sandbox
--        | --
FI, SE, NO| Use your own mobile number.<br/><br/>When using Siru Mobile gateway to top up Wallet, variant1 limitations also apply.<br/><br/>We can provide you with a test wallet for testing with fake money already deposited.

### Variant4

Variant4 is a fast online payment currently available in UK, Austria and Germany

The maximum basePrice depends on the target country and your contract with Siru, but it is usually between 30-50EUR

Countries | Testing in sandbox
--        | --
GB        | Use your own mobile number or one of the test numbers that we provide
AT        | You must use the test number and one fixed price point that we provide
DE        | You must use the test number, name, birthdate and one fixed price point that we provide

## Signature calculation

```php
<?
$secret = 'MySecretFromSiruMobile';
$fields = array(
    'variant' => 'variant1',
    'merchantId' => '123123',
    'purchaseCountry' => 'FI',
    'basePrice' => '12.34',
    'taxClass' => 3,
    'serviceGroup' => 3,
    'customerNumber' => '050 123 4567',
    // ...
);
$fields = array_filter($fields); // Drop empty fields
ksort($fields); // Sort by field name
$signature = hash_hmac("sha512", implode(';', $fields), $secret);
```

The data must be signed using a hash from certain values in data and the merchant's secret. The signature is calculated as follows:

1. Take all the fields from the table below that match the variant being used.

2. Drop all values that are empty.

3. Sort values by the field name.

4.  Concatenate the values of those fields in that order, with a semicolon (‘;’) as a separator.

5.  Take a SHA512-HMAC with the resulting string as data and the merchant's secret as the key.

| Field                | Variants                             |
|----------------------|--------------------------------------|
| basePrice            | All                                  |
| currency             | All                                  |
| customerNumber       | Variant1 and variant4 only           |
| customerPersonalId   | All                                  |
| customerReference    | All                                  |
| description          | Variant4 only                        |
| trialPeriod          | Variant2 only                        |
| instantPay           | Variant2 only                        |
| interval             | Variant2 and Variant4 only           |
| merchantId           | All                                  |
| notifyAfterCancel    | All                                  |
| notifyAfterFailure   | All                                  |
| notifyAfterSuccess   | All                                  |
| purchaseCountry      | All                                  |
| purchaseReference    | All                                  |
| serviceGroup         | Variant1, variant2 and variant4 only |
| submerchantReference | All                                  |
| taxClass             | Variant1, variant2 and variant4 only |
| title                | Variant2 and variant4 only           |
| variant              | All                                  |

## Field types

### String
An UTF-8 string with a maximum length of 255 characters.

### Integer
A non-negative JSON integer that fits into 32 bits.

### Country
A ISO-3166-1 alpha-2 country code in upper case. Example 'FI'.

### Locale
The format is {Language code}_{Country code} i.e. like most POSIX locales. Supported locales are fi_FI, sv_SE, nn_NO, en_GB (default), de_AT and de_DE are supported.

### Phone number

Phone number can be sent either in international MSISDN format or in national format in which case the API automatically converts the number to international format based on purchaseCountry value. A leading + character and any spaces are ignored.

Examples of valid phone numbers: 358501234567, +358 50 1234567, 0501234567, 050 123 4567

More information

- <http://en.wikipedia.org/wiki/MSISDN>
- [libphonenumber](https://github.com/googlei18n/libphonenumber) is a handy library for validating phone numbers and has been ported to multiple languages.

### Money

Money fields must be given as a string with a decimal point (not a comma) and two decimal places. The two
decimal places must be given even when zero. The currency is determined by the purchaseCountry field.
Examples: 13.50, 0.20, 7.15

### Personal ID

If end-users personal ID is provided, Siru will compare this to available KYC data and automatically
blocks payment if it does not match with users verified personal ID number.  In Germany, this value is required.
In other countries, this feature is only available upon request.

<aside class="notice">
    In Germany, use end-users birthdate in format YYYYMMDD. For example 19820215
</aside>
<aside class="warning">
    The personal ID must be valid in given purchaseCountry. If the personal ID is not valid in selected country, Payment API will respond with error.
</aside>


### URL
An UTF-8 string that is a valid URL. The maximum length is 1024 characters. The validation is strict, so use strictly valid urls like http://example.com/?variable=value instead of http://example.com?variable=value.

For redirect URLs, the following parameters are added to the query. For notification URLs, the parameters are sent in a JSON object in POST body.

| Parameter                 | Format  | Description                                                    |
|---------------------------|---------|----------------------------------------------------------------|
| siru_uuid                 | String  | Siru's unique identifier for the purchase. 36 characters long. |
| siru_merchantId           | Integer | The merchantId given in the request.                           |
| siru_submerchantReference | String  | The submerchantReference given in the request.                 |
| siru_purchaseReference    | String  | The purchaseReference given in the request.                    |
| siru_event                | String  | One of the following: success, failure or cancel.              |
| siru_signature            | String  | See below.                                                     |

siru_signature is similar to the request's signature and you **must** use it to validate authenticity of the parameters. It is calculated by concatenating the values (including empty values) of siru_uuid, siru_merchantId, siru_submerchantReference, siru_purchaseReference and siru_event (in that order) with a semicolon (‘;’) as a separator and taking a SHA512-HMAC with the resulting string as data and the merchant secret as key.

If your redirect page does anything that depends on the purchase being completed successfully, then you must verify that siru_signature and siru_event are correct! Attacker may try and direct his browser to send failure parameters to the success URL.

The customer may fail to arrive at any of the redirect URLs for various reasons, such as connection loss or user error. Relying on the notifyAfter* URLs for purchase confirmation is therefore recommended, even though it is takes some extra effort.

Your server should respond to the notification with HTTP 200 code. If the notification fails, either due to a network error or with a non-200 code, then it is retried for 7 days. This time limit may change in the future.

<aside class="warning">
    In very rare (and theoretically unavoidable) corner cases, it is possible for a notification to be delivered more than once. Your system should be prepared to ignore a second notification, preferably with 200 code.
</aside>


### Tax class
This specifies the Value Added Tax class. If your country is not listed here, leave this field empty.

|Num. | Countries | VAT % |
|---  | ---       |-------|
|0    | Finland   | 0%    |
|1    | Finland   | 10%   |
|2    | Finland   | 14%   |
|3    | Finland   | 24%   |

Tax classes may be added, changed and removed as legislation changes. Siru will notify merchants in advance of such changes. Tax class number 0 will, however, always mean a 0% VAT.

More information<br>

- <https://www.vero.fi/yritykset-ja-yhteisot/verot-ja-maksut/arvonlisaverotus/arvonlisaveroprosentit/> (in Finnish)<br>
- <https://www.vero.fi/en/businesses-and-corporations/taxes-and-charges/vat/rates-of-vat/> (in English)

### Service group
This specifies the service group of the product or service. If your country is not listed here, leave this field empty.

|Num. | Countries | FICORA title                 |
|---  | ---       |------------------------------|
|1    | Finland   | Non-profit services          |
|2    | Finland   | Online services              |
|3    | Finland   | Entertainment services       |
|4    | Finland   | Adult entertainment services |

A customer's phone subscription may bar calls to certain service groups. The service groups also show up on the customer's phone bill breakdown. If multiple service groups apply, then the group with the highest number must be chosen. Contact Siru for further advice if you have any doubt.

The service group scheme may change as legislation changes. Siru will notify merchants in advance if any changes are required.

More information<br>

- <http://www.mapel.fi/eettiset_saannot/palveluiden_luokittelu/>

### Signature

SHA512-HMAC hash calculated from selected field values and merchant's secret. See [Signature calculation](#signature-calculation).



## Common pitfalls

We have gathered here some of the more common mistakes you might encounter during integration.

### Invalid mobile number

We use very strict validation for phone numbers ie. the customerNumber field. See [Phone number](#phone-number). You should validate phone number carefully. Often we see numbers with missing 0 prefix (national format), missing country code (international format) or with both 0 and country code prefix which is invalid.

Examples of **invalid** values: 3580501234567, 501234567

### Invalid price point

When integrating variant1 payments, the allowed values for base price might be limited to a set of fixed price points. Also note that the price points
are different between staging- and production environments. Attempt to create payment with price that is not one of the allowed price points or that exceeds the agreed maximum price for the country, will result in an error.

With other variants, there are no fixed price points but there is still a maximum payment amount.

Details of the allowed base price values are sent with your API credentials.

### Invalid variant for given country

Usually we have only enabled one variant per country for you to use. For example variant3 in Norway and variant1 in Finland. If you mix these, the API call will result in an error.

### Monitoring errors

You and we at Siru want to keep the end-users happy and not miss any sales. If a bug has made its way to your code and the API call fails, we log the error automatically. You can monitor failed API calls from our Merchant panel. Contact us if you have not received your credentials to our Merchant panel.


## Subscriptions

If you have subscriptions enabled with Siru Mobile, you can create a new payment that is automatically renewed on agreed upon intervals. When using subscriptions, there are some things you must take in to account.

### Notifications

To receive notification when payment is renewed, you must provide notifyAfterSuccess URL. This is the only way to know when payment has been renewed.

### Purchase reference and UUID

When creating a new subscription, you must use a unique purchaseReference. When payment is automatically renewed, the notification will have the same purchaseReference as the original payment.

When creating a new subscription, store the UUID you receive from Payment API. You need this if you need to end subscription via API.

# Subscription API

If purchase was created as a subscription which is automatically renewed by Siru at set intervals, you can cancel the subscription using this API.

## Unsubscribe

### DESCRIPTION

Cancels active subscription.

### REQUEST

```shell
curl --request POST \
    --url https://payment.sirumobile.com/payment/unsubscribe/byUuid.json \
    --header 'content-type: application/json' \
    -d '{"merchantId":1,"uuid":"061d488d-ab43-458d-9b5e-0b2005595d7e","signature":"mycalculatedhash"}'
```

`POST /payment/unsubscribe/byUuid.json`

### REQUEST PARAMETERS

Parameter  | Format | Value
--         | --     | --
merchantId|Integer|Your merchant ID
uuid|String|UUID of the purchase that was used to create the subscription
signature|String|Concatenate the values of merchantId and uuid (in that order), with ';' as a separator. Take the SHA512-HMAC of that with the merchant's secret as the key.

> Response example

```json
{
    "success": true
}

```

### RESPONSE

## Errors

> Failure response example

```json
{
    "error":
    {
        "code":404,
        "message":"Not Found"
    }
}
```

HTTP status | Reason
--          | --
403         | Invalid signature
404         | Purchase by UUID was not found or is not a subscription





# Purchase Status API

The purchase status API provides information about statuses of purchases.

There are three ways to query:

*   by the merchant provided purchaseReference field
*   by the Siru-assigned UUID
*   by a date range.

## Query by purchase reference

### DESCRIPTION

Search transactions that match given purchase reference.

### REQUEST

```shell
curl --request GET -G \
    --url https://payment.sirumobile.com/payment/byPurchaseReference.json \
    -d 'merchantId=1' \
    -d 'purchaseReference=p12345678' \
    -d 'signature=mycalculatedhash'
```

`GET /payment/byPurchaseReference.json`

### REQUEST PARAMETERS

Parameter  | Format | Value
--         | --     | --
merchantId|Integer|Your merchant ID
submerchantReference|String|Submerchant reference. If not empty, only transactions from this submerchant are included. Otherwise all matching transactions are included.
purchaseReference|String|The purchase reference.
signature|String|Concatenate the values (including empty values) of merchantId, submerchantReference and purchaseReference (in that order), with ';' as the separator. Take the SHA512-HMAC of that with the merchant's secret as the key.

> Response example

```json
{
    "purchases": [
        {
            "uuid": "e53c3cfc-6d5b-4af3-ae30-7ae7812c9cf7",
            "submerchantReference":null,
            "customerReference":null,
            "purchaseReference":"testshop1420070765",
            "status": "unconfirmed",
            "basePrice": "12.34",
            "finalPrice": "12.55",
            "currency": "EUR",
            "createdAt": "2013-06-13T12:51:33+0200",
            "startedAt": "2013-06-13T12:51:58+0200",
            "finishedAt": null,
            "customerNumber": "358501234567"
        }
    ]
}
```

### RESPONSE

JSON object with a single property "purchases" which contains array of matching transactions.

<aside class="notice">
    Siru does not enforce the uniqueness of purchase reference so you may get more than one result. The results are sorted newest first.
</aside>

## Query by uuid

### DESCRIPTION

Find a single transaction by UUID.

### REQUEST

```shell
curl --request GET -G \
    --url https://payment.sirumobile.com/payment/byUuid.json \
    -d 'merchantId=1' \
    -d 'uuid=061d488d-ab43-458d-9b5e-0b2005595d7e' \
    -d 'signature=mycalculatedhash'
```

`GET /payment/byUuid.json`

### REQUEST PARAMETERS

Parameter  | Format | Value
--         | --     | --
merchantId|Integer|Your merchant ID
uuid|String|The UUID to query by
signature|String|Concatenate the values of merchantId and uuid (in that order), with ';' as a separator. Take the SHA512-HMAC of that with the merchant's secret as the key.


### RESPONSE

The response is a single transaction object. If UUID is not found, an error with HTTP 404 status is returned.

## Query by date range

### DESCRIPTION

Search all transactions that were created during given time period.

### REQUEST

```shell
# Retrieve transactions for december 2017
curl --request GET -G \
    --url https://payment.sirumobile.com/payment/byDate.json \
    -d 'merchantId=1' \
    -d 'from=2017-12-01 00:00:00' \
    -d 'to=2018-01-01 00:00:00' \
    -d 'signature=mycalculatedhash'
```

`GET /payment/byDate.json`

### REQUEST PARAMETERS

Parameter  | Format | Value
--         | --     | --
merchantId|Integer|Your merchant ID
from|String|Lower date limit (UTC). Purchases with this datetime will be included in the result. Format: yyyy-mm-dd hh:ii:ss (2015-01-01 00:00:00).
to|String|Upper date limit (UTC). Purchases with this datetime will be excluded from the result. Format: yyyy-mm-dd hh:ii:ss (2015-01-01 00:00:00).
signature|String|Concatenate the values of merchantId, from and to (in that order), with ';' as a separator. Take the SHA512-HMAC of that with the merchant's secret as the key.

> Response example

```json
{
    "purchases": [
        {
            "id": 1234567,
            "uuid":"f599cd21-47a2-4bbc-b44b-2cc4b1bb25f1",
            "merchantId":18,
            "submerchantReference":null,
            "customerReference":null,
            "purchaseReference":"testshop1420070765",
            "customerNumber":"XXXXXXX",
            "basePrice":"5.00",
            "finalPrice":"8.51",
            "currency": "EUR",
            "status":"canceled",
            "createdAt":"2015-01-01T00:06:05+0000",
            "startedAt":null,
            "finishedAt":"2015-01-01T00:17:09+0000"
        }
    ]
}

```

### RESPONSE

JSON object with a single property "purchases" which contains array of matching transactions. The transaction objects will include some additional fields for your leisure!

## Response fields

> Example of transaction object returned by status API

```json
    {
        "uuid": "e53c3cfc-6d5b-4af3-ae30-7ae7812c9cf7",
        "submerchantReference":null,
        "customerReference":null,
        "purchaseReference":"testshop1420070765",
        "status": "unconfirmed",
        "basePrice": "12.34",
        "finalPrice": "12.55",
        "currency": "EUR",
        "createdAt": "2013-06-13T12:51:33+0200",
        "startedAt": "2013-06-13T12:51:58+0200",
        "finishedAt": null,
        "customerNumber": "358501234567"
    }
```

The basic transaction object returned by search includes fields

Field          | Description
--             | --
uuid           | Siru unique UUID for this transaction
submerchantReference  | Sub-merchant reference sent by you
customerReference     | Customer reference sent by you
purchaseReference     | Purchase reference sent by you
status         | Purchase current status, see below table
basePrice      | The original price sent by you
finalPrice     | The price user was charged with possible commission included
currency       | Payment currency
createdAt      | ISO-8601 date when transaction was created
startedAt      | ISO-8601 date when customer started payment
finishedAt     | ISO-8601 date when purchase was finalized
customerNumber | Customers phone number if available

The possible values for "status" are as follows:

Status | Meaning
--     | --
new | Siru has received the purchase but the customer has not yet initiated payment.
unconfirmed | The customer is undergoing phone number verification.
call_started | The customer has initiated payment by calling Siru's payment number.
confirmed | The payment is successful.
canceled | The user has chosen to cancel the payment.
failed | The purchase has failed due to an error. The merchant panel may provide more information.

The states confirmed, canceled and failed are final states. Any other state should be considered as intermediate and subject to change. Once purchase reaches a final state, it will not leave that state.

Backwards-compatibility is guaranteed as follows: Siru may add or remove only non-final states.

## Errors

> Failure response example

```json
{
    "error":
    {
        "code":404,
        "message":"Not Found"
    }
}
```

HTTP status | Reason
--          | --
403         | Invalid signature
404         | Purchase by UUID was not found








# KYC API

The KYC API can be used to retrieve end-user KYC information after successful payment. KYC data is availabe
for limited countries only and is available for limited time only after payment is confirmed.

This API is only available upon request. Please contact our developer support or sales if you need access to this API.


## Query by uuid

### DESCRIPTION

Lookup KYC information for payment by UUID. 

### REQUEST

```shell
curl --request GET -G \
    --url https://payment.sirumobile.com/payment/kyc \
    -d 'merchantId=1' \
    -d 'uuid=061d488d-ab43-458d-9b5e-0b2005595d7e' \
    -d 'signature=mycalculatedhash'
```

`GET /payment/kyc`

### REQUEST PARAMETERS

Parameter  | Format | Value
--         | --     | --
merchantId|Integer|Your merchant ID
uuid|String|The UUID to query by
signature|String|Concatenate the values of merchantId and uuid (in that order), with ';' as a separator. Take the SHA512-HMAC of that with the merchant's secret as the key.


### RESPONSE

> Example report

```json
    {
        "report": {
            "firstName": "James",
            "lastName": "Smith"
        }
    }
```

If KYC data is available, response JSON contains key "report" with end-users verified firstName and lastName.
If UUID is not found or KYC data is no longer available, HTTP 404 is returned.

## Errors

> Failure response example

```json
{
    "error":
    {
        "code":404,
        "message":"Not Found"
    }
}
```

HTTP status | Reason
--          | --
403         | Invalid signature or KYC API is not enabled
404         | Purchase was not found or KYC data is no longer available









#Price API

## Calculate end-user price

### DESCRIPTION

Siru will automatically add possible fees and commissions to transaction base price. Since the exact formula for calculating the final price is somewhat intricate and subject to change, this API allows you to calculate the final price to display it to the end-user.

### REQUEST

```shell
curl --request GET -G \
    --url https://payment.sirumobile.com/payment/price.json \
    -d 'variant=variant1' -d 'merchantId=18' -d 'purchaseCountry=FI' -d 'taxClass=3' -d 'basePrice=15.00'
```

`GET /payment/price.json`

### REQUEST PARAMETERS

Parameter | Format | Value
--        | --     | --
variant|String|variant1 or variant4
merchantId|Integer|Your merchant ID
submerchantReference (optional)|String|Submerchant reference, if any.
purchaseCountry|Country|The country where the merchant sells the item.
taxClass <br>(optional*)|Tax class|The tax class number.
basePrice|Money|The price of the product with VAT included.
callback <br>(optional)|String|The JSONP callback name, if you are using JSONP.

<aside class="notice">
    This API is useful for variant1 and variant4 payments only.
</aside>

> Response example

```json
{
    "finalCallPrice": "13.57"
}
```

### RESPONSE

Response is a JSON object with single property "finalCallPrice".

> Failure response example

```json
{
    "error": "technical error message in English"
}

```

### ERRORS

If an error occurs, the response is a JSON object with single property "error" with error message. The error message should not be shown to end user.

## Get maximum base price

### DESCRIPTION

Siru imposes a limit on the maximum price of a purchase. The limit is fairly stable, so it may be acceptable to hard-code it into your application. Nevertheless, the limit can be retrieved through a JSON API.

### REQUEST

```shell
curl --request GET -G \
    --url https://payment.sirumobile.com/payment/maxPrice.json \
    -d 'variant=variant1' -d 'merchantId=18' -d 'purchaseCountry=FI' -d 'taxClass=3'
```

`GET /payment/maxPrice.json`

### REQUEST PARAMETERS

Parameter  | Format | Value
--         | --     | --
variant|String|variant1
merchantId|Integer|Your merchant ID
submerchantReference<br>(optional)|String|Submerchant reference, if any
purchaseCountry|Country|The country where the merchant sells the item.
taxClass <br>(optional*)|Tax class|The tax class number.
callback <b>(optional)|String|The JSONP callback name, if you are using JSONP.

> Response example

```json
{
    "maxBasePrice": "39.40",
    "maxFinalCallPrice": "40.00"
}
```

### RESPONSE

Response is a JSON object with two properties

Field             | Description
--                | --
maxBasePrice      | The maximum available base price.
maxFinalCallPrice | The final price of maxBasePrice.

<aside class="notice">
    End-user may also have individual spending limits with Siru. This API does not take those into account.
</aside>

> Failure response example

```json
{
    "error": "technical error message in English"
}

```

### ERRORS

Errors are JSON objects with single property "error". The error message should not be shown to end user. Response will include one of the following HTTP status codes.

HTTP status | Description
--          | --
404         | One of the request parameters was invalid
500         | An unknown error






# Feature detection API

## Check IP-address

### DESCRIPTION

The Feature detection API can be used to verify if IP Payments (variant2) are available for given IP-address.

### REQUEST

```shell
curl --request GET -G \
    --url https://payment.sirumobile.com/payment/ip/feature-check \
    -d 'merchantId=18' -d 'ip=12.12.12.12' -d 'signature=mycalculatedhash'
```

`GET /payment/ip/feature-check`

### REQUEST PARAMETERS

Parameter  | Format | Value
--         | --     | --
merchantId | Integer| Your merchant ID
ip         | String | End-users IP address
signature  | String | Concatenate the values (including empty values) of merchantId and ip (in that order), with ';' as the separator. Take the SHA512- HMAC of that with the merchant's secret as the key.

> Response example

```json
{
    "ipPaymentsEnabled": true
}

```

### RESPONSE

### ERRORS

> Failure response example

```json
{
    "error":
    {
        "code":403,
        "message":"Forbidden"
    }
}

```

Errors are JSON objects with single property "error". Response will include one of the following HTTP status codes.

HTTP status | Description
--          | --
403         | Invalid signature






# API operational status

## Check API status

### DESCRIPTION

Check operational status of Siru Mobile API.

### REQUEST

```shell
curl -w "%{http_code}" --url https://payment.sirumobile.com/status
```

`GET /status`

### REQUEST PARAMETERS

None

### RESPONSE

The response HTTP status code will tell you the operational status of the API.

HTTP status | Description
--|--
200|Siru Mobile is up and all should be well.
503|Siru Mobile is temporarily down for maintenance. Usually this doesn't last longer than a few minutes.
500|There is a problem with Siru Mobile API. You should never get this response from status query.

# Maintenance

Occasionally we will do maintenance on Siru Mobile API which may require a temporary down time. Don't worry: we handle all your customers' ongoing purchases gracefully and (if the need for maintenance isn't surprising or urgent) you should also know about all our maintencance windows in advance.
