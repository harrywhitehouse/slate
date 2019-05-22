# Errors

The RBAPI API uses the following error codes:

Error Code | HTTP Status Code | Meaning
---------- | ---------------- | -------
S0001 | 422 | Failed schema validation
S0002 | 400 | JSON format - unable to parse JSON
X0001 | 500 | Internal server error
M0002 | 404 | Empty manifest - no mailpieces found to be manifested
V0001 | 422 | Invalid parameter
V0002 | 422 | Invalid service parameters
V0004 | 422 | UPS error
V0005 | 422 | FedEx error
V0006 | 422 | GSS error
V0007 | 400 | Phone already confirmed
V0007 | 422 | Account balance insufficient funds
V0008 | 400 | Phone confirmation failed
V0009 | 422 | No phone number
A0001 | 401 | Authentication error - unauthenticated or/and unauthorized
A0002 | 422 | User status
AVS01 | 422 | Invalid address
AVS02 | 422 | Invalid zip code
AVS03 | 422 | Invalid state
AVS04 | 422 | Invalid city
AVS05 | 422 | Address not found
AVS06 | 422 | Multiple addresses found
AVS07 | 422 | Invalid street number
