---
title: Siru Mobile API reference

language_tabs: # must be one of https://git.io/vQNgJ
  - html: HTML
  - php: PHP


toc_footers:
  - <a href='https://sirumobile.com'>Siru Mobile</a>

includes:
  - api/status
  - api/price
  - api/feature
  - api/operationalstatus

search: true


---

# Introduction

Siru Mobile API provides a way for a merchant to accept payments from a customer via the customer's mobile phone. Customer is billed on his mobile phone bill.


## Glossary

Term | Meaning
-------------- | --------------
Siru Mobile | That’s us our payment gateway!
Merchant    | That’s your boss, probably. Someone who sells some product payable with our payment gateway
Customer, End user|That’s someone using your application, paying for his purchase with our payment gateway
Variant     | Our payment gateway contains several methods of payment or sub-API’s, so to say. We call these “variants”. Don't worry, we will tell you what variant to use.
Purchase    | A purchase (transaction) in Siru Mobile payment gateway

## Get Started
Integrating to Siru Mobile is easy and quick, just follow these simple steps.

1.  You or your business guys contact Siru Mobile so we understand where you will be using Siru Mobile payments.
2.  Request sandbox credentials from us. We also provide examples on how to use the API.
3.  Read this documentation from top to bottom.
4.  Code and test your application against our sandbox. It’s all fake money there, don’t worry.
5.  When ready to go live, just switch to Siru production environment using your production credentials that we have provided to you. If you still haven't received your credentials, contact us or ask your boss to do so.


## Ready-made libraries

We publish both ready-made libraries and a reference / example shop implementation at [GitHub](https://github.com/Sirumachinery). It’s all free software and open source, so feel free to use them. Contributions and pull requests are appreciated!

If you are using Magento or Woocommerce, contact us about our ready-made plugins.



# Environment

We have two environments available: production (the real live deal) and staging, which is a sandbox for you to play in. Production uses real money, staging does not, so we strongly recommend you code against the staging environment until you are ready to go live. Urls are the same for both environments. Only the endpoint changes.

Environment | Endpoint
------      | -----
Production  | https://payment.sirumobile.com
Sandbox     | https://staging.sirumobile.com

<aside class="notice">
The production environment imposes limits on how much the customer can purchase during 24h and 30 day periods to prevent misuse. The sandbox environment does not impose such limits.
</aside>

## Credentials

To connect with the API, you will need two values: merchantId, which is your merchant’s identifier, and secret, which is the secret. Contact Siru Mobile to receive your credentials. You can ask for sandbox credentials before getting the final credentials for production environment.

# Variants


## Variant 1

Payment by calling is the choice for older mobile phones (feature phones), or for regions where IP payment is not available. The customer may use either a full fledged smartphone or a desktop browser. The only features required from the phone are capability to a) receive SMS’s and b) make calls to premium rate numbers.

The customer is redirected to a web page where he is asked to call a telephone number in order to confirm his purchase. After the call is either completed, canceled or failed, customer is redirected back to the merchant’s service.

## Variant 2

IP Payment is for smart phones and is currently only available in Finland.

The flow is very quick and simple. The merchant creates a purchase via our API and the user is redirected to Siru’s payment page. User just clicks to confirm payment and we bill the customer’s mobile subscription. User is then redirected back to the merchant’s system.

This requires a mobile internet connection.

<aside class="warning">
If one opens up an insecure mobile tethering hotspot, other people can connect it and make purchases.
</aside>

## Variant 3

This is a digital wallet where customer can deposit money with various means (Siru Mobile payment gateway,
for example) and use it everywhere where the wallet is an accepted payment method.

## Variant 4

Variant 4 uses external payment gateway for each country.

Payment flow may differ for each country. Most common payment flow is where merchant creates a purchase via our API and the user is redirected to Siru payment page. User will confirm purchase and is then redirected back to merchant website. The payment confirmation will often require entering a PIN code that is automatically sent to users mobile phone.

## Variant limitations

Configuration of your production environment (which variants and features are available, how prices are configured etc.) will depend on your contracts with Siru Mobile. In sandbox, all features are available and configuration is always the same.

Variant | Limitations in sandbox | Limitation in production
------- | ------------------ | -------------------
Variant1 | <p>Countries FI, SE and NO</p> <p>Use your own mobile phone number. The numbers you are required to call are in Sweden and calling them costs only the international toll, even if the system says otherwise.</p><p>WARNING: At the moment you can not successfully call sandbox numbers from Sweden. If needed, we can provide a Skype number for testing.</p>|<p>Countries FI, SE and NO </p><p>You must use a mobile phone subscription from selected country and you can not be roaming.This variant may not be available in Norway depending on your business.</p>    
Variant2 | <p>Countries FI</p><p>To simulate a successful purchase, supply a basePrice dividable by 2 and NOT dividable by 6. To simulate failure, supply a basePrice not dividable by 2 OR dividable by 6. The difference between odd and even failing basePrices is the point of failure.</p>|<p>Countries FI</p><p>You must use a mobile connection for variant2 to work.</p>  
Variant3 | <p>Countries FI, SE, NO and GB.</p><p>When using Siru Mobile gateway to topup Wallet, variant1 limitations also apply.</p>|Countries FI, SE, NO and GB   
Variant4 | Countries GB|Countries GB  


# Payment API

> Example request

```json
{
    "merchantId":"123456789",
    "basePrice":"3.40",
    "purchaseCountry":"FI",
    "taxClass":"3",
    "customerNumber":"0501234567",
    "customerLocale":"fi_FI",
    "variant":"variant1",
    "redirectAfterSuccess":"https://shop.example.com/success",
    "redirectAfterFailure":"https://shop.example.com/fail",
    "redirectAfterCancel":"https://shop.example.com/cancel",
    "serviceGroup":"3",
    "signature":"..."
}
```

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

```html
<form method="post" action="https://payment.sirumobile.com/payment.html">
    <input type="hidden" name="basePrice" value="3.40"/>
    <input type="hidden" name="merchantId" value="123456789"/>
    <input type="hidden" name="purchaseCountry" value="FI"/>
    <input type="hidden" name="taxClass" value="3"/>
    <input type="hidden" name="serviceGroup" value="3" />
    <input type="hidden" name="variant" value="variant1" />
    <input type="hidden" name="signature" value="0b7eac5293cfffcc4c9e0259ae97e57d061ca13b0e60e60b4dde7e15b3147c105b2461e76610108b75c42ad778f2f8069525b1afd4d1a9d52a92d013ab3c0494"/>
    <input type="hidden" name="redirectAfterSuccess" value="https://shop.example.com/success" />
    <input type="hidden" name="redirectAfterFailure" value="https://shop.example.com/fail" />
    <input type="hidden" name="redirectAfterCancel" value="https://shop.example.com/cancel" />
    <div>
        Please enter your phone number:
        <input type="text" name="customerNumber" value=""/>
    </div>
    <div>
        <button type="submit">Continue</button>
    </div>
</form>
```

You have a choice of two methods to access Payment API. First and the recommended method is a modern JSON API. You POST a JSON object in request body and you receive a JSON response with payment identifier and URL where user should be redirected or an error message in case of error.

REQUEST

`POST /payment.json`

The second method is a traditional HTML form API where you construct a HTML form with certain parameters and user submits the form. This method is not recommended as it exposes more data to the end-user and prevents you from catching errors in the request.

REQUEST

`POST /payment.html`


## Fields


Due to different payment methods and regulations / conventions in different countries, the API is split into common fields and variant-specific fields .

### Common fields

Field | Required | Signed | Format | Description
---   | ---      | ---    | ---    | ---
variant|yes|yes|String|The API variant. 'variant1', 'variant2', 'variant3' or 'variant4'.
merchantId|yes|yes|Integer|The merchant identifier provided by Siru.  
submerchantReference| |yes|String|Used to identify submerchants, e.g. individual shops.This is currently purely a convenience field and may be safely ignored.
purchaseCountry|yes|yes|Country|The country where the merchant sells the item. Currently supported are 'FI', 'SE', 'NO' and 'GB'  
purchaseReference| |yes|String|The merchant's internal ID for the purchase. These will be visible in the merchant's control panel.  
customerReference|	|yes|String|The merchant’s internal ID for the customer. These will be visible in the merchant's control panel.
customerLastName|	|  |String|Customer's last name, shown in the merchant panel.  
customerFirstName|  |  |String|Customer's first name, shown in the merchant panel.  
customerEmail|  |  |String|Customer's email address, shown in the merchant panel  
customerLocale|  |  |Locale|Sets the locale of the payment page. Defaults to the default locale of purchaseCountry. Currently supported 'fi_FI', 'sv_SE', 'nn_NO' and 'en_GB'.
redirectAfterSuccess|yes  |  |  |Redirect URL 	URL where the customer will get sent after a successful payment.
redirectAfterFailure|yes|   |   |Redirect URL 	URL where the customer will get sent after an unsuccessful payment.
redirectAfterCancel|yes|  |Redirect URL|URL where the customer will get sent if he cancels the payment himself, or if the system is down for maintenance
notifyAfterSuccess|  |yes|Notification URL|URL to notify after a successful purchase.
notifyAfterFailure|  |yes|Notification URL|URL to notify after a failed purchase.
notifyAfterCancel|  |yes|Notification URL|URL to notify after a canceled purchase.
signature|yes|  |Signature|A hash calculated from select fields. See Signature calculation.


### Variant1 fields

Field | Required | Signed | Format | Description
---   | ---      | ---    | ---    | ---
basePrice|yes|yes|Money|<p>The price of the product with VAT included. Siru's fees are added to this automatically.</p><p>The prices are fixed and depend on your contract with Siru and selected country. The currency is determined by the purchaseCountry field.<p><p>In sandbox, you can use 2.50, 5.00 or 10.00 as base price.</p>
customerNumber|yes|yes|Phone number|The customer’s phone number.
taxClass|yes*|yes|Tax class|<p>The tax class number.</p><p>* Must be set if purchase country has options listed. Must be empty otherwise. See Tax classes.</p>
serviceGroup|yes*|yes|Service group|<p>The service group number.</p><p>* Must be set if purchase country has options listed. Must be empty otherwise. See Service groups.</p>

### Variant2 fields

Field | Required | Signed | Format | Description
---   | ---      | ---    | ---    | ---
basePrice|yes|yes|Money|<p>The price of the product without VAT. The actual price paid by the end customer is affected by the details of the purchase (for example VAT class in countries where VAT applies).</p><p>Price can be anything between 0.10 and 30.00.  </p>
taxClass|yes*|yes|tax class|<p>The tax class number.</p><p>* Must be set if purchase country has options listed. Must be empty otherwise. See Tax classes.</p>
serviceGroup|yes*|yes|Service group|<p>The service group number.</p><p>* Must be set if purchase country has options listed. Must be empty otherwise. See Service groups.</p>
instantPay|yes|yes|integer|Currently only supported value is 1.

### Variant3 fields

Field | Required | Signed | Format | Description
---   | ---      | ---    | ---    | ---
basePrice|yes|yes|Money|<p>The price of the product with VAT included. The actual price paid by the end customer is always exactly the same. Handling / applying any taxes appropriately is the responsibility of the merchant.</p><p>In sandbox price can be anything between 0.10 and 30.00.</p>
taxClass|no|  |tax class|The tax class number. See Tax classes.

### Variant4 fields

Field | Required | Signed | Format | Description
---   | ---      | ---    | ---    | ---
basePrice|yes||yes|Money|<p>The price of the product with VAT included.</p><p>Available prices depend on purchase country and contract with Siru.</p>
customerNumber|yes|yes|Phone number|The customer’s phone number.
taxClass|yes*|yes|Tax class|<p>The tax class number.</p><p>* Must be set if purchase country has options listed. Must be empty otherwise. See Tax classes.</p>
serviceGroup|yes*|yes|Service group|<p>The service group number.</p><p>* Must be set if purchase country has options listed. Must be empty otherwise. See Service groups.</p>
title|yes|yes|String|A short title, describing what the customer is buying.
description|no|yes|String|A longer description, describing what the customer is buying.


## Signature calculation

```php

<?php
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

?>
```

The data must be signed using a hash from certain values in data and the merchant's secret. The signature field is calculated as follows:

1.  Sort the field names of all the fields that are marked in the ‘Signed’ column in the above tables.

2.  Drop the names of fields that you left empty.

3.  Concatenate the values of those fields in that order, with a semicolon (‘;’) as a separator.

4.  Take a SHA512-HMAC with the resulting string as data and the merchant's secret as the key.

For your convenience, here is a full list of fields that need to be signed:

Field | Variants
---   | ---
variant|All
merchantId|All
submerchantReference|All
purchaseCountry|All
purchaseReference|All
customerReference|All
notifyAfterSuccess|All
notifyAfterFailure|All
notifyAfterCancel|All
basePrice|All
customerNumber|Variant1 and variant4 only
instantPay|Variant2 only
taxClass|Variant1, variant2 and variant4 only
serviceGroup|Variant1, variant2 and variant4 only
title|Variant4 only
description|Variant4 only

## Field types

**String**<br>
An UTF-8 string with a maximum length of 255 characters.

**Integer**<br>
A non-negative integer that fits into 32 bits. In the JSON API, it should be a JSON integer.

**Country**<br>
A ISO-3166-1 alpha-2 country code in upper case. Example 'FI'.

**Locale**<br>
The format is {Language code}_{Country code} i.e. like most POSIX locales. Currently the supported locales are fi_FI, sv_SE, nn_NO and en_GB (default) are supported.

**Phone number**<br>
A phone number in either MSISDN format or in national format. A leading + character and any spaces are ignored.

More information<br>
<http://en.wikipedia.org/wiki/MSISDN>

**Money**<br>
Money fields must be given as a string with a decimal point (not a comma) and two decimal places. The two
decimal places must be given even when zero. The currency is determined by the purchaseCountry field.
Examples: 13.50, 0.20, 7.15

**Redirect URL**<br>
An UTF-8 string that is a valid URL. The maximum length is 1024 characters. The validation is strict, so use
strictly valid urls like http://example.com/?variable=value instead of http://example.com?variable=value.

Following parameters are appended to the URL.

Parameter|Format|Description
---|---|---
siru_uuid|String|Siru's unique identifier for the purchase. 36 characters long.
siru_merchantId|Integer|The merchantId given in the request.
siru_submerchantReference|String|The submerchantReference given in the request.
siru_purchaseReference|String|The purchaseReference given in the request.
siru_event|String|Event name in case the merchant uses the same url for all events. One of: success, failure, cancel.
siru_signature|String|See below.

siru_signature is similar to the request's signature and you must use it to validate authenticity of the parameters. It is calculated by concatenating the values (including empty values) of siru_uuid, siru_merchantId, siru_submerchantReference, siru_purchaseReference and siru_event (in that order) with a semicolon (‘;’) as a separator and taking a SHA512-HMAC with the resulting string as data and the merchant secret as key.
If your redirect page does anything that depends on the purchase being completed successfully, then you must verify that siru_signature and siru_event are correct!
Checking siru_event is important because an attacker may attempt to direct his browser to send failure parameters to the success URL.

The customer may fail to arrive at any of the redirect URLs for various reasons, such as connection loss or user error. Relying on the notifyAfter* URLs for purchase confirmation is therefore recommended, even though it is takes some extra effort.

**Notification URL**<br>
An UTF-8 string that is a valid URL. The maximum length is 1024 characters. The validation is strict, so use strictly valid urls like <http://example.com/?variable=value> instead of <http://example.com?variable=value>.

One of the notifyAfter* URLs is called after the purchase completes. It will receive a POST request with a JSON object carrying the same parameters as the GET parameters of redirectAfter* URL. The signature is also calculated the same way and you must verify it the same way as with redirectAfter* URL!

Your server should respond to the notification with HTTP 200 code. If the notification fails, either due to a network error or with a non-200 code, then it is retried for 7 days. This time limit may change in the future.

In very rare (and theoretically unavoidable) corner cases, it is possible for a notification to be delivered more than once. Your system should be prepared to ignore a second notification, preferably with 200 code.

**Tax class**<br>
This specifies the Value Added Tax class. If your country is not listed here, leave this field empty.

Num. | Countries | VAT %
---  | ---       | ---
0    | Finland   | 0%
1    | Finland   | 10%
2    | Finland   | 14%
3    | Finland   | 24%

Tax classes may be added, changed and removed as legislation changes. Siru will notify merchants in advance of such changes. Tax class number 0 will, however, always mean a 0% VAT.

More information<br>

- <http://www.vero.fi/fi-FI/Yritys_ja_yhteisoasiakkaat/Liikkeen_ja_ammatinharjoittaja/Arvonlisaverotus> (in Finnish)<br>
- <http://www.vero.fi/en-US/Companies_and_organisations/VAT> (in English)

**Service group**<br>
This specifies the service group of the product or service. If your country is not listed here, leave this field empty.

Num. | Countries | FICORA title
---  | ---       | ---
1    | Finland   | Non-profit services
2    | Finland   | Online services
3    | Finland   | Entertainment services
4    | Finland   | Adult entertainment services

A customer's phone subscription may bar calls to certain service groups. The service groups also show up on the customer's phone bill breakdown. If multiple service groups apply, then the group with the highest number must be chosen.

The merchant is responsible for specifying the correct service group. Please contact Siru for further advice on the correct service group if you have any doubt.

The service group scheme may change as legislation changes. Siru will notify merchants in advance if any changes are required.

More information<br>

- <http://www.mapel.fi/eettiset_saannot/palveluiden_luokittelu/><br>
- <http://www.mapel.fi/yrityksille-tietoa-maksupalvelui/><br>
- <https://www.viestintavirasto.fi/attachments/maaraykset/Viestintavirasto35P2011M.pdf> (in Finnish)<br>
- <https://www.viestintavirasto.fi/en/index/puhelin/laskutusjakulujenhallinta/kulujenhallinta/estopalvelut.html> (non-authoritative)

**Signature**<br>
SHA512-HMAC hash calculated from selected field values and merchant's secret. <a href="#signature-calculation">See Signature calculation</a>.
