#Price API

## Price calculation

> Response example

```json
{
	"finalCallPrice": "13.57"
}
```

> Failure response example

```json
{
	"error": "technical error message in English"
}

```

The API expects basePrice to be the price of the product. Siru will add the necessary fees to the final call price automatically. Since the exact formula for calculating the final price is somewhat intricate and subject to change, Siru provides an API for querying the final price and displaying it to customers.

REQUEST

`GET /payment/price.json`

PARAMETERS

Parameter|Format|Value
--|--|--
variant|String|variant1
merchantId|Integer|Your merchant ID
submerchantReference (optional)|String|Submerchant reference, if any.
purchaseCountry|Country|The country where the merchant sells the item.
taxClass <br>(optional*)|Tax class|The tax class number.
basePrice|Money|The price of the product with VAT included, without fees caused by the payment method.
callback <br>(optional)|String|The JSONP callback name, if you are using JSONP.

FAILURE RESPONSE EXAMPLE

An error response will have a 4xx or 5xx error code. It is not recommended to show the error message to end users.

## Get maximum base price

> Response example

```json
{
	"maxBasePrice": "39.40",
	"maxFinalCallPrice": "40.00"
}
```

> Failure response example

```json
{
	"error": "technical error message in English"
}

```

Siru imposes a limit on the maximum price of a purchase. The limit is fairly stable, so it may be acceptable to hard-code it into your application. Nevertheless, the limit can be retrieved through a JSON API.

Customers may also have individual spending limits with Siru. This API does not take those into account.

REQUEST

`GET /payment/maxPrice.json`

Parameter|Format|Value
--|--|--
variant|String|variant1
merchantId|Integer|Your merchant ID
submerchantReference<br>(optional)|String|Submerchant reference, if any
purchaseCountry|Country|The country where the merchant sells the item.
taxClass <br>(optional*)|Tax class|The tax class number.
callback <b>(optional)|String|The JSONP callback name, if you are using JSONP.

FAILURE RESPONSE EXAMPLE

An error response will have a 4xx or 5xx error code. It is not recommended to show the error message to end users.

## Javascript library

```html
<script src="https://payment.sirumobile.com/js/lib/siru.js"></script>
```

Siru provides a small JavaScript convenience library.

**Installation**<br>

Use the included example to include the library. You may copy the library to your site, but it is recommended to include it directly to obtain any future updates. Updates will be backwards-compatible.

The library depends on jQuery 1.5+. jQuery must be included before the Siru library so that the global jQuery object is available.

The library defines the global object Siru. Including it has no other effects.

**Price calculation**

Siru.getPrice(params) uses the price calculation API to fetch the final call price given a base price.

params must be an object with the following fields:

Parameter|Format|Value
--|--|--
variant|String|variant1
basePrice|Money|The price of the product with VAT included. A decimal value (with '.' as separator).
merchantId|Integer|Your merchant ID
submerchantReference<br>(optional)|String|Submerchant reference, if any.
purchaseCountry|Country|The country where the merchant sells the item.
taxClass <br>(optional*)|Tax class|The tax class number. * Must be set if purchase country has options listed. Must be empty otherwise.
success|Callback|A function that is called when the request succeeds. The price will be given as a parameter (a decimal number string).
error|Callback|A function that is called when the request fails or times out. A technical error message is given as a parameter.
timeout<br>(optional)|Integer|A timeout, in milliseconds. Defaults to 15000ms.

The function calls success or error when the request completes. It returns an object that has an abort method that cancels the request. If abort is used then neither success nor error will be called.

**Get maximum base price**

Siru.getMaxBasePrice(params) uses the maximum price API to fetch the maximum base price that Siru will accept.

params must be an object with the following fields.

Parameter|Format|Value
--|--|--
variant|String|variant1
basePrice|Money|The price of the product with VAT included. A decimal value (with '.' as separator)
merchantId|Integer|Your merchant ID
submerchantReference<br> (optional)|String|Submerchant reference, if any.
purchaseCountry|Country|The country where the merchant sells the item.
taxClass <br>(optional*)|Tax class|The tax class number. * Must be set if purchase country has options listed. Must be empty otherwise.
success|Callback|A function that is called when the request succeeds. The price will be given as a parameter (a decimal number string).
error|Callback|A function that is called when the request fails or times out. A technical error message is given as a parameter.
timeout <br>(optional)|Integer	A timeout, in milliseconds. Defaults to 15000ms.

The function calls success or error when the request completes. It returns an object that has an abort method that cancels the request. If abort is used then neither success nor error will be called.
