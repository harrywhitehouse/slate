# Errors

The RBAPI API uses the following error codes:

Error Code | HTTP Status Code | Meaning
---------- | ---------------- | -------
S0001 | 422 | Schema validation API exception.
S0002 | 400 | JSON format API exception - unable to parse JSON.
X0001 | 500 | Internal server API exception - internal server error.
M0002 | 404 | Empty manifest API exception - no mailpieces found to be manifested.
V0002 | 422 | Invalid services API exception - invalid mail class & services combination.
V0001 | 422 | Invalid parameter API exception.
V0002 | 422 | Invalid service parameters API exception.
A0001 | 401 | Authentication API exception - unauthenticated or/and unauthorized.
A0002 | 422 | User status API exception.
AMS01 | 422 | Invalid address API exception.