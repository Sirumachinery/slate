# API operational status

REQUEST

`GET /status`

PARAMETERS

None

The response HTTP status code will tell you the operational status of the API.

HTTP status code|Explained
--|--
200|Siru Mobile is up and all should be well.
503|Siru Mobile is temporarily down for maintenance. Usually this doesn't last longer than a few minutes.
500|There is a problem with Siru Mobile API. You should never get this response from status query.

# Maintenance

Occasionally we will do maintenance on Siru Mobile API which may require a temporary down time. Don't worry: we handle all your customers' ongoing purchases gracefully and (if the need for maintenance isn't surprising or urgent) you should also know about all our maintencance windows in advance.
