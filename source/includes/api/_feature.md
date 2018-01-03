# Feature detection API

> Response example

```json
{
	"ipPaymentsEnabled": true
}

```

The Feature detection API can be used to verify if IP Payments (variant2) are available for given IP-address.

REQUEST

`GET /payment/ip/feature-check`

PARAMETERS

Parameter|Format|Value
--|--|--
merchantId|Integer|Your merchant ID
ip|String|	The IP address of someone
signature|String|Concatenate the values (including empty values) of merchantId and ip (in that order), with ';' as the separator. Take the SHA512- HMAC of that with the merchant's secret as the key.
