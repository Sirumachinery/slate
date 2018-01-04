---
title: Siru Mobile API reference

language_tabs: # must be one of https://git.io/vQNgJ
  - html: HTML
  - php: PHP


toc_footers:
  - <a href='https://sirumobile.com'>Siru Mobile</a>

includes:
  - api/payment
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

Environment | Endpoint
------      | -----
Production  | https://payment.sirumobile.com
Sandbox     | https://staging.sirumobile.com

<aside class="notice">
    We will provide you with separate credentials to production environment. You can not use sandbox credentials in production!
</aside>

<aside class="notice">
    The production environment imposes limits on how much the user can spend in a given time period to prevent misuse. The sandbox environment does not impose such limits.
</aside>

# Authentication

Requests and responses are authenticated by a signature created using request values and merchant secret we have provided.

Contact Siru Mobile to receive your credentials. We will first provide credentials to Sandbox environment and when you are ready to go live, we will provide you with another set of credentials for production. You may receive separate credentials for each country where you are integrating.

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

