#Purchase Status API

The purchase status API provides information about statuses of purchases.

There are three ways to query:

*   by the merchant provided purchaseReference field
*   by the Siru-assigned UUID and
*   by a date range.

##Query by purchase reference

> Response example

```json
{
	"purchases": [
		{
			"uuid": "e53c3cfc-6d5b-4af3-ae30-7ae7812c9cf7",
			"status": "unconfirmed",
			"basePrice": "12.34",
			"finalPrice": "12.55",
			"createdAt": "2013-06-13T12:51:33+0200",
			"startedAt": "2013-06-13T12:51:58+0200",
			"finishedAt": null,
			"customerNumber": "358501234567"
		}
	]
}
```

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

REQUEST

`GET /payment/byPurchaseReference.json`

PARAMETERS

Parameter|Format|Value
--|--|--
merchantId|Integer|Your merchant ID
submerchantReference|String|Submerchant reference, if any.
purchaseReference|String|The purchase reference to query.
signature|String|Concatenate the values (including empty values) of merchantId, submerchantReference and purchaseReference (in that order), with ';' as the separator. Take the SHA512-HMAC of that with the merchant's secret as the key

The reason for returning purchases as an array is that Siru does not enforce the uniqueness of purchaseReference. If the same merchant and submerchant have multiple purchases with the same purchaseReference then they are returned in the order they were created (newest first).

Datetimes are in ISO-8601 format.

The possible values for "status" are as follows:

Status|Meaning
--|--
new|Siru has received the purchase but the customer has not yet initiated payment.
unconfirmed|The customer is undergoing phone number verification.
call_started|The customer has initiated payment by calling Siru's payment number.
confirmed|The payment is successful.
canceled|The user has chosen to cancel the payment.
failed|The purchase has failed due to an error. The merchant panel may provide more information.

The states confirmed, canceled and failed are final states. A purchase will not leave a final state.

Backwards-compatibility is guaranteed as follows: only non-final states may be added and removed.

##Query by uuid

REQUEST

`GET /payment/byUuid.json`

PARAMETERS

Parameter|Format|Value
--|--|--
merchantId|Integer|Your merchant ID
uuid|String|The UUID to query by
signature|String|Concatenate the values of merchantId and uuid (in that order), with ';' as a separator. Take the SHA512-HMAC of that with the merchant's secret as the key.

The response is the same kind as the one for purchases/byPurchaseReference.json, except the purchase object is returned directly instead of having an object and an array around it.

##Query by date range

> Response example

```json
{
	"purchases": [
		{
			"id": "1234567",
			"uuid":"f599cd21-47a2-4bbc-b44b-2cc4b1bb25f1",
			"merchantId":"18",
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

You can query a set of purchases within a specified datetime range. The date the purchases are retrieved with is the moment they were created in our database.

REQUEST

`GET /payment/byDate.json`

PARAMETERS

Parameter|Format|Value
--|--|--
merchantId|Integer|Your merchant ID
from|String|Lower date limit (UTC). Purchases with this datetime will be included in the result. Format: yyyy-mm-dd hh:ii:ss (2015-01-01 00:00:00).
to|String|Upper date limit (UTC). Purchases with this datetime will be excluded from the result. Format: yyyy-mm-dd hh:ii:ss (2015-01-01 00:00:00).
signature|String|Concatenate the values of merchantId, from and to (in that order), with ';' as a separator. Take the SHA512-HMAC of that with the merchant's secret as the key.

The response is the same kind as the one for purchases/byPurchaseReference.json. It just contains a few more fields for your leisure!
